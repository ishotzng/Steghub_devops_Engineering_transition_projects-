# WEB STACK IMPLEMENTATION (MERN STACK) ON AWS

## Introduction

The **MERN** stack is a modern, high-performance, full-stack JavaScript framework used for building dynamic and scalable web applications. This guide provides a comprehensive walkthrough for deploying and configuring the four core components on an **AWS** infrastructure:

* **MongoDB**: M  
* **Express.js**: E  
* **React.js**: R  
* **Node.js**: N  

| Component | Technology | Role |
| :--- | :--- | :--- |
| **Database** | [MongoDB](https://www.mongodb.com) | NoSQL database for storing application data |
| **Backend Framework** | [Express.js](https://expressjs.com) | Handles server logic and API routing |
| **Frontend Library** | [React.js](https://react.dev) | Builds interactive user interfaces |
| **Runtime Environment** | [Node.js](https://nodejs.org) | Executes JavaScript on the server |

---

## PREREQUISITES

- AWS EC2 instance (t3.small Ubuntu 24.04)
- SSH key pair
- Security group with ports:
  - 22, 80, 443, 3000, 5000
  
<img width="1886" height="784" alt="Screenshot 2026-04-02 151222" src="https://github.com/user-attachments/assets/7ac1cf0f-f9d4-4c89-9712-630290009377" />

- <img width="1875" height="791" alt="Screenshot 2026-04-02 151550" src="https://github.com/user-attachments/assets/cb359719-c818-4159-b9f3-1df01cfcdf83" />
<img width="1875" height="756" alt="Screenshot 2026-04-02 151427" src="https://github.com/user-attachments/assets/6db1cda3-047c-4df5-82a6-53eb6bebc3ad" />



```bash
chmod 400 my-ec2-key.pem
ssh -i ""my key.pem" ubuntu@ec2-44-243-84-48.us-west-2.compute.amazonaws.com
```
<img width="1367" height="587" alt="Screenshot 2026-04-02 151821" src="https://github.com/user-attachments/assets/fe7a07a1-2a98-41fd-be01-e13fb4f2108a" />
<img width="1541" height="833" alt="Screenshot 2026-04-02 151904" src="https://github.com/user-attachments/assets/1a41ac13-5089-4837-9a23-b0023a628803" />

---

### BACKEND SETUP
## Step 1: Backend Configuration (Node.js + Express Setup)
# 1. System Update
Before installing dependencies, the system packages were updated to ensure stability and access to latest security patches.


```bash
sudo apt update && sudo apt upgrade -y
```
<img width="912" height="742" alt="Screenshot 2026-04-02 152107" src="https://github.com/user-attachments/assets/5117f2a6-7843-490b-9f08-86019363a53a" />
<img width="902" height="697" alt="Screenshot 2026-04-02 152052" src="https://github.com/user-attachments/assets/9ca7dfeb-d5a3-4f49-8f70-57aa80a6ee73" />
<img width="907" height="724" alt="Screenshot 2026-04-02 152000" src="https://github.com/user-attachments/assets/353b4962-e4ca-408a-89f8-4cdef2fbaff3" />
<br>
<br>


## Step 2 Add Node.js Repository
To install Node.js (v18), the official NodeSource repository was added:

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install nodejs -y

```
<img width="943" height="616" alt="Screenshot 2026-04-02 152430" src="https://github.com/user-attachments/assets/c2144d07-eefa-41ad-b5e6-1754d74a3534" />
<img width="921" height="740" alt="Screenshot 2026-04-02 152452" src="https://github.com/user-attachments/assets/804d5007-2346-4b04-a56c-380a4d1618ab" />

<br>
<br>
<br>

## Step 3: Verify Installation

To confirm successful installation:

```bash
node -v
npm -v
```
<img width="475" height="263" alt="Screenshot 2026-04-02 152735" src="https://github.com/user-attachments/assets/5b6f3f19-5457-4533-90ec-7fa57fdbd4ed" />

---

# Application Setup (Backend Project Initialization)

### 1. Create Project Directory
A dedicated folder was created for the To-Do application:

```bash
mkdir Todo && cd Todo
npm init -y
npm install express dotenv mongoose body-parser
```
This initializes a Node.js project and generates a package.json file, which manages dependencies and scripts.
---
### 2. Backend Framework Setup (Express.js)
# 1. Install Express.js

```bash
npm install express
```
Express simplifies API creation and routing in Node.js.

### 2. Create Server File
```bash
touch index.js
```
This file serves as the main entry point for the backend server.

### 3. Install Environment Manager

```bash
npm install dotenv
```
This allows secure storage of configuration variables such as database credentials and ports.

### 4. Configure Express Server

The backend server was configured in index.js:
Handles HTTP requests
Enables CORS access
Defines API entry point
Starts server on port 5000

This establishes the foundation of the backend API service.







## CONCLUSION

This project demonstrates a full MERN stack deployment on AWS using:
- Node.js + Express backend
- MongoDB database
- React frontend
- REST API integration
```  
