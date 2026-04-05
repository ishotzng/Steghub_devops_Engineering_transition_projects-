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
node -v  // Gives the node version
npm -v    // Gives the node package manager version
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
mkdir Todo
ls
cd Todo
```
 
Initialise the project (creates `package.json`):
 
```bash
npm init
```
 
Follow the prompts and press **Enter** to accept defaults. Type `yes` when asked to write the `package.json` file.
 
```bash
ls   # confirm package.json was created
```
 
---
 
### Install ExpressJS
 
```bash
npm install express
```
 
Create the entry point file:
 
```bash
touch index.js
ls   # confirm index.js was created
```
 
Install the dotenv module:
 
```bash
npm install dotenv
```
 
Open and populate `index.js`:
 
```bash
vim index.js
```
 
Paste the following code:
 
```javascript
const express = require('express');
require('dotenv').config();
 
const app = express();
const port = process.env.PORT || 5000;
 
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});
 
app.use((req, res, next) => {
  res.send('Welcome to Express');
});
 
app.listen(port, () => {
  console.log(`Server running on port ${port}`)
});
```
 
> Save with `:w` and exit with `:qa` in vim.
 
Start the server:
 
```bash
node index.js
```
 
You should see: `Server running on port 5000`
 
> **AWS Note:** Open TCP port **5000** in your EC2 Security Group inbound rules.
 
Access in browser:
 
```
http://<PublicIP-or-PublicDNS>:5000
```
 
To get your server's public IP or DNS:
 
```bash
curl -s http://169.254.169.254/latest/meta-data/public-ipv4        # Public IP
curl -s http://169.254.169.254/latest/meta-data/public-hostname    # Public DNS
```
 
---
 
### Routes
 
The To-Do app needs three capabilities:
 
- Create a new task → **POST**
- Display all tasks → **GET**
- Delete a completed task → **DELETE**
 
Create the routes directory and file:
 
```bash
mkdir routes
cd routes
touch api.js
vim api.js
```
 
Paste the following:
 
```javascript
const express = require('express');
const router = express.Router();
 
router.get('/todos', (req, res, next) => {
 
});
 
router.post('/todos', (req, res, next) => {
 
});
 
router.delete('/todos/:id', (req, res, next) => {
 
});
 
module.exports = router;
```
 
---
 
### Models
 
Install Mongoose (MongoDB ODM for Node.js):
 
```bash
cd ..
npm install mongoose
```
 
Create the models directory and file:
 
```bash
mkdir models && cd models && touch todo.js
```
 
Open and populate `todo.js`:
 
```bash
vim todo.js
```
 
```javascript
const mongoose = require('mongoose');
const Schema = mongoose.Schema;
 
// Create schema for todo
const TodoSchema = new Schema({
  action: {
    type: String,
    required: [true, 'The todo text field is required']
  }
});
 
// Create model for todo
const Todo = mongoose.model('todo', TodoSchema);
 
module.exports = Todo;
```
 
Update `routes/api.js` to use the model. Open the file and clear it with `:%d`, then paste:
 
```javascript
const express = require('express');
const router = express.Router();
const Todo = require('../models/todo');
 
router.get('/todos', (req, res, next) => {
  // Returns all data, exposing only id and action fields
  Todo.find({}, 'action')
    .then(data => res.json(data))
    .catch(next);
});
 
router.post('/todos', (req, res, next) => {
  if (req.body.action) {
    Todo.create(req.body)
      .then(data => res.json(data))
      .catch(next);
  } else {
    res.json({ error: "The input field is empty" });
  }
});
 
router.delete('/todos/:id', (req, res, next) => {
  Todo.findOneAndDelete({ "_id": req.params.id })
    .then(data => res.json(data))
    .catch(next);
});
 
module.exports = router;
```
 
---
 
### MongoDB Database (mLab)
 
1. Sign up at [mLab](https://www.mongodb.com/atlas) and select AWS as cloud provider.
2. Allow access from anywhere (**for testing only**).
3. Set entry deletion time to **1 Week** (not 6 hours).
4. Create a database and collection.
 
Create the `.env` file in the `Todo` directory:
 
```bash
touch .env
vi .env
```
 
Add your connection string:
 
```
DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```
 
> Replace `<username>`, `<password>`, `<network-address>`, and `<dbname>` with your actual credentials.
 
Update `index.js` (clear with `:%d` in vim and paste):
 
```javascript
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();
 
