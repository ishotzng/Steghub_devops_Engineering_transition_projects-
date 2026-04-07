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

    
### 3. Security Group Rules

The following inbound rules were configured to ensure secure access and proper functionality of the application:


| Protocol | Port | Source | Description |
| :--- | :--- | :--- | :--- |
| **SSH** | 22 | `0.0.0.0/0` | Remote access via Terminal |
| **HTTP** | 80 | `0.0.0.0/0` | Standard web traffic |
| **HTTPS** | 443 | `0.0.0.0/0` | Secure web traffic (SSL/TLS) |
| **Custom TCP** | 3300 | `0.0.0.0/0` | Node.js Book Application Port |

> **Note**: While `0.0.0.0/0` allows access from anywhere, for production environments, it is recommended to restrict Port 22 to your specific IP address for better security.


<img width="1560" height="318" alt="Screenshot 2026-04-06 162420" src="https://github.com/user-attachments/assets/40e8af51-5ced-440e-843e-cbbff7bfc921" />

## Step 1: Install Node.js
 
Node.js is a JavaScript runtime built on Chrome's V8 engine. It powers the backend of this application by running the Express server and handling all API routes.
 
### Update & Upgrade Ubuntu
 
```bash
sudo apt update
sudo apt upgrade -y
```
<img width="960" height="422" alt="Screenshot 2026-04-06 150919" src="https://github.com/user-attachments/assets/49548c53-eb64-4826-b97e-afbc14c62ba2" />

---
### Add Required Certificates
 
```bash
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates
```
<img width="950" height="943" alt="Screenshot 2026-04-06 151502" src="https://github.com/user-attachments/assets/b90e8339-f0dc-481b-b4f2-3bb6bf01c716" />

---
 
### Add NodeSource Repository
 
```bash
curl -sL https://deb.nodesource.com/setup_22.x | sudo -E bash -
```
<img width="950" height="943" alt="Screenshot 2026-04-06 151502" src="https://github.com/user-attachments/assets/f217eedd-2050-4be6-8cec-99d9d0b7207d" />

---
### Install Node.js
 
```bash
sudo apt install -y nodejs
```
<img width="1146" height="921" alt="Screenshot 2026-04-06 151616" src="https://github.com/user-attachments/assets/05863f93-b324-4ae4-a097-740a3523da16" />

---
 
### Verify Installation
 
```bash
node -v
npm -v
```
 <img width="849" height="268" alt="Screenshot 2026-04-06 151650" src="https://github.com/user-attachments/assets/ca2cf9ba-b424-473c-8f8e-9fdeb6cba937" />
<img width="847" height="258" alt="Screenshot 2026-04-06 153716" src="https://github.com/user-attachments/assets/86efd7d6-b1dc-49ac-ae8d-bfb38b546a8b" />

--- 
## Step 2: Install MongoDB
 
MongoDB is the database layer of this application. It stores book records in flexible, JSON-like documents. Each record holds the book name, ISBN, author, and number of pages.
 
### Import MongoDB GPG Key
 
```bash
sudo apt-get install -y gnupg curl
 
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc \
  | sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor
```
<img width="934" height="383" alt="Screenshot 2026-04-06 152848" src="https://github.com/user-attachments/assets/7e2cfee4-3679-4e4c-8f14-ae6f6cf1f0fd" />

---
### Add MongoDB Repository
 
```bash
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] \
https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" \
| sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```
<img width="934" height="383" alt="Screenshot 2026-04-06 152848" src="https://github.com/user-attachments/assets/40042319-e145-482d-b846-85336dbaa53b" />

---

### Install MongoDB
 
```bash
sudo apt-get update
sudo apt-get install -y mongodb-org
```
<img width="942" height="699" alt="Screenshot 2026-04-06 153015" src="https://github.com/user-attachments/assets/292f8740-6d80-409c-b944-2f2748cab06e" />
<img width="954" height="538" alt="Screenshot 2026-04-06 152957" src="https://github.com/user-attachments/assets/519ae3ad-fff5-4a86-ad7d-e5704c10cd28" />

---
### Start & Enable MongoDB
 
