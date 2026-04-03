# MERN WEB STACK IMPLEMENTATION ON AWS

## INTRODUCTION

The MERN stack is a combination of technologies used to build full-stack web applications:

- MongoDB – Database
- Express.js – Backend framework
- React.js – Frontend library
- Node.js – Runtime environment

This project demonstrates building and deploying a MERN stack app on AWS.

---

## PREREQUISITES

- AWS EC2 instance (t3.small Ubuntu 24.04)
- SSH key pair
- Security group with ports:
  - 22, 80, 443, 3000, 5000

```bash
chmod 400 my-ec2-key.pem
ssh -i "my-ec2-key.pem" ubuntu@<PUBLIC_IP>
```

---

## BACKEND SETUP

```bash
sudo apt update && sudo apt upgrade -y

curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install nodejs -y

node -v
npm -v
```

---

## PROJECT INIT

```bash
mkdir Todo && cd Todo
npm init -y
npm install express dotenv mongoose body-parser
```

---

## INDEX.JS (BACKEND SERVER)

```js
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
require('dotenv').config();

const app = express();
const port = process.env.PORT || 5000;

mongoose.connect(process.env.DB)
  .then(() => console.log("DB Connected"))
  .catch(err => console.log(err));

app.use(bodyParser.json());
app.use('/api', routes);

app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  next();
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

---

## ROUTES

```bash
mkdir routes && cd routes
touch api.js
```

```js
const express = require('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res) => {
  Todo.find().then(data => res.json(data));
});

router.post('/todos', (req, res) => {
  Todo.create(req.body).then(data => res.json(data));
});

router.delete('/todos/:id', (req, res) => {
  Todo.findByIdAndDelete(req.params.id)
    .then(data => res.json(data));
});

module.exports = router;
```

---

## MODEL

```bash
mkdir models && cd models
touch todo.js
```

```js
const mongoose = require('mongoose');

const TodoSchema = new mongoose.Schema({
  action: {
    type: String,
    required: true
  }
});

module.exports = mongoose.model('todo', TodoSchema);
```

---

## ENV FILE

```env
DB=mongodb+srv://<username>:<password>@<cluster>/<dbname>
```

---

## REACT APP

```bash
npx create-react-app client
cd client
npm install axios
```

---

## ROOT PACKAGE.JSON SCRIPTS

```json
"scripts": {
  "start": "node index.js",
  "start-watch": "nodemon index.js",
  "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
}
```

---

## CLIENT PACKAGE.JSON

```json
"proxy": "http://localhost:5000"
```

---

## REACT COMPONENTS

### Input.js
```js
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {
  state = { action: "" };

  handleChange = (e) => {
    this.setState({ action: e.target.value });
  };

  addTodo = () => {
    axios.post('/api/todos', { action: this.state.action })
      .then(() => {
        this.props.getTodos();
        this.setState({ action: "" });
      });
  };

  render() {
    return (
      <div>
        <input value={this.state.action} onChange={this.handleChange} />
        <button onClick={this.addTodo}>Add</button>
      </div>
    );
  }
}

export default Input;
```

---

### ListTodo.js
```js
import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => (
  <ul>
    {todos.length ? todos.map(todo => (
      <li key={todo._id} onClick={() => deleteTodo(todo._id)}>
        {todo.action}
      </li>
    )) : <li>No Todos</li>}
  </ul>
);

export default ListTodo;
```

---

### Todo.js
```js
import React, { Component } from 'react';
import axios from 'axios';
import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {
  state = { todos: [] };

  componentDidMount() {
    this.getTodos();
  }

  getTodos = () => {
    axios.get('/api/todos')
      .then(res => this.setState({ todos: res.data }));
  };

  deleteTodo = (id) => {
    axios.delete(`/api/todos/${id}`)
      .then(() => this.getTodos());
  };

  render() {
    return (
      <div>
        <h1>My Todos</h1>
        <Input getTodos={this.getTodos} />
        <ListTodo todos={this.state.todos} deleteTodo={this.deleteTodo} />
      </div>
    );
  }
}

export default Todo;
```

---

### App.js
```js
import React from 'react';
import Todo from './components/Todo';

const App = () => (
  <div>
    <Todo />
  </div>
);

export default App;
```

---

## RUN APPLICATION

```bash
npm run dev
```

- Backend: http://localhost:5000  
- Frontend: http://localhost:3000  

---

## CONCLUSION

This project demonstrates a full MERN stack deployment on AWS using:
- Node.js + Express backend
- MongoDB database
- React frontend
- REST API integration
```  
