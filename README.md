# Java Application Deployment with Reverse Proxy on AWS

This project demonstrates the secure deployment of a Java-based **Student Registration Web Application** on **Amazon Web Services (AWS)** using a reverse proxy setup. It integrates key AWS services (EC2 and RDS) and open-source tools (Apache Tomcat, Nginx, MySQL) to build a scalable, secure, and maintainable architecture.

## 📌 Project Objectives

- Deploy a Java `.war` application on an EC2 instance with Apache Tomcat.
- Launch a MySQL-compatible Amazon RDS instance to store student registration data.
- Configure a reverse proxy using Apache HTTPD or Nginx on a second EC2 instance.
- Ensure the application is only publicly accessible through the reverse proxy.
- Establish secure communication between application, proxy, and database layers.

---

## 🛠️ Technology Stack

| Layer               | Tool/Service              |
|---------------------|---------------------------|
| Operating System    | Amazon Linux 2            |
| Application Server  | Apache Tomcat             |
| Reverse Proxy       | Nginx or Apache HTTPD     |
| Database            | Amazon RDS (MySQL)        |
| Java Connector      | MySQL Connector/J         |
| Cloud Infrastructure| AWS EC2, AWS RDS          |

---

## 🔐 Security Architecture

- **EC2-Backend**: Accepts traffic only from EC2-Proxy’s private IP.
- **EC2-Proxy**: Accepts HTTP traffic from the internet (port 80 only).
- **Amazon RDS**: Only allows connections from EC2-Backend.

---

## 📊 System Architecture

                    +-------------+
                    |    User     |
                    +-------------+
                          |
                          v
                 Public Access (Port 80)
                          |
                    +-------------+
                    | EC2-Proxy   |  (Nginx / Apache HTTPD)
                    | (Public IP) |
                    +-------------+
                          |
             Private Connection (Port 8080)
                          |
                    +--------------+
                    | EC2-Backend  |  (Apache Tomcat)
                    | (Private IP) |
                    +--------------+
                          |
             Private Connection (MySQL JDBC)
                          |
                    +-------------+
                    |    RDS      |  (MySQL-Compatible)
                    +-------------+



- **EC2-Proxy**: Publicly accessible Nginx reverse proxy
- **EC2-Backend**: Hosts Tomcat and the deployed Java application
- **Amazon RDS**: Stores student registration data

---

## ⚙️ Implementation Steps

### 1. Infrastructure Setup
- Launch two EC2 instances on Amazon Linux 2:
  - **ec2-backend** (private)
  - **ec2-proxy** (public)
- Launch an **Amazon RDS (MySQL)** instance.

### 2. Application Deployment
- Install Java and Apache Tomcat on **ec2-backend**.
- Deploy the `student.war` file to `webapps/`.
- Verify application: `http://<backend-private-ip>:8080/student`.

### 3. Database Setup
- Create a MySQL schema and `students` table:

  CREATE TABLE students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    course VARCHAR(50)
  );
4. Database Connector
Download mysql-connector.jar.

Place it into Tomcat’s lib/ directory.

Restart Tomcat for JDBC connectivity.

5. Reverse Proxy Configuration
Install Nginx on ec2-proxy.

Configure Nginx:


server {
    listen 80;
    location /student {
        proxy_pass http://<backend-private-ip>:8080/student;
    }
}
6. Access Setup
Configure security groups:

EC2-Backend: only accessible by EC2-Proxy.

RDS: only accessible by EC2-Backend.

EC2-Proxy: publicly accessible (port 80).

Application accessible only at: http://<proxy-public-ip>/student.

✅ Results
Java web app successfully deployed on Tomcat.

User access is securely routed via Nginx reverse proxy.

Student data is inserted and verified in Amazon RDS.

🧩 Challenges Faced
Security group misconfiguration causing blocked access.

JDBC connectivity issues, resolved with proper MySQL connector and VPC settings.

🧠 Conclusion
This project showcases best practices for cloud-based Java application deployment using AWS, ensuring:

Security through controlled access via reverse proxy.

Scalability using cloud-native services.

Maintainability with modular architecture.