```bash
sudo systemctl start mongod
sudo systemctl enable mongod
```
<img width="918" height="513" alt="Screenshot 2026-04-06 153810" src="https://github.com/user-attachments/assets/3d0b30a4-e48b-43c1-b20b-af442e34bf13" />

---
 
### Verify MongoDB is Running
 
```bash
sudo systemctl status mongod
```
 <img width="918" height="513" alt="Screenshot 2026-04-06 153810" src="https://github.com/user-attachments/assets/74af4cb6-95a9-408e-a12d-aad1bb61c3bd" />

---
 
## Step 3: Install Express and Set Up Server Routes
 
Express is the web framework that handles HTTP requests and connects the frontend to MongoDB. Mongoose provides a schema-based model to define and validate the structure of data stored in MongoDB. `body-parser` processes incoming JSON request bodies before they reach the route handlers.
 
### Install npm
 
```bash
sudo apt install -y npm
```
<img width="860" height="360" alt="Screenshot 2026-04-06 154151" src="https://github.com/user-attachments/assets/7bf14c01-dc8a-4e1a-b530-5802dc79f307" />

---
### Create Project Folder and Initialize npm
 
```bash
mkdir Books && cd Books
npm init -y
npm install express mongoose body-parser
```
<img width="909" height="425" alt="Screenshot 2026-04-06 154217" src="https://github.com/user-attachments/assets/546a9c8d-1c21-414f-aaf9-7772ab5f8554" />
<img width="951" height="950" alt="Screenshot 2026-04-06 154423" src="https://github.com/user-attachments/assets/e9721af8-ab4d-4f24-9fb5-22129e9b4c64" />


---
### Create `server.js`
 
The entry point of the application. It connects to MongoDB, loads middleware, mounts the routes, and starts listening on port 3300.
 
```bash
vi server.js
```
<img width="898" height="308" alt="Screenshot 2026-04-06 154448" src="https://github.com/user-attachments/assets/58b5915b-0611-43c1-bf0e-6dd5a31b62d2" />

---
 
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const path = require('path');
 
const app = express();
const PORT = process.env.PORT || 3300;
 
mongoose.connect('mongodb://localhost:27017/test', {})
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.error('MongoDB connection error:', err));
 
app.use(express.static(path.join(__dirname, 'public')));
app.use(bodyParser.json());
 
require('./apps/routes')(app);
 
app.listen(PORT, () => {
  console.log(`Server up: http://localhost:${PORT}`);
});
```
<img width="936" height="450" alt="Screenshot 2026-04-06 154615" src="https://github.com/user-attachments/assets/713975f7-7b67-4767-9dba-db7c48b6367b" />
<img width="948" height="733" alt="Screenshot 2026-04-06 154509" src="https://github.com/user-attachments/assets/ef0be3f9-0609-422b-aa53-e054a7078403" />

---
### Create the `apps` Folder and `routes.js`
 
Each route maps an HTTP method to a database operation: GET fetches all books, POST adds a new one, and DELETE removes a book by its ISBN.
 
```bash
mkdir apps && cd apps
vi routes.js
```
<img width="927" height="503" alt="Screenshot 2026-04-06 154827" src="https://github.com/user-attachments/assets/1e8a9b08-2fc2-4fda-a059-05722392f883" />
<img width="930" height="394" alt="Screenshot 2026-04-06 154655" src="https://github.com/user-attachments/assets/e97da6c9-96f6-4a52-9c1a-2d0a2dd1c3bd" />


---
 
```javascript
const Book = require('./models/book');
const path = require('path');
 