const app = express();
const port = process.env.PORT || 5000;
 
// Connect to the database
mongoose.connect(process.env.DB)
  .then(() => console.log(`Database connected successfully`))
  .catch(err => console.log(err));
 
// Override deprecated mongoose promise with Node's promise
mongoose.Promise = global.Promise;
 
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});
 
app.use(bodyParser.json());
app.use('/api', routes);
 
app.use((err, req, res, next) => {
  console.log(err);
  next();
});
 
app.listen(port, () => {
  console.log(`Server running on port ${port}`)
});
```
 
Start the server:
 
```bash
node index.js
```
 
You should see: `Database connected successfully`
 
---
 
### Testing Backend with Postman
 
Install [Postman](https://www.postman.com/downloads/) to test the API endpoints.
 
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `http://<PublicIP>:5000/api/todos` | Create a new task |
| GET | `http://<PublicIP>:5000/api/todos` | Retrieve all tasks |
| DELETE | `http://<PublicIP>:5000/api/todos/:id` | Delete a task by ID |
 
> **Note:** Set the header `Content-Type: application/json` for POST requests.
 
**Backend checklist:**
 
- [x] Display a list of tasks – HTTP GET request
- [x] Add a new task to the list – HTTP POST request
- [x] Delete an existing task from the list – HTTP DELETE request
 
---
 
## Step 2 – Frontend Creation
 
Navigate back to the `Todo` root directory and scaffold the React app:
 
```bash
npx create-react-app client
```
 
This creates a `client` folder containing all React code.
 
### Install Dependencies
 
```bash
# Run multiple commands simultaneously from one terminal
npm install concurrently --save-dev
 
# Auto-restart server on code changes
npm install nodemon --save-dev
```
 
Update the `scripts` section in `Todo/package.json`:
 
```json
"scripts": {
  "start": "node index.js",
  "start-watch": "nodemon index.js",
  "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
}
```
 
### Configure Proxy
 
```bash
cd client
vi package.json
```
 
Add the proxy key:
 
```json
"proxy": "http://localhost:5000"
```
 
> This allows the browser to call `http://localhost:5000` directly instead of the full API path.
 
### Run the App
 
From the `Todo` directory:
 
```bash
npm run dev
```
 
The app will start on **http://localhost:3000**
 
> **AWS Note:** Open TCP port **3000** in your EC2 Security Group inbound rules to access the app from the Internet.
 
---
 
## Step 3 – Creating React Components
 
One of the advantages of React is that it makes use of reusable components, which also keeps code modular. For our Todo app, there will be two stateful components and one stateless component.
 
From the `Todo` directory, navigate to the components folder:
 
```bash
cd client
cd src
mkdir components
cd components
```
 
Create the three component files:
 
```bash
touch Input.js ListTodo.js Todo.js
```
 
---
 
### Input.js
 
```bash
vi Input.js
```
 
Paste the following:
 
```javascript
import React, { Component } from 'react';
import axios from 'axios';
 
class Input extends Component {
 
  state = {
    action: ""
  }
 
  addTodo = () => {
    const task = { action: this.state.action }
 
    if (task.action && task.action.length > 0) {
      axios.post('/api/todos', task)
        .then(res => {
          if (res.data) {
            this.props.getTodos();
            this.setState({ action: "" })
          }
        })
        .catch(err => console.log(err))
    } else {
      console.log('input field required')
    }
  }
 
  handleChange = (e) => {
    this.setState({
      action: e.target.value
    })
  }
 
  render() {
    let { action } = this.state;
    return (
      <div>
        <input type="text" onChange={this.handleChange} value={action} />
        <button onClick={this.addTodo}>add todo</button>
      </div>
    )
  }
}
 
export default Input;
```
 
### Install Axios
 
Axios is a Promise-based HTTP client for the browser and Node.js. Move back out of `components` to the `client` folder and install:
 
