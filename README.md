# Node-Express-MongoDB-trick

# NodeJs, Express, MongoDB Tricks

## Summary

## Develop NodeJs REST APIs using Express and MongoDB

> **url**: https://medium.com/nerd-for-tech/develop-nodejs-rest-apis-using-express-and-mongodb-e5da289ff8c0
> 
> **author**: Vishnu Sivan
> 
> **re-writer**: Renaud Racinet

APIs (Application Programming Interfaces) define the rules that programmers must follow in order to interact with a programming language, a software library, or any other software tool. Web APIs are a set of rules for interacting with a webserver, with the most common use case being data retrieval.

APIs are vital tools for businesses in all industries. The main reason that APIs matter so much in modern markets is that they allow for faster innovation. The importance of APIs from a technical standpoint, they allow the capabilities of one computer program to be used by another. Hence two different programs are able to communicate.

### Rest and Rest Apis

REST is an acronym for Representational State Transfer. It is a web standards architecture and HTTP Protocol. RESTful applications use HTTP requests to perform the four operations termed as CRUD (C: create, R: read, U: update, and D: delete). Create and/or update is used to post data, get for reading/listing data, and delete to remove data. In REST, each URL is called a request while the data sent back to you is called a response.

REST API is an API that conforms to the design principles of the REST, or representational state transfer architectural style.

### Getting Started

This article is designed for creating a RESTful API using node and mongoDB. For that, we create a RESTful todo list API (endpoints that will create a task, get or read list of all tasks, read a particular task, delete a task, and update a task).

**Requirements**

