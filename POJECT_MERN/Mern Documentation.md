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
<img width="924" height="480" alt="Screenshot 2026-04-02 153907" src="https://github.com/user-attachments/assets/e26b920f-75e2-4ee2-b5e6-9ac52fbc4535" />
<img width="937" height="323" alt="Screenshot 2026-04-02 153901" src="https://github.com/user-attachments/assets/ffafb656-6052-4ccf-9aed-bf3c2c1f2d0e" />
<img width="832" height="644" alt="Screenshot 2026-04-02 153517" src="https://github.com/user-attachments/assets/168bab0a-33dc-48ee-b651-c8d76413a7f8" />
<img width="904" height="719" alt="Screenshot 2026-04-02 153502" src="https://github.com/user-attachments/assets/6c32b1cc-57f8-4917-bdfc-62ce03dbefc3" />
<img width="918" height="553" alt="Screenshot 2026-04-02 152839" src="https://github.com/user-attachments/assets/f0632b96-c711-4960-b493-ecfd66d1af62" />


This initializes a Node.js project and generates a package.json file, which manages dependencies and scripts.
---
### 2. Backend Framework Setup (Express.js)
# 1. Install Express.js
```bash
mkdir Todo
ls
cd Todo
```
 <img width="918" height="553" alt="Screenshot 2026-04-02 152839" src="https://github.com/user-attachments/assets/0f4fb0fa-ea22-4b87-984f-6464083840b6" />
 
Initialise the project (creates `package.json`):
 
```bash
npm init
```
<img width="832" height="644" alt="Screenshot 2026-04-02 153517" src="https://github.com/user-attachments/assets/98a2b279-120e-41a5-a9b9-5eed8e465afe" />
<img width="904" height="719" alt="Screenshot 2026-04-02 153502" src="https://github.com/user-attachments/assets/b7b88df3-db75-404f-9fb8-bcaa40ef993f" />

 
Follow the prompts and press **Enter** to accept defaults. Type `yes` when asked to write the `package.json` file.
 
```bash
ls   # confirm package.json was created
```
 
---
 
### Install ExpressJS
 
```bash
npm install express
```
 <img width="937" height="323" alt="Screenshot 2026-04-02 153901" src="https://github.com/user-attachments/assets/4b7d9100-1cf1-46d7-8270-93ec53a22915" />
 <img width="924" height="480" alt="Screenshot 2026-04-02 153907" src="https://github.com/user-attachments/assets/0de2e8af-1d3c-4eef-bb2c-cd07f4a220c7" />


Create the entry point file:
 
```bash
touch index.js
ls   # confirm index.js was created
```
<img width="935" height="642" alt="Screenshot 2026-04-02 154100" src="https://github.com/user-attachments/assets/2c1335da-9ef4-4610-824f-5599b23431de" />

 
Install the dotenv module:
 
```bash
npm install dotenv
```
 <img width="935" height="642" alt="Screenshot 2026-04-02 154100" src="https://github.com/user-attachments/assets/3ec02633-0684-4a37-a8cf-564b62fa5921" />


Open and populate `index.js`:
 
```bash
vim index.js
```
<img width="935" height="642" alt="Screenshot 2026-04-02 154100" src="https://github.com/user-attachments/assets/8bb03be1-9dca-4d9a-9de8-cc706954c95d" />
 
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
 <img width="947" height="590" alt="Screenshot 2026-04-02 154223" src="https://github.com/user-attachments/assets/f7523b55-8dc0-4d48-adf2-3805da5a651b" />

> Save with `:w` and exit with `:qa` in vim.
 
Start the server:
 
```bash
node index.js
```
<img width="951" height="596" alt="Screenshot 2026-04-02 154343" src="https://github.com/user-attachments/assets/0a76a67b-24b1-4724-b5d2-847f42df6e05" />

 
You should see: `Server running on port 5000`
 
> **AWS Note:** Open TCP port **5000** in your EC2 Security Group inbound rules.
 
Access in browser:
 
```
http://<PublicIP-or-PublicDNS>:5000
```
Port 5000 has been opened in ec2 security group.
To get your server's public IP or DNS:
 
```bash
curl http://44.243.84.48:500

```
 <img width="838" height="449" alt="Screenshot 2026-04-02 154512" src="https://github.com/user-attachments/assets/c9d77b29-89ca-472a-bb73-4888fe04875b" />

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
<img width="951" height="287" alt="Screenshot 2026-04-02 155115" src="https://github.com/user-attachments/assets/a1c44b08-c5a8-49c8-ad64-6c8c9dd1b996" />

 
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
 <img width="851" height="528" alt="Screenshot 2026-04-02 155133" src="https://github.com/user-attachments/assets/eac8f42a-cbf0-4c3c-9024-e3496f41fb59" />

---
 
### Models
 
Install Mongoose (MongoDB ODM for Node.js):
 
