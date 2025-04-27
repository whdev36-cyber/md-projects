# Learning React JS Through a Project

The best way to learn React is by building a project! Let's create a simple Task Manager application that will cover core React concepts.

## Project Overview: Task Manager App

We'll build an application that allows users to:
- Add new tasks
- Mark tasks as complete
- Delete tasks
- Filter tasks (all, active, completed)

## Step 1: Set Up Your Environment

1. Install Node.js (includes npm)
2. Create React app: `npx create-react-app task-manager`
3. Navigate to project: `cd task-manager`
4. Start development server: `npm start`

## Step 2: Project Structure

```
src/
├── components/
│   ├── Task.js
│   ├── TaskList.js
│   ├── AddTask.js
│   └── Filter.js
├── App.js
├── index.js
└── styles.css
```

## Step 3: Core Components

### 1. App.js (Main Component)
```jsx
import { useState } from 'react';
import AddTask from './components/AddTask';
import TaskList from './components/TaskList';
import Filter from './components/Filter';
import './styles.css';

function App() {
  const [tasks, setTasks] = useState([]);
  const [filter, setFilter] = useState('all');

  const addTask = (text) => {
    const newTask = {
      id: Date.now(),
      text,
      completed: false
    };
    setTasks([...tasks, newTask]);
  };

  const toggleTask = (id) => {
    setTasks(tasks.map(task => 
      task.id === id ? {...task, completed: !task.completed} : task
    ));
  };

  const deleteTask = (id) => {
    setTasks(tasks.filter(task => task.id !== id));
  };

  const filteredTasks = tasks.filter(task => {
    if (filter === 'active') return !task.completed;
    if (filter === 'completed') return task.completed;
    return true;
  });

  return (
    <div className="app">
      <h1>Task Manager</h1>
      <AddTask onAdd={addTask} />
      <Filter currentFilter={filter} onFilterChange={setFilter} />
      <TaskList 
        tasks={filteredTasks} 
        onToggle={toggleTask} 
        onDelete={deleteTask} 
      />
    </div>
  );
}

export default App;
```

### 2. Task.js (Individual Task Component)
```jsx
export default function Task({ task, onToggle, onDelete }) {
  return (
    <div className={`task ${task.completed ? 'completed' : ''}`}>
      <input
        type="checkbox"
        checked={task.completed}
        onChange={() => onToggle(task.id)}
      />
      <span>{task.text}</span>
      <button onClick={() => onDelete(task.id)}>Delete</button>
    </div>
  );
}
```

### 3. TaskList.js
```jsx
import Task from './Task';

export default function TaskList({ tasks, onToggle, onDelete }) {
  return (
    <div className="task-list">
      {tasks.length === 0 ? (
        <p>No tasks found</p>
      ) : (
        tasks.map(task => (
          <Task 
            key={task.id} 
            task={task} 
            onToggle={onToggle} 
            onDelete={onDelete} 
          />
        ))
      )}
    </div>
  );
}
```

### 4. AddTask.js
```jsx
import { useState } from 'react';

export default function AddTask({ onAdd }) {
  const [text, setText] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    if (text.trim()) {
      onAdd(text);
      setText('');
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={text}
        onChange={(e) => setText(e.target.value)}
        placeholder="Add a new task..."
      />
      <button type="submit">Add Task</button>
    </form>
  );
}
```

### 5. Filter.js
```jsx
export default function Filter({ currentFilter, onFilterChange }) {
  const filters = ['all', 'active', 'completed'];

  return (
    <div className="filter">
      {filters.map(filter => (
        <button
          key={filter}
          className={currentFilter === filter ? 'active' : ''}
          onClick={() => onFilterChange(filter)}
        >
          {filter.charAt(0).toUpperCase() + filter.slice(1)}
        </button>
      ))}
    </div>
  );
}
```

## Step 4: Basic Styling (styles.css)
```css
.app {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.task {
  display: flex;
  align-items: center;
  padding: 10px;
  margin: 5px 0;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.task.completed span {
  text-decoration: line-through;
  color: #888;
}

.task input {
  margin-right: 10px;
}

.task span {
  flex-grow: 1;
}

.task button {
  background: #ff4444;
  color: white;
  border: none;
  padding: 5px 10px;
  border-radius: 3px;
  cursor: pointer;
}

form {
  display: flex;
  margin: 20px 0;
}

form input {
  flex-grow: 1;
  padding: 8px;
  margin-right: 10px;
}

.filter button {
  margin-right: 5px;
  padding: 5px 10px;
  background: #f0f0f0;
  border: 1px solid #ddd;
  cursor: pointer;
}

.filter button.active {
  background: #4CAF50;
  color: white;
}
```

## Key React Concepts Covered

1. **Components**: Breaking UI into reusable pieces
2. **State Management**: Using `useState` hook
3. **Props**: Passing data between components
4. **Event Handling**: Responding to user interactions
5. **Conditional Rendering**: Showing different UI based on state
6. **Lists & Keys**: Rendering dynamic lists
7. **Forms**: Controlled components

## Next Steps to Enhance the Project

1. Add local storage to persist tasks
2. Implement editing existing tasks
3. Add due dates and priorities
4. Create a more sophisticated UI with a library like Material-UI
5. Add animations for task transitions
6. Implement drag-and-drop reordering

This project covers fundamental React concepts that you can build upon. As you become more comfortable, you can explore more advanced topics like:
- Context API for state management
- React Router for multiple views
- Custom hooks
- Integration with backend APIs