* [NodeJS](https://nodejs.org/en/download/)
* [MongoDB](https://www.mongodb.com/try/download)
* [Postman](https://www.postman.com/downloads/)
* [Visual Studio Code](https://www.postman.com/downloads/)

Hope you have installed the requirements. You have to unlock the mongodb installer for installation. It can be done by taking properties window and check the unblock checkbox in the bottom right corner.

Also ensure that you have added mongodb path in the environment variables.

For windows **ONLY**
![https://miro.medium.com/max/1400/1*3b9oX3KpOLNaZPqbxdUv3g.png](https://miro.medium.com/max/1400/1*3b9oX3KpOLNaZPqbxdUv3g.png)

Run the following commands in the command prompt to verify the installation status.

```bash
npm -v
mongo --version
```

#### Setting up the project structure
Let’s start to create our first node api.

* Open command prompt and follow the steps
* Create a Folder name todoListApi

```bash
mkdir todoListApi
```

* Navigate to the root of your newly created folder

```bash
cd todoListApi
```

* Create a package.json file

```bash
npm init
```

`npm init` will prompt you to enter some information such as the app name, description, version, author, keyword and also ask if what you see is what you like. Type yes and press enter to complete the creation of our package.json.

`Package.json` is a file that gives the necessary information to npm which allows it to identify the project as well as handle the project's dependencies.

![https://miro.medium.com/max/1400/1*n8UVGjNZgT9Ib8BaIJSX-A.png](https://miro.medium.com/max/1400/1*n8UVGjNZgT9Ib8BaIJSX-A.png)

* Open VSCode under this folder by typing code .
* Create a file called index.js. Here, we will write the protocols to create our server.
* Create a folder called api. Inside this folder called api, create three separate folders called models, routes, and controllers. 
* Create todoListController.js in the api/controller folder, todoListRoutes.js in the routes folder, and todoListModel in the model folder.

```bash
touch api/controllers/todoListController.js api/models/todoListModel.js api/routes/todoListRoutes.js
```

The final folder structure should look like this:

![https://miro.medium.com/max/1400/1*WUJuZ68V1T1lMvJ2-TvFbg.png](https://miro.medium.com/max/1400/1*WUJuZ68V1T1lMvJ2-TvFbg.png)

#### Setting up the server
Let’s install express, will be used to create the server.

```bash
npm install express
```

then open the `package.json` file and add this task to the script.

```bash
"start": "node index.js"
```

![https://miro.medium.com/max/1040/1*ZhWn1Z0eZKSJWmjPNzxjPA.png](https://miro.medium.com/max/1040/1*ZhWn1Z0eZKSJWmjPNzxjPA.png)

Now, open index.js file and add the following,

```bash
var express = require('express'),
app = express(),
port = process.env.PORT || 3000;

app.listen(port);console.log('todo list api server listening at ' + port);
```

then run `npm start` in cmd to start the server.

![https://miro.medium.com/max/1306/1*hcB4PvJXD6SKOlq7n0kvcQ.png](https://miro.medium.com/max/1306/1*hcB4PvJXD6SKOlq7n0kvcQ.png)

#### Setting up the schema
Let’s install express, will be used to interact with a MongoDB instance.

```bash
npm install mongoose
```

Now, open the `todoListModel.js` file in your api/models folder and add the following,

```javascript
'use strict';
var mongoose = require('mongoose');
var Schema = mongoose.Schema;var TaskSchema = new Schema({
  name: {
    type: String,
    required: 'Enter the name of the task'
  },
  created_date: {
    type: Date,
    default: Date.now
  },
  status: {
    type: [{
      type: String,
      enum: ['pending', 'ongoing', 'completed']
    }],
    default: ['pending']
  }
});

module.exports = mongoose.model('Tasks', TaskSchema);
```

```typescript
import Mongoose, {Schema, Model, Types} from 'mongoose';

interface ITask {
    name: string,
    created_Date: Date
    status: string
}

const TaskSchema: Schema<ITask> = new Schema({
    name: {
        type: String,
        required: 'Enter the name of the task'
    },
    created_date: {
        type: Date,
        default: Date.now
    },
    status: {
        type: [{
            type: String,
            enum: ['pending', 'ongoing', 'completed']
        }],
        default: ['pending']
    }
});

const TaskModel: Model<> = Mongoose.model<ITask>('Task', TaskSchema);

export default TaskModel;
```

Let’s understand the code.

We have created a model of the task collection. The task collection contains a name: string, the date it was created and task status which we have defined as pending by default for every task created.

#### Setting up the routes

Routing refers to determining how an application responds to a client request for a specific endpoint. Each of the routes has different route handler functions, which are executed when the route is matched.
Here we have defined two basic routes `/tasks`, and `/tasks/taskId`
/tasks has two methods GET and POST, while `/tasks/taskId` has GET, PUT and DELETE.
Also, we required the controller so each of the routes methods can call it’s respective handler function. Will do it next.

So, open the `todoListRoutes.js` file in your api/routes folder and add the following,

```javascript
'use strict';
module.exports = function(app) {
  var todoList = require('../controllers/todoListController');  app.route('/tasks')
    .get(todoList.listAllTasks)
    .post(todoList.createTask);  app.route('/tasks/:taskId')
    .get(todoList.readTask)
    .put(todoList.updateTask)
    .delete(todoList.deleteTask);
};
```

```typescript
import todoList from '../controllers/todoListController';

const paths = (app: Express.Application) => {
    app.route('/tasks')
        .get(todoList?.listAllTasks)
        .post(todoList?.createTask);
    
    app.route('/tasks/:taskId')
        .get(todoList?.readTask)
        .put(todoList?.updateTask)
        .delete(todoList?.deleteTask)
} 

export default paths;
```

#### Setting up the controller

Controllers are typically callback functions that corresponds to the routers to handle requests. It is a good design principle to keep the code concise and readable.

Here, we would be writing five different methods — **listAllTasks, createTask, readTask, updateTask, deleteTask**. We will exported each of the functions for us to use in our routes. Each of these functions uses different mongoose methods such as **find, findById, findOneAndUpdate, save and remove**.

So, open the `todoListController.js` file in your api/controllers folder and add the following,

```javascript
'use strict';
var mongoose = require('mongoose'),
    Task = mongoose.model('Tasks');

exports.listAllTasks = function(req, res) {
  Task.find({}, function(err, task) {
    if (err)
      res.send(err);
    res.json(task);
  });
};

exports.createTask = function(req, res) {
  var new_task = new Task(req.body);
  new_task.save(function(err, task) {
    if (err)
      res.send(err);
    res.json(task);
  });
};

exports.readTask = function(req, res) {
  Task.findById(req.params.taskId, function(err, task) {
    if (err)
      res.send(err);
    res.json(task);
  });
};

exports.updateTask = function(req, res) {
  Task.findOneAndUpdate({_id: req.params.taskId}, req.body, {new: true}, function(err, task) {
    if (err)
      res.send(err);
    res.json(task);
  });
};

exports.deleteTask = function(req, res) {
  Task.remove({
    _id: req.params.taskId
  }, function(err, task) {
    if (err)
      res.send(err);
    res.json({ message: 'Task successfully deleted' });
  });
};
```

```typescript
import { Request, Response} from 'express';
import Mongoose, {Model} from 'mongoose';
import TaskModel from '../abstraction/taskModel';

export default class todoListController {
    constructor() {}
    
    public listAllTasks = (req: Request, res: Response) => {
        Task.find({}, (err, task) => {
            if (err) res.send(err); // Or throw new Error(err.message);
            return res.json(task); // Or return task;   
        })
    }
    
    public createTask = (req: Request, res: Response) => {
        const NEW_TASK = new Task(req?.body);
        NEW_TASK?.save( (err, task) => {
            if (err) res.send(err);
            return res.json(task);
        })
    }
    
    public readTask = (req: Request, res: Response) => {
        TaskModel?.findById(req?.params?.taskId, (err, task) => {
            if (err) res.send(err);
            res.json(task);
        })
    }
    
    public updateTask = (req: Request, res: Response) => {
        TaskModel?.findOneAndUpdate(
            {
                _id: req.params.taskId
            },
            req?.body,
            { 
                new: true
            },
            (err, task) => {
                if (err) res.send(err);
                res.json(task);
            }
        )
    }
    
    public deleteTask = (req: Request, res: Response) => {
        TaskModel?.remove({ _id: req?.params?.taskId }, (req: Request, res: Response) => { 
            if (err) res.send(err);
            res.json({ message: 'Task successfully deleted'});
        });
    } 
}
```

#### Putting everything together

We almost done with the code. Now, we will be connecting database, controllers, models, and routes together.

1. Connect your database by adding a url to the mongoose instance connection
2. Load the created model — task
3. Install bodyParser by typing `npm install body-parser` and use bodyParser Parse incoming request bodies in a middleware before your handlers, available under the `req.body property`. All middlewares will populate the `req.bodyproperty` with the parsed body, or an empty object ({}) if there was no body to parse (or an error was returned).
4. Register our created routes in the server
5. Finally replace the `index.js` file content with the following

```javascript
var express = require('express'),
  app = express(),
  port = process.env.PORT || 3000,
  mongoose = require('mongoose'),
  Task = require('./api/models/todoListModel'), //created model loading here
  bodyParser = require('body-parser');
  
// mongoose instance connection url connection
mongoose.Promise = global.Promise;
mongoose.connect('mongodb://localhost/Tododb', { useNewUrlParser: true, useUnifiedTopology: true });

app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());var routes = require('./api/routes/todoListRoutes'); //importing route

routes(app); //register the routeapp.listen(port);

console.log('todo list api server listening at ' + port);
```

```typescript
import express, {Application} from 'express';
import bodyParser from 'body-parser';
import Mongoose from 'mongoose';
import TaskModel from './api/models/todoListModel';
import TaskRoute from './api/routes/todoListRoutes';

export class Application {
    public app: Application = express().Application;
    private readonly PORT: string | number = process.env.PORT || 3000;

    constructor() {
        this.initialize();
        this.DBInit();
        this.configuationRoutes();
        this.initializeServer();
    }

    private initialize() {
        this.app.use(bodyParser.urlencodedd({ extended: true }));
        this.app.use(bodyParser.json());
    }
    
    private DBInit() {
        mongoose.Promise = global.Promise;
        mongoose.connect('mongodb://localhost/Tododb', {
            useNewUrlParser: true,
            useUnifiedTopology: true
        });
    }

    private configuationRoutes() {
        TaskRoute.path(this.app);
    }
    
    private initializeServer() {
        this.app.listen( this.PORT, () => console.log('todo list api server listening at ' + PORT));
    }
}

new Application();
```

Now you can start your server.

```bash
npm start
```

#### Test you app

Now that everything is now connected, let’s test each of the routes and the respective methods.

1. Open your postman and type `http://localhost:3000/tasks` in the enter request URL section and press enter.
2. On enter, you should see [] because there is nothing in the database yet.
3. On the same address, change the method to POST, click body and select `x-www-form-urlencoded`.
   
   Then, enter name as the key and the corresponding task name as value.
   
   After this, click on send button.

   This should give you a response 200 ok

#### Handling Exceptions (optional)

If you have entered `http://localhost:3000/task` instead of `http://localhost:3000/tasks`, it responds with a message **Cannot GET /task**. Let’s add express middleware which could be used to return more interactive messages.

Middlewares basically intercepts incoming http request and as such you can use them to perform several operations ranging from authentication to validations etc.

So, open your `index.js` file and the following.

```javascript
app.use( (err, res) => {
    res.status(404).send({ url: req.originalUrl + ' not found'});
})
```

I hope you had fun reading and/or following along. If you are interested in further exploring, here is the git repo with [this project](https://github.com/codemaker2015/rest-todo-list-api)