```bash
cd ..          # move to src folder
cd ..          # move to client folder
npm install axios
```
 
Then go back to the `components` directory:
 
```bash
cd src/components
```
 
---
 
### ListTodo.js
 
```bash
vi ListTodo.js
```
 
Paste the following:
 
```javascript
import React from 'react';
 
const ListTodo = ({ todos, deleteTodo }) => {
  return (
    <ul>
      {
        todos && todos.length > 0 ?
          (
            todos.map(todo => {
              return (
                <li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
              )
            })
          ) :
          (
            <li>No todo(s) left</li>
          )
      }
    </ul>
  )
}
 
export default ListTodo;
```
 
---
 
### Todo.js
 
```bash
vi Todo.js
```
 
Paste the following:
 
```javascript
import React, { Component } from 'react';
import axios from 'axios';
 
import Input from './Input';
import ListTodo from './ListTodo';
 
class Todo extends Component {
 
  state = {
    todos: []
  }
 
  componentDidMount() {
    this.getTodos();
  }
 
  getTodos = () => {
    axios.get('/api/todos')
      .then(res => {
        if (res.data) {
          this.setState({
            todos: res.data
          })
        }
      })
      .catch(err => console.log(err))
  }
 
  deleteTodo = (id) => {
    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if (res.data) {
          this.getTodos()
        }
      })
      .catch(err => console.log(err))
  }
 
  render() {
    let { todos } = this.state;
    return (
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos} />
        <ListTodo todos={todos} deleteTodo={this.deleteTodo} />
      </div>
    )
  }
}
 
export default Todo;
```
 
---
 
### Update App.js
 
Move to the `src` folder and update `App.js`:
 
```bash
cd ..
vi App.js
```
 
Replace the content with:
 
```javascript
import React from 'react';
 
import Todo from './components/Todo';
import './App.css';
 
const App = () => {
  return (
    <div className="App">
      <Todo />
    </div>
  );
}
 
export default App;
```
 
---
 
### Update App.css
 
```bash
vi App.css
```
 
Replace the content with:
 
```css
.App {
  text-align: center;
  font-size: calc(10px + 2vmin);
  width: 60%;
  margin-left: auto;
  margin-right: auto;
}
 
input {
  height: 40px;
  width: 50%;
  border: none;
  border-bottom: 2px #101113 solid;
  background: none;
  font-size: 1.5rem;
  color: #787a80;
}
 
input:focus {
  outline: none;
}
 
button {
  width: 25%;
  height: 45px;
  border: none;
  margin-left: 10px;
  font-size: 25px;
  background: #101113;
  border-radius: 5px;
  color: #787a80;
  cursor: pointer;
}
 
button:focus {
  outline: none;
}
 
ul {
  list-style: none;
  text-align: left;
  padding: 15px;
  background: #171a1f;
  border-radius: 5px;
}
 
li {
  padding: 15px;
  font-size: 1.5rem;
  margin-bottom: 15px;
  background: #282c34;
  border-radius: 5px;
  overflow-wrap: break-word;
  cursor: pointer;
}
 
@media only screen and (min-width: 300px) {
  .App {
    width: 80%;
  }
 
  input {
    width: 100%;
  }
 
  button {
    width: 100%;
    margin-top: 15px;
    margin-left: 0;
  }
}
 
@media only screen and (min-width: 640px) {
  .App {
    width: 60%;
  }
 
  input {
    width: 50%;
  }
 
  button {
    width: 30%;
    margin-left: 10px;
    margin-top: 0;
  }
}
```
 
---
 
### Update index.css
 
```bash
vim index.css
```
 
Replace the content with:
 
```css
body {
  margin: 0;
  padding: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
    "Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
    sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  box-sizing: border-box;
  background-color: #282c34;
  color: #787a80;
}
 
code {
  font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
    monospace;
}
```
 
---
 
### Run the Full Application
 
Navigate back to the `Todo` root directory and start the app:
 
```bash
cd ../..
npm run dev
```
 
Assuming no errors when saving all files, the To-Do app should be ready and fully functional with:
 
- Creating a task
- Deleting a task
- Viewing all tasks
 
---
 
