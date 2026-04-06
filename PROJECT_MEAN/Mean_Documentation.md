# MEAN Stack Deployment Documentation

## Introduction
The MEAN stack is a powerful JavaScript-based framework used for building scalable web applications. It consists of:
- **MongoDB**: A NoSQL document database.
- **Express.js**: A web application framework for Node.js.
- **AngularJS**: A frontend framework for dynamic views.
- **Node.js**: A JavaScript runtime environment.

---
## Step 0: Prerequisites

1.  **EC2 Instance**: Ubuntu 22.04 LTS instance (t3.small) launched in the us-east-1 region.
2.  **Access Control**: Security Group configured to allow:
    *   SSH (Port 22)
    *   HTTP (Port 80)
    *   HTTPS (Port 443)
    *   Custom TCP (Port 3300) for the application.
3.  **SSH Connection**:
    ```bash
    chmod 400 my-key.pem
    ssh -i "my-key.pem" ubuntu@44.244.4.195
    ```