```bash
cd ..
npm install mongoose
```
 <img width="928" height="447" alt="Screenshot 2026-04-02 155402" src="https://github.com/user-attachments/assets/a48a5497-3373-4c3c-b55c-f85a8f28bf97" />

Create the models directory and file:
 
```bash
mkdir models && cd models && touch todo.js
```
<img width="942" height="645" alt="Screenshot 2026-04-02 155248" src="https://github.com/user-attachments/assets/11bce67b-d9f6-4948-995b-212173e57ea8" />
Open and populate `todo.js`:
 
```bash
vim todo.js
```
 <img width="947" height="592" alt="Screenshot 2026-04-02 155556" src="https://github.com/user-attachments/assets/3185fd30-5b1c-4e55-92ed-70319a3e10fc" />

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
<img width="933" height="695" alt="Screenshot 2026-04-02 155546" src="https://github.com/user-attachments/assets/2c02328d-e7a0-4b93-9e2d-28aef6dfe632" />

 
Update `routes/api.js` to use the model. Open the file and clear it with `:%d`, then paste:
 <img width="939" height="563" alt="Screenshot 2026-04-02 155641" src="https://github.com/user-attachments/assets/73bae8cd-3152-40c6-b791-2703d2721911" />
<img width="941" height="567" alt="Screenshot 2026-04-02 155706" src="https://github.com/user-attachments/assets/3409512d-0e42-41df-9515-d8b7d436cf4b" />

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
 <img width="935" height="773" alt="Screenshot 2026-04-02 155743" src="https://github.com/user-attachments/assets/be26c2ef-6278-4478-9870-c0466892f3b6" />

---
 
### MongoDB Database (mLab)
 
