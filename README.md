# taskplanner.github.io
task-planner/
├── backend/
│   ├── server.js
│   ├── package.json
│   ├── .env
│   └── ...
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── App.js
│   │   ├── index.js
│   │   └── ...
│   ├── package.json
│   └── ...
├── .gitignore
└── README.md
cd backend
npm init -y
npm install express mongoose dotenv
const express = require('express');
const mongoose = require('mongoose');
const dotenv = require('dotenv');

dotenv.config();

const app = express();
const PORT = process.env.PORT || 5000;

app.use(express.json());

mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.log(err));

const taskSchema = new mongoose.Schema({
  date: Date,
  description: String
});

const Task = mongoose.model('Task', taskSchema);

app.get('/tasks', async (req, res) => {
  const tasks = await Task.find();
  res.json(tasks);
});

app.post('/tasks', async (req, res) => {
  const { date, description } = req.body;
  const newTask = new Task({ date, description });
  await newTask.save();
  res.json(newTask);
});

app.delete('/tasks/:id', async (req, res) => {
  await Task.findByIdAndDelete(req.params.id);
  res.json({ message: 'Task deleted' });
});

app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
MONGO_URI=your_mongodb_connection_string
npx create-react-app frontend
cd frontend
npm install axios
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [tasks, setTasks] = useState([]);
  const [description, setDescription] = useState('');
  const [date, setDate] = useState('');

  useEffect(() => {
    axios.get('/tasks')
      .then(response => setTasks(response.data))
      .catch(error => console.error('Error fetching tasks:', error));
  }, []);

  const addTask = () => {
    axios.post('/tasks', { date, description })
      .then(response => setTasks([...tasks, response.data]))
      .catch(error => console.error('Error adding task:', error));
  };

  const deleteTask = (id) => {
    axios.delete(`/tasks/${id}`)
      .then(() => setTasks(tasks.filter(task => task._id !== id)))
      .catch(error => console.error('Error deleting task:', error));
  };

  return (
    <div>
      <h1>Task Planner</h1>
      <input type="date" value={date} onChange={e => setDate(e.target.value)} />
      <input type="text" placeholder="Task description" value={description} onChange={e => setDescription(e.target.value)} />
      <button onClick={addTask}>Add Task</button>
      <ul>
        {tasks.map(task => (
          <li key={task._id}>
            {task.date}: {task.description}
            <button onClick={() => deleteTask(task._id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}

export default App;
"proxy": "http://localhost:5000"
git add .
git commit -m "Initial commit"
git push origin main
