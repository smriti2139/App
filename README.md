<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>To-Do App</title>
    <style>
        body {
    font-family: Arial, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
}

.container {
    display: flex;
    width: 80%;
    height: 80%;
    border: 1px solid #ccc;
}

.left-pane, .right-pane {
    flex: 1;
    padding: 20px;
}

#task-list {
    list-style-type: none;
    padding: 0;
}

.task-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
    border-bottom: 1px solid #ccc;
}

.task-item.completed {
    text-decoration: line-through;
}

.task-item button {
    margin-left: 10px;
}

    </style>
</head>
<body>
    <div class="container">
        <div class="left-pane">
            <ul id="task-list"></ul>
        </div>
        <div class="right-pane">
            <textarea id="task-input" placeholder="Enter a new task"></textarea>
        </div>
    </div>
</body>
<script>
    document.addEventListener('DOMContentLoaded', () => {
    const taskInput = document.getElementById('task-input');
    const taskList = document.getElementById('task-list');

    // Load tasks from local storage
    const tasks = JSON.parse(localStorage.getItem('tasks')) || [];
    tasks.forEach(task => addTaskToDOM(task));

    // Add task on Enter key press
    taskInput.addEventListener('keypress', (e) => {
        if (e.key === 'Enter') {
            const task = {
                id: Date.now(),
                name: taskInput.value,
                completed: false
            };
            tasks.push(task);
            localStorage.setItem('tasks', JSON.stringify(tasks));
            addTaskToDOM(task);
            taskInput.value = '';
        }
    });

    // Add task to DOM
    function addTaskToDOM(task) {
        const li = document.createElement('li');
        li.className = `task-item ${task.completed ? 'completed' : ''}`;
        li.dataset.id = task.id;
        li.innerHTML = `
            <input type="checkbox" ${task.completed ? 'checked' : ''}>
            <span>${task.name}</span>
            <button class="edit">✏️</button>
            <button class="delete">❌</button>
        `;
        taskList.appendChild(li);
    }

    // Handle task actions
    taskList.addEventListener('click', (e) => {
        const id = e.target.closest('.task-item').dataset.id;
        const task = tasks.find(task => task.id == id);

        if (e.target.classList.contains('delete')) {
            tasks.splice(tasks.indexOf(task), 1);
            localStorage.setItem('tasks', JSON.stringify(tasks));
            e.target.closest('.task-item').remove();
        } else if (e.target.classList.contains('edit')) {
            const newName = prompt('Update task name:', task.name);
            if (newName) {
                task.name = newName;
                localStorage.setItem('tasks', JSON.stringify(tasks));
                e.target.closest('.task-item').querySelector('span').textContent = newName;
            }
        } else if (e.target.type === 'checkbox') {
            task.completed = e.target.checked;
            localStorage.setItem('tasks', JSON.stringify(tasks));
            e.target.closest('.task-item').classList.toggle('completed', task.completed);
        }
    });
});

</script>
</html>
