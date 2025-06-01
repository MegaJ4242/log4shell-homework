# SEAS 8405 – Homework 9: Log4Shell Exploit & Defense

This repository contains the files and documentation for Homework 9 of SEAS 8405: Cyber Architecture. The assignment focused on simulating a Log4Shell (CVE-2021-44228) exploit in a controlled environment using Docker and Java, and applying the MITRE ATT&CK, DEFEND, and REACT frameworks.

---

## 📁 Folder Structure

```
log4shell-homework/
├── src/                         # Java source files for Spring Boot vulnerable app
│   └── main/java/com/example/
│       ├── Log4shellDemoApplication.java
│       └── LogController.java
├── Dockerfile                   # Builds the vulnerable app container
├── docker-compose.yml          # Orchestrates the container
├── pom.xml                     # Maven project config with vulnerable Log4j version
├── exploit-server/             # Contains Exploit.java and .class file for RCE simulation
├── marshalsec/                 # LDAP exploit server (jar built from official repo)
├── deliverables/
│   ├── HW9_Partial_Exploit_Report.pdf
│   └── screen_recording.mp4
```

---

## 🧪 Summary of Attempted Exploit

- A Spring Boot app using Log4j 2.14.1 was deployed.
- A simulated exploit payload (`${jndi:ldap://...}`) was sent to a `/log` endpoint that logged user input.
- A Python HTTP server was configured to serve a malicious `.class` file, and marshalsec was set up to act as the LDAP server.

### ❌ Exploit Outcome

- The payload was **successfully received and logged** by the app.
- However, the payload was **not resolved/executed**:
  - The LDAP server (`marshalsec`) received no connection.
  - The malicious `.class` file was never requested.
- This incomplete execution was likely due to:
  - Spring Boot’s runtime protections
  - The absence of a `-Dlog4j2.formatMsgNoLookups=false` override
  - The flawed starter code provided in the assignment
  - The assignment’s broken Python LDAP example (which is not a functioning server)

---

## 🔐 Defense Implementation

- Upgraded Log4j to version 2.17.0
- Added input validation to block JNDI patterns in the `/log` route
- Rebuilt and redeployed app to test patch effectiveness

---

## 📄 Deliverables

- ✅ [Final PDF Report](./deliverables/HW9_Partial_Exploit_Report.pdf)
- 🎥 Screen recording demonstrating environment setup, payload attempt, and mitigation
- 💬 Honest documentation of blockers and what worked

---

## 💬 Reflections

This assignment was challenging due to inconsistencies in the provided instructions, broken LDAP setup, and flawed sample code that lacked a `main()` method and necessary dependencies. A working exploit flow was ultimately assembled using a custom-built marshalsec JAR and verified `.class` loader chain, though the final execution was blocked by runtime mitigations. The exercise still provided valuable insight into how real-world Log4Shell attacks are chained and mitigated.

---

## 🛠️ To Run

```bash
docker-compose up --build
```

Use curl or Postman to send test inputs to:
```
http://localhost:8080/log
```

---

Meg Thomas  
Spring 2025  
SEAS 8405: Cyber Architecture