module.exports = function(app) {
 
  app.get('/book', async (req, res) => {
    try {
      const books = await Book.find();
      res.json(books);
    } catch (err) {
      res.status(500).json({ message: 'Error fetching books', error: err.message });
    }
  });
 
  app.post('/book', async (req, res) => {
    try {
      const book = new Book({
        name: req.body.name,
        isbn: req.body.isbn,
        author: req.body.author,
        pages: req.body.pages
      });
      const savedBook = await book.save();
      res.status(201).json({ message: 'Successfully added book', book: savedBook });
    } catch (err) {
      res.status(400).json({ message: 'Error adding book', error: err.message });
    }
  });
 
  app.delete('/book/:isbn', async (req, res) => {
    try {
      const result = await Book.findOneAndDelete({ isbn: req.params.isbn });
      if (!result) {
        return res.status(404).json({ message: 'Book not found' });
      }
      res.json({ message: 'Successfully deleted the book', book: result });
    } catch (err) {
      res.status(500).json({ message: 'Error deleting book', error: err.message });
    }
  });
 
  app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname, '../public', 'index.html'));
  });
 
};
```
<img width="950" height="970" alt="Screenshot 2026-04-06 154808" src="https://github.com/user-attachments/assets/2e5eea8f-a414-481f-8960-5d9ba49a8059" />

---
 
### Create the `models` Folder and `book.js`
 
The Mongoose schema enforces data types and required fields on every book document written to MongoDB.
 
```bash
mkdir models && cd models
vi book.js
```
<img width="909" height="319" alt="Screenshot 2026-04-06 154906" src="https://github.com/user-attachments/assets/cee860b5-d331-4f91-b9ad-c47f61c7b634" />

---
 
```javascript
const mongoose = require('mongoose');
 
const bookSchema = new mongoose.Schema({
  name:   { type: String, required: true },
  isbn:   { type: String, required: true, unique: true, index: true },
  author: { type: String, required: true },
  pages:  { type: Number, required: true, min: 1 }
}, { timestamps: true });
 
module.exports = mongoose.model('Book', bookSchema);
```
 <img width="930" height="513" alt="Screenshot 2026-04-06 154929" src="https://github.com/user-attachments/assets/a1a172bb-d8da-4413-9bd4-038316e84e1f" />

---
 
## Step 4: Access the Routes with AngularJS
 
AngularJS is the frontend framework that connects the browser to the Express API. It handles user input, sends HTTP requests to the backend, and dynamically updates the book list on the page without full reloads.
 
### Navigate Back to the `Books` Root and Create the `public` Folder
 
```bash
cd ../..
mkdir public && cd public
```
<img width="918" height="307" alt="Screenshot 2026-04-06 155026" src="https://github.com/user-attachments/assets/e174f26d-4498-450b-808b-a657f6e7b354" />

---
### Create `script.js`
 
This AngularJS controller manages three actions: loading all books on page load, posting a new book from the form, and deleting a book by ISBN — all via the `$http` service.
 
```bash
vi script.js
```
<img width="918" height="307" alt="Screenshot 2026-04-06 155026" src="https://github.com/user-attachments/assets/acf1df3d-004e-49fe-b506-4b7175ba9858" />

---
 
```javascript
angular.module('myApp', [])
  .controller('myCtrl', function($scope, $http) {
 
    function fetchBooks() {
      $http.get('/book')
        .then(response => {
          $scope.books = response.data;
        })
        .catch(error => {
          console.error('Error fetching books:', error);
        });
    }
 
    fetchBooks();
 
    $scope.del_book = function(book) {
      $http.delete(`/book/${book.isbn}`)
        .then(() => {
          fetchBooks();
        })
        .catch(error => {
          console.error('Error deleting book:', error);
        });
    };
 
    $scope.add_book = function() {
      const newBook = {
        name: $scope.Name,
        isbn: $scope.Isbn,
        author: $scope.Author,
        pages: $scope.Pages
      };
 
      $http.post('/book', newBook)
        .then(() => {
          fetchBooks();
          $scope.Name = $scope.Isbn = $scope.Author = $scope.Pages = '';
        })
        .catch(error => {
          console.error('Error adding book:', error);
        });
    };
 
  });