1. Sign up at [mLab](https://www.mongodb.com/atlas) and select AWS as cloud provider.
2. Allow access from anywhere (**for testing only**).
3. Set entry deletion time to **1 Week** (not 6 hours).
4. Create a database and collection.
   <img width="1666" height="697" alt="Screenshot 2026-04-02 161248" src="https://github.com/user-attachments/assets/47e09552-b62f-47bb-9024-d47d8ca3908b" />

 
Create the `.env` file in the `Todo` directory:
 
```bash
touch .env
vi .env
```
 <img width="931" height="500" alt="Screenshot 2026-04-02 161408" src="https://github.com/user-attachments/assets/388470a0-9dc0-469b-9241-3177cd3a90ab" />

Add your connection string:
 
```
This format DB = 'mongodb+srv://<username>:<password>@<network-address>/<dbname>?retryWrites=true&w=majority'
```
 <img width="951" height="370" alt="Screenshot 2026-04-02 161440" src="https://github.com/user-attachments/assets/aebfc7dc-10da-4ca8-b35a-167cc24e07e8" />


 
Update `index.js` (clear with `:%d` in vim and paste):
<img width="939" height="529" alt="Screenshot 2026-04-02 161625" src="https://github.com/user-attachments/assets/d63e1a3d-0cd1-4ed1-a7f2-8b4c6ff81792" />

 
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
 <img width="952" height="779" alt="Screenshot 2026-04-02 161712" src="https://github.com/user-attachments/assets/887d0933-5def-4b85-af3e-e51aa70aa6ae" />

Start the server:
 
```bash
node index.js
```
 <img width="806" height="131" alt="Screenshot 2026-04-02 163846" src="https://github.com/user-attachments/assets/471665bd-7ce9-4e4f-8c00-6aa35b9ac0f1" />

You should see: `Database connected successfully`
 
---
 
### Testing Backend with Postman
 
Install [Postman](https://www.postman.com/downloads/) to test the API endpoints.
 
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| POST | `http://52.39.50.10:5000/api/Todo` | Create a new task |
| GET | `http://52.39.50.10:5000/api/Todo` | Retrieve all tasks |
| DELETE | `http://52.39.50.10:5000/api/Todo:id` | Delete a task by ID |
 
 
> **Note:** Set the header `Content-Type: application/json` for POST requests.
 
**Backend checklist:**
 
- [x] Display a list of tasks – HTTP GET request
- [x] Add a new task to the list – HTTP POST request
- [x] Delete an existing task from the list – HTTP DELETE request
      <img width="1419" height="856" alt="Screenshot 2026-04-03 143658" src="https://github.com/user-attachments/assets/5a0e35b8-1763-48f8-9170-f04af552ad24" />
<img width="948" height="881" alt="Screenshot 2026-04-03 143430" src="https://github.com/user-attachments/assets/4df6a9a1-d5c4-4537-a2e6-8e829264b542" />
<img width="1405" height="508" alt="Screenshot 2026-04-03 143208" src="https://github.com/user-attachments/assets/933b2656-7f60-4b03-8f01-c6b3cb031046" />
<img width="314" height="160" alt="Screenshot 2026-04-03 143158" src="https://github.com/user-attachments/assets/2ddf70cf-026b-4a78-8ce6-24ba768098fb" />
<img width="1401" height="491" alt="Screenshot 2026-04-03 143143" src="https://github.com/user-attachments/assets/64454bae-3873-48cd-b63c-210b0511ae73" />

 
---
 
## Step 2 – Frontend Creation
 
Navigate back to the `Todo` root directory and scaffold the React app:
 
```bash
npx create-react-app client
```
 
This creates a `client` folder containing all React code.
 <img width="948" height="896" alt="Screenshot 2026-04-03 144019" src="https://github.com/user-attachments/assets/434f8dfa-872d-472e-911a-69d908d32399" />

### Install Dependencies
 
```bash

npm install concurrently --save-dev
 
# Auto-restart server on code changes
npm install nodemon --save-dev
```
 <img width="943" height="945" alt="Screenshot 2026-04-03 144134" src="https://github.com/user-attachments/assets/93f949ca-bdff-4d9b-877a-cbaf8875a260" />
<img width="948" height="896" alt="Screenshot 2026-04-03 144019" src="https://github.com/user-attachments/assets/d078a728-33be-4210-a07d-15be58e1eda0" />

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
<img width="949" height="736" alt="Screenshot 2026-04-03 153852" src="https://github.com/user-attachments/assets/df0c8c93-bc7f-4f6e-b7c1-1125d8588ec7" />

 
Then go back to the `components` directory:
 
```bash
cd src/components
```
 <img width="949" height="713" alt="Screenshot 2026-04-03 153957" src="https://github.com/user-attachments/assets/1dc1ac15-0b83-4ffd-ba04-6b697355464f" />
<img width="957" height="946" alt="Screenshot 2026-04-03 153916" src="https://github.com/user-attachments/assets/b0f5d17f-fac7-472e-bfcb-1e082155856e" />

---
 
### ListTodo.js
 
```bash
vi ListTodo.js
```
<img width="949" height="713" alt="Screenshot 2026-04-03 153957" src="https://github.com/user-attachments/assets/602d8f44-0982-4bd1-b0c2-a76119a990b6" />

 
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
 <img width="938" height="830" alt="Screenshot 2026-04-03 154024" src="https://github.com/user-attachments/assets/45d765f5-d111-4436-a80b-7f70c4666cfa" />

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
 <img width="956" height="369" alt="Screenshot 2026-04-03 154250" src="https://github.com/user-attachments/assets/3ec671ed-dd80-49e0-aecc-e395b2fa5734" />

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
 <img width="936" height="669" alt="Screenshot 2026-04-03 154303" src="https://github.com/user-attachments/assets/f889ff58-b456-40dc-8a4c-959850a2ea46" />

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
 <img width="928" height="361" alt="Screenshot 2026-04-03 154509" src="https://github.com/user-attachments/assets/5d5b2152-a5f4-4ff9-b6d8-a1f083d0b3ff" />

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
 <img width="928" height="361" alt="Screenshot 2026-04-03 154509" src="https://github.com/user-attachments/assets/906ca743-6fcf-4a71-bf78-86bccc04fb25" />

---
 
### Run the Full Application
 
Navigate back to the `Todo` root directory and start the app:
 
```bash
cd ../..
npm run dev
```
 <img width="956" height="949" alt="Screenshot 2026-04-03 154557" src="https://github.com/user-attachments/assets/c55bb718-eccf-4e89-8808-dbf5c3ea19a2" />
 <img width="1597" height="861" alt="Screenshot 2026-04-03 161528" src="https://github.com/user-attachments/assets/cc6c5a81-eeb7-4573-9221-150174aecf7b" />


The Todoo app is ready and fully functional with:
 
- Creating a task
- Deleting a task
- Viewing all tasks
 
### Final Conclusion & Key Takeaways

The successful deployment of this **MERN** application on **AWS** marks a critical transition from local development to a scalable, cloud-hosted architecture. By meticulously integrating **MongoDB Atlas** for data persistence, **Express.js** and **Node.js** for a robust API layer, and **React.js** for a dynamic user interface, we have established a professional-grade web stack.

#### 🚀 Key Technical Achievements:

* **Cloud Infrastructure:** Leveraged **AWS EC2 (t3.small)** to provide the necessary memory overhead and CPU burst capabilities required for a seamless development-to-production environment.
* **Security & Configuration:** Implemented **Environment Variables (.env)** and **AWS Security Groups** to ensure that sensitive database credentials and server ports remain protected from unauthorized access.
* **Asynchronous Communication:** Utilized **Axios** and the **MERN** design pattern to facilitate real-time, non-blocking data exchange between the client and the server.
* **Workflow Optimization:** Integrated **Concurrently** and **Nodemon**, allowing for a high-velocity development cycle where both frontend and backend changes are rendered and monitored instantly.

#### 🏁 Summary
This project serves as a foundational blueprint for building and deploying complex, full-stack applications. With this architecture in place, the application is now ready for further professional enhancements, such as **Nginx** reverse proxying, **SSL/TLS** encryption for HTTPS, and automated **CI/CD** pipelines for continuous deployment.
 
