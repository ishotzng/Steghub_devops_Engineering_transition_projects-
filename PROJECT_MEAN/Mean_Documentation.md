# WEB STACK IMPLEMENTATION (MEAN STACK) ON AWS

## Introduction

The **MEAN** stack is a powerful, full-stack JavaScript framework used for building dynamic web applications. This guide provides a comprehensive walkthrough for deploying and configuring the four essential components on an **AWS** infrastructure:

*   **MongoDB**: M (NoSQL Database)
*   **Express.js**: E (Back-end Framework)
*   **AngularJS**: A (Front-end Framework)
*   **Node.js**: N (JavaScript Runtime)


| Component | Technology | Role |
| :--- | :--- | :--- |
| **Database** | [MongoDB](https://mongodb.com) | Document-based data storage |
| **Back-end Framework** | [Express.js](https://expressjs.com) | Web application framework for Node.js |
| **Front-end Framework** | [AngularJS](https://angularjs.org) | Client-side dynamic interface |
| **Runtime Environment** | [Node.js](https://nodejs.org) | Server-side JavaScript execution |

---

## Step 0: Prerequisites


### 1.  **EC2 Instance**: Launch an Ubuntu 22.04 LTS instance (t3.small recommended) on AWS.
---
<img width="1891" height="753" alt="Screenshot 2026-04-06 150117" src="https://github.com/user-attachments/assets/122c5fb8-7afb-4b78-8788-190cc6b02aa6" />
<img width="1612" height="641" alt="Screenshot 2026-04-06 150213" src="https://github.com/user-attachments/assets/957b6500-ef3e-4ca0-84de-69c2509516ac" />

---

### 2.   **SSH Access**: Secure your instance using your private key:


    ```bash
    chmod 400 "my-key.pem"
    ssh -i "my-key.pem" ubuntu@44.244.4.195
    ```
   
<img width="934" height="930" alt="Screenshot 2026-04-06 150551" src="https://github.com/user-attachments/assets/54f68c6b-2385-463a-802d-3dfd2e1d086e" />

---

    
### 3.  **Security Group Rules**:
    *   **Port 22**: SSH access.
    *   **Port 80**: HTTP traffic.
    *   **Port 443**: HTTPS traffic.
    *   **Port 3300**: Custom TCP for the Book Application.

<img width="1560" height="318" alt="Screenshot 2026-04-06 162420" src="https://github.com/user-attachments/assets/40e8af51-5ced-440e-843e-cbbff7bfc921" />