```
<img width="823" height="897" alt="Screenshot 2026-04-06 155101" src="https://github.com/user-attachments/assets/d1e268f0-36f5-42ae-b2fc-2d650f883a1e" />


 
### Create `index.html`
 
The single-page frontend. It loads AngularJS from a CDN, binds the form fields to the controller scope, and renders the live book list in a styled table.
 
```bash
vi index.html
```
<img width="909" height="319" alt="Screenshot 2026-04-06 154906" src="https://github.com/user-attachments/assets/1f60e618-deae-42aa-9a04-03992db9349b" />

---
 
```html
<!DOCTYPE html>
<html ng-app="myApp" ng-controller="myCtrl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Book Management</title>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.8.2/angular.min.js"></script>
  <script src="script.js"></script>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    table { border-collapse: collapse; width: 100%; }
    th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
    th { background-color: #f2f2f2; }
    input[type="text"], input[type="number"] { width: 100%; padding: 5px; }
    button { margin-top: 10px; padding: 5px 10px; }
  </style>
</head>
<body>
  <h1>Book Management</h1>
 
  <h2>Add New Book</h2>
  <form ng-submit="add_book()">
    <table>
      <tr>
        <td>Name:</td>
        <td><input type="text" ng-model="Name" required></td>
      </tr>
      <tr>
        <td>ISBN:</td>
        <td><input type="text" ng-model="Isbn" required></td>
      </tr>
      <tr>
        <td>Author:</td>
        <td><input type="text" ng-model="Author" required></td>
      </tr>
      <tr>
        <td>Pages:</td>
        <td><input type="number" ng-model="Pages" required></td>
      </tr>
    </table>
    <button type="submit">Add Book</button>
  </form>
 
  <h2>Book List</h2>
  <table>
    <thead>
      <tr>
        <th>Name</th>
        <th>ISBN</th>
        <th>Author</th>
        <th>Pages</th>
        <th>Action</th>
      </tr>
    </thead>
    <tbody>
      <tr ng-repeat="book in books">
        <td>{{book.name}}</td>
        <td>{{book.isbn}}</td>
        <td>{{book.author}}</td>
        <td>{{book.pages}}</td>
        <td><button ng-click="del_book(book)">Delete</button></td>
      </tr>
    </tbody>
  </table>
</body>
</html>
```
 <img width="947" height="914" alt="Screenshot 2026-04-06 155208" src="https://github.com/user-attachments/assets/bdd37343-6781-4b46-b542-734f37768fbf" />

---
 
## Step 5: Start the Application
 
Navigate back to the `Books` root and start the Node.js server.
 
```bash
cd ..
node server.js
```
<img width="935" height="386" alt="Screenshot 2026-04-06 155246" src="https://github.com/user-attachments/assets/80c2c034-70f0-4cf0-bb80-1e654a0761ea" />

---
 
Expected output:
 
```
MongoDB connected
Server up: http://localhost:3300
```
<img width="863" height="424" alt="Screenshot 2026-04-06 161314" src="https://github.com/user-attachments/assets/bf9bb22c-1a7a-4c2c-b956-3eecab6c3339" />

---
 
### Test Locally
 
```bash
curl -s http://localhost:3300
```
 
### Access the App in Your Browser
 
```
http://44.244.4.195:3300
```
 <img width="1734" height="842" alt="Screenshot 2026-04-06 163333" src="https://github.com/user-attachments/assets/d27b40a5-d4d9-4f92-a46b-7c4a83e1763f" />
<img width="1857" height="951" alt="Screenshot 2026-04-06 162743" src="https://github.com/user-attachments/assets/0854bbba-c245-451b-9150-5606a97a1ff1" />
<img width="1882" height="886" alt="Screenshot 2026-04-06 162523" src="https://github.com/user-attachments/assets/61c4fbb5-a789-4a0d-8bd0-28371d13feaa" />

---
 
## Final Project Structure
 
```
Books/
├── server.js
├── package.json
├── node_modules/
├── public/
│   ├── index.html
│   └── script.js
└── apps/
    ├── routes.js
    └── models/
        └── book.js
```
 
 
 The MEAN Stack Book Register Application is live!
---

# Conclusion

The **MEAN stack** comprising MongoDB, Express.js, AngularJS, and Node.js—provides a robust and cohesive framework for developing modern, scalable web applications. By utilizing JavaScript across the entire stack, developers can maintain a unified codebase, significantly streamlining the development lifecycle from the front-end interface to the back-end database. This architectural synergy ensures high performance and flexibility, making it an ideal choice for building dynamic, data-driven applications.



