<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Task Manager</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body {
            font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif; /* Minimalist font */
            margin: 10px;
            background-color: #f8f8f8;
            color: #333;
            transition: background-color 0.3s ease;
        }
        .tab-container {
            display: flex;
            overflow-x: auto;
            flex-wrap: nowrap;
            background-color: #fff;
            border-bottom: 1px solid #ddd;
            margin-bottom: 15px;
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
        }
        .tab-button {
            padding: 10px 12px;
            border: none;
            background-color: transparent;
            cursor: pointer;
            border-bottom: 2px solid transparent;
            font-weight: normal;
            color: #555;
            transition: color 0.3s ease, border-bottom-color 0.3s ease;
            white-space: nowrap;
            text-transform: uppercase; /* Uppercase tab text */
            font-size: 0.85em; /* Slightly smaller uppercase text */
        }
        .tab-button.active {
            border-bottom-color: #007bff;
            color: #007bff;
            font-weight: bold;
        }
        .tab-button:hover {
            color: #007bff;
        }
        .tab-content {
            padding: 15px;
            background-color: #fff;
            border: 1px solid #eee;
            border-radius: 5px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            margin-bottom: 15px;
            opacity: 0;
            transform: translateY(10px);
            transition: opacity 0.3s ease, transform 0.3s ease;
        }
        .tab-content.active {
            opacity: 1;
            transform: translateY(0);
        }
        .chart-container {
            width: 100%;
            max-width: 400px;
            height: auto;
            margin: 15px auto;
            position: relative;
        }
        .input-forms {
            padding: 15px;
            background-color: #fff;
            border: 1px solid #eee;
            border-radius: 5px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }
        .input-forms h3 {
            margin-top: 0;
            color: #333;
            font-weight: 500;
            border-bottom: 1px solid #ddd;
            padding-bottom: 8px;
            margin-bottom: 10px;
            font-size: 1.1em; /* Slightly smaller uppercase heading */
            text-transform: uppercase; /* Uppercase heading */
        }
        .input-forms label {
            display: block;
            margin-bottom: 3px;
            color: #555;
            font-weight: normal;
            font-size: 0.85em; /* Slightly smaller label */
        }
        .input-forms input[type="text"],
        .input-forms input[type="number"],
        .input-forms select {
            width: calc(100% - 12px);
            padding: 8px;
            margin-bottom: 8px;
            border: 1px solid #ccc;
            border-radius: 3px;
            box-sizing: border-box;
            font-size: 0.85em; /* Slightly smaller input text */
            transition: border-color 0.3s ease;
        }
        .input-forms input[type="text"]:focus,
        .input-forms input[type="number"]:focus,
        .input-forms select:focus {
            border-color: #007bff;
            outline: none;
            box-shadow: 0 0 5px rgba(0, 123, 255, 0.25);
        }
        .input-forms button {
            padding: 10px 15px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 3px;
            cursor: pointer;
            font-weight: 500;
            transition: background-color 0.3s ease, transform 0.2s ease, box-shadow 0.2s ease;
            font-size: 0.85em; /* Slightly smaller uppercase button text */
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
            text-transform: uppercase; /* Uppercase button text */
        }
        .input-forms button:hover {
            background-color: #0056b3;
            transform: scale(1.02);
            box-shadow: 0 3px 7px rgba(0, 0, 0, 0.15);
        }
        .task-list-container, .finished-task-container {
            margin-top: 15px;
            border: 1px solid #eee;
            padding: 10px;
            background-color: #fff;
            border-radius: 5px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }
        .task-list-container h3 {
            color: #333;
            font-weight: 500;
            border-bottom: 1px solid #ddd;
            padding-bottom: 8px;
            margin-bottom: 10px;
            font-size: 1em; /* Slightly smaller uppercase heading */
            text-transform: uppercase; /* Uppercase heading */
        }
        .finished-task-container h2 {
            color: #333;
            font-weight: 500;
            border-bottom: 1px solid #ddd;
            padding-bottom: 8px;
            margin-bottom: 10px;
            font-size: 1em; /* Slightly smaller uppercase heading */
            text-transform: uppercase; /* Uppercase heading */
        }
        .task-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 6px 0;
            border-bottom: 1px solid #eee;
            color: #555;
            font-size: 0.85em; /* Slightly smaller task item text */
            transition: background-color 0.3s ease;
        }
        .task-item:last-child {
            border-bottom: none;
        }
        .task-item:hover {
            background-color: #f9f9f9;
        }
        .task-actions button {
            margin-left: 5px;
            padding: 5px 8px;
            cursor: pointer;
            border: 1px solid #ccc;
            border-radius: 3px;
            background-color: transparent;
            color: #555;
            font-size: 0.75em; /* Even smaller uppercase action buttons */
            transition: background-color 0.3s ease, color 0.3s ease, transform 0.2s ease, box-shadow 0.2s ease;
            text-transform: uppercase; /* Uppercase action button text */
        }
        .task-actions button:hover {
            background-color: #ddd;
            color: #333;
            transform: scale(1.05);
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
        }
        .popup {
            position: fixed;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: #fff;
            border: 1px solid #eee;
            padding: 15px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.15);
            z-index: 1000;
            border-radius: 5px;
            width: 80%;
            max-width: 300px;
            opacity: 0;
            transform: translate(-50%, -50%) scale(0.9);
            transition: opacity 0.3s ease, transform 0.3s ease;
        }
        .popup.show {
            opacity: 1;
            transform: translate(-50%, -50%) scale(1);
        }
        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            z-index: 999;
            opacity: 0;
            transition: opacity 0.3s ease;
        }
        .overlay.show {
            opacity: 1;
        }
        .timer-container {
            margin-top: 10px;
            font-weight: bold;
            color: #333;
            font-size: 0.9em; /* Slightly smaller timer text */
            text-transform: uppercase; /* Uppercase timer label */
        }
        #currentTaskListDisplay {
            list-style-type: none;
            padding: 0;
        }
        #currentTaskListDisplay li {
            border-bottom: 1px solid #eee;
            padding: 6px 0;
            color: #555;
            font-size: 0.85em; /* Slightly smaller task list text */
        }
        #currentTaskListDisplay li:last-child {
            border-bottom: none;
        }
        #currentTaskListDisplay li span {
            font-weight: bold;
        }
        .popup h4 {
            margin-top: 0;
            color: #333;
            font-weight: 500;
            margin-bottom: 10px;
            font-size: 1em; /* Slightly smaller uppercase popup title */
            text-transform: uppercase; /* Uppercase popup title */
        }
        .popup-actions {
            margin-top: 10px;
            display: flex;
            flex-direction: column;
            align-items: stretch;
        }
        .popup-actions button {
            margin-left: 0;
            margin-bottom: 5px;
            padding: 8px 12px;
            cursor: pointer;
            border: 1px solid #ccc;
            border-radius: 3px;
            font-weight: 500;
            background-color: #eee;
            color: #333;
            transition: background-color 0.3s ease, color 0.3s ease, transform 0.2s ease, box-shadow 0.2s ease;
            font-size: 0.8em; /* Even smaller uppercase popup action buttons */
            text-transform: uppercase; /* Uppercase popup action button text */
        }
        .popup-actions button:last-child {
            margin-bottom: 0;
        }
        .popup-actions button:hover {
            background-color: #ddd;
            color: #333;
            transform: scale(1.02);
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.08);
        }

        /* Media query for smaller screens */
        @media (max-width: 600px) {
            body {
                margin: 8px;
            }
            .tab-container {
                margin-bottom: 10px;
            }
            .tab-button {
                padding: 8px 10px;
                font-size: 0.75em; /* Further reduce font size on very small screens */
            }
            .tab-content {
                padding: 10px;
                margin-bottom: 10px;
            }
            .input-forms h3 {
                font-size: 1em; /* Further reduce font size on very small screens */
                margin-bottom: 8px;
            }
            .input-forms label {
                font-size: 0.8em; /* Further reduce font size on very small screens */
            }
            .input-forms input[type="text"],
            .input-forms input[type="number"],
            .input-forms select {
                font-size: 0.8em; /* Further reduce font size on very small screens */
                padding: 6px;
                margin-bottom: 6px;
            }
            .input-forms button {
                font-size: 0.8em; /* Further reduce font size on very small screens */
                padding: 8px 12px;
            }
            .task-list-container h3, .finished-task-container h2 {
                font-size: 0.9em; /* Further reduce font size on very small screens */
                margin-bottom: 8px;
            }
            .task-item {
                font-size: 0.8em; /* Further reduce font size on very small screens */
                padding: 5px 0;
            }
            .task-actions button {
                font-size: 0.7em; /* Further reduce font size on very small screens */
                padding: 4px 6px;
            }
            .popup {
                padding: 10px;
            }
            .timer-container {
                font-size: 0.8em; /* Further reduce font size on very small screens */
            }
            #currentTaskListDisplay li {
                font-size: 0.8em; /* Further reduce font size on very small screens */
                padding: 5px 0;
            }
        }
    </style>
</head>
<body>

    <div class="tab-container">
        <button class="tab-button active" onclick="openTab('combinedChart')">Combined Chart</button>
        <button class="tab-button" onclick="openTab('taskPieChart')">Task Chart</button>
        <button class="tab-button" onclick="openTab('subtaskPieChart')">Sub-task Chart</button>
        <button class="tab-button" onclick="openTab('list')">Current Tasks</button>
        <button class="tab-button" onclick="openTab('input')">Input</button>
        <button class="tab-button" onclick="openTab('history')">History</button>
    </div>

    <div id="combinedChart" class="tab-content active">
        <h2>Combined Task and Sub-task Chart</h2>
        <div class="chart-container">
            <canvas id="combinedChartCanvas" width="400" height="400"></canvas>
        </div>
    </div>

    <div id="taskPieChart" class="tab-content">
        <h2>Task Chart</h2>
        <div class="chart-container">
            <canvas id="taskPieCanvas" width="400" height="400"></canvas>
        </div>
    </div>

    <div id="subtaskPieChart" class="tab-content">
        <h2>Sub-task Chart</h2>
        <div class="chart-container">
            <canvas id="subtaskPieCanvas" width="400" height="400"></canvas>
        </div>
    </div>

    <div id="list" class="tab-content">
        <h2>Current Tasks</h2>
        <ul id="currentTaskListDisplay">
            </ul>
    </div>

    <div id="input" class="tab-content">
        <h2>Input Tasks and Sub-tasks</h2>
        <div class="input-forms">
            <h3>Add New Task</h3>
            <label for="taskName">Task Name:</label>
            <input type="text" id="taskName"><br>
            <label for="taskHours">Task Duration (Hours):</label>
            <input type="number" id="taskHours" min="0" value="0">
            <label for="taskMinutes">Minutes:</label>
            <input type="number" id="taskMinutes" min="0" max="59" value="0"><br>
            <button onclick="addTask()">Add Task</button>

            <h3>Add Sub-task to Existing Task</h3>
            <label for="parentTask">Select Task:</label>
            <select id="parentTask"></select><br>
            <label for="subtaskName">Sub-task Name:</label>
            <input type="text" id="subtaskName"><br>
            <label for="subtaskHours">Sub-task Duration (Hours):</label>
            <input type="number" id="subtaskHours" min="0" value="0">
            <label for="subtaskMinutes">Minutes:</label>
            <input type="number" id="subtaskMinutes" min="0" max="59" value="0"><br>
            <button onclick="addSubtask()">Add Sub-task</button>
        </div>
    </div>

    <div id="history" class="tab-content">
        <h2>Finished Tasks</h2>
        <div class="finished-task-container">
            <ul id="finishedTasksList"></ul>
        </div>
    </div>

    <div class="task-list-container" style="display: none;">
        <h3>Current Tasks</h3>
        <ul id="currentTasksList"></ul>
    </div>

    <div id="popupContainer" style="display:none;">
        <div class="overlay"></div>
        <div class="popup">
            <h4 id="popupTitle"></h4>
            <p id="popupDetails"></p>
            <ul id="popupSubtaskList"></ul>
            <div id="timerDisplay" class="timer-container"></div>
            <div class="popup-actions">
                <button onclick="startTimer()">Start Timer</button>
                <button onclick="manualCompleteTask()">Complete</button>
                <button onclick="editSelectedItem()">Edit</button>
                <button onclick="cancelSelectedItem()">Cancel</button>
                <button onclick="closePopup()">Close</button>
            </div>
        </div>
    </div>

    <script>
        let tasks = [];
        let finishedTasks = [];
        let activeTimer = null;
        let timerInterval;
        let selectedChartItem = null; // To store the clicked task or subtask info
        let taskPieChartInstance;
        let subtaskPieChartInstance;
        let combinedChartInstance; // New instance for combined chart
        const softPalette = ['#e0e0e0', '#a6a6a6', '#f0f0f0', '#d1d1d1', '#c2c2c2', '#8c8c8c'];
        const blankSubtaskColor = '#777'; // Darker grey for "Blank" subtask
        const tabContents = document.querySelectorAll('.tab-content');
        const popupContainer = document.getElementById('popupContainer');
        const overlay = popupContainer.querySelector('.overlay');
        const popup = popupContainer.querySelector('.popup');

        // Placeholder data for testing (durations in minutes)
        tasks.push({
            id: 1,
            name: 'Task A',
            duration: 30,
            subtasks: [
                { id: 11, name: 'Subtask A.1', duration: 15, completed: false },
                { id: 12, name: 'Subtask A.2', duration: 20, completed: false },
                { id: 13, name: 'Blank', duration: -5, completed: false } // Will be recalculated
            ],
            completed: false
        });
        tasks.forEach(task => {
            updateBlankSubtask(task);
        });

        function openTab(tabId) {
            tabContents.forEach(tab => {
                tab.classList.remove('active');
                tab.style.display = 'none';
            });
            const buttons = document.querySelectorAll('.tab-button');
            buttons.forEach(button => button.classList.remove('active'));
            const targetTab = document.getElementById(tabId);
            if (targetTab) {
                targetTab.style.display = 'block';
                setTimeout(() => targetTab.classList.add('active'), 0); // Add active class after display is set
            }
            document.querySelector(`.tab-button[onclick="openTab('${tabId}')"]`).classList.add('active');

            if (tabId === 'list') {
                updateCurrentTaskListDisplay();
            } else if (tabId === 'taskPieChart') {
                renderTaskPieChart();
            } else if (tabId === 'subtaskPieChart') {
                renderSubtaskPieChart();
            } else if (tabId === 'combinedChart') {
                renderCombinedChart();
            }
        }

        function updateCurrentTaskListDisplay() {
            const currentTaskListDisplay = document.getElementById('currentTaskListDisplay');
            currentTaskListDisplay.innerHTML = '';
            tasks.forEach(task => {
                const totalDuration = calculateTotalDuration(task);
                const listItem = document.createElement('li');
                listItem.classList.add('task-item'); // Ensure task-item class is added
                listItem.innerHTML = `<span>${task.name.toUpperCase()}</span> (${formatTime(totalDuration)})`; // Uppercase task name in list
                if (task.subtasks && task.subtasks.length > 0) {
                    const subtaskList = document.createElement('ul');
                    task.subtasks.forEach(subtask => {
                        const sublistItem = document.createElement('li');
                        sublistItem.textContent = `- ${subtask.name.toUpperCase()} (${formatTime(subtask.duration)})`; // Uppercase subtask name
                        subtaskList.appendChild(sublistItem);
                    });
                    listItem.appendChild(subtaskList);
                }
                currentTaskListDisplay.appendChild(listItem);
            });
            if (tasks.length === 0) {
                currentTaskListDisplay.innerHTML = '<p>No current tasks.</p>';
            }
        }

        function updateTaskListDropdown() {
            const parentTaskDropdown = document.getElementById('parentTask');
            parentTaskDropdown.innerHTML = '<option value="">-- Select a Task --</option>';
            tasks.forEach((task, index) => {
                parentTaskDropdown.innerHTML += `<option value="${index}">${task.name.toUpperCase()}</option>`; // Uppercase in dropdown
            });
        }

        function addTask() {
            const taskNameInput = document.getElementById('taskName');
            const taskHoursInput = document.getElementById('taskHours');
            const taskMinutesInput = document.getElementById('taskMinutes');

            const name = taskNameInput.value.trim();
            const hours = parseInt(taskHoursInput.value) || 0;
            const minutes = parseInt(taskMinutesInput.value) || 0;
            const totalMinutes = hours * 60 + minutes;

            if (name) {
                const newTask = {
                    id: Date.now(),
                    name: name,
                    duration: totalMinutes,
                    subtasks: [],
                    completed: false
                };
                newTask.subtasks.push({ id: Date.now() + 1, name: 'Blank', duration: totalMinutes, completed: false });
                tasks.push(newTask);
                taskNameInput.value = '';
                taskHoursInput.value = '0';
                taskMinutesInput.value = '0';
                updateTaskListDropdown();
                updateCurrentTaskListDisplay();
                renderTaskLists();
                renderTaskPieChart();
                renderSubtaskPieChart();
                renderCombinedChart(); // Update combined chart
            } else {
                alert('Please enter a task name.');
            }
        }

        function addSubtask() {
            const parentTaskDropdown = document.getElementById('parentTask');
            const subtaskNameInput = document.getElementById('subtaskName');
            const subtaskHoursInput = document.getElementById('subtaskHours');
            const subtaskMinutesInput = document.getElementById('subtaskMinutes');

            const parentIndex = parentTaskDropdown.value;
            const name = subtaskNameInput.value.trim();
            const hours = parseInt(subtaskHoursInput.value) || 0;
            const minutes = parseInt(subtaskMinutesInput.value) || 0;
            const totalMinutes = hours * 60 + minutes;

            if (parentIndex !== '' && name && totalMinutes > 0) {
                tasks[parentIndex].subtasks.push({
                    id: Date.now(),
                    name: name,
                    duration: totalMinutes,
                    completed: false
                });
                updateBlankSubtask(tasks[parentIndex]);
                subtaskNameInput.value = '';
                subtaskHoursInput.value = '0';
                subtaskMinutesInput.value = '0';
                updateCurrentTaskListDisplay();
                renderTaskLists();
                renderSubtaskPieChart();
                renderCombinedChart(); // Update combined chart
            } else {
                alert('Please select a parent task, enter a sub-task name, and a valid duration.');
            }
        }

        function updateBlankSubtask(task) {
            const blankSubtask = task.subtasks.find(sub => sub.name === 'Blank');
            if (blankSubtask) {
                const otherSubtasksDuration = task.subtasks
                    .filter(sub => sub.name !== 'Blank')
                    .reduce((sum, sub) => sum + sub.duration, 0);
                blankSubtask.duration = Math.max(0, task.duration - otherSubtasksDuration);
            } else {
                const otherSubtasksDuration = task.subtasks.reduce((sum, sub) => sum + sub.duration, 0);
                task.subtasks.push({ id: Date.now() + Math.random(), name: 'Blank', duration: Math.max(0, task.duration - otherSubtasksDuration), completed: false });
            }
        }

        function calculateTotalDuration(task) {
            let total = task.duration;
            if (task.subtasks && task.subtasks.length > 0) {
                total += task.subtasks.reduce((sum, subtask) => sum + subtask.duration, 0);
            }
            return total;
        }

        function renderTaskPieChart() {
            const taskPieCanvas = document.getElementById('taskPieCanvas');
            const ctx = taskPieCanvas.getContext('2d');

            if (taskPieChartInstance) {
                taskPieChartInstance.destroy();
            }

            const labels = tasks.map(task => `${task.name.toUpperCase()} (${formatTime(task.duration)})`); // Uppercase labels
            const data = tasks.map(task => task.duration);
            const backgroundColors = tasks.map((_, index) => softPalette[index % softPalette.length]);

            taskPieChartInstance = new Chart(ctx, {
                type: 'pie',
                data: {
                    labels: labels,
                    datasets: [{
                        data: data,
                        backgroundColor: backgroundColors,
                        borderColor: '#f8f8f8', // Soft outline color (light grey - same as body background)
                        borderWidth: 1,
                        hoverOffset: 4
                    }]
                },
                options: {
                    responsive: true, // Enable responsiveness
                    maintainAspectRatio: false, // Allow chart to scale freely
                    plugins: {
                        legend: {
                            display: false,
                            position: 'bottom'
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return `${context.label}: ${formatTime(context.parsed)}`;
                                }
                            }
                        }
                    },
                    onClick: function(event, elements) {
                        if (elements.length > 0) {
                            const clickedIndex = elements[0].index;
                            const task = tasks[clickedIndex];
                            selectedChartItem = { type: 'task', data: task };
                            showTaskDetailsPopup(task);
                        }
                    }
                }
            });
        }

        function renderSubtaskPieChart() {
            const subtaskPieCanvas = document.getElementById('subtaskPieCanvas');
            const ctx = subtaskPieCanvas.getContext('2d');

            if (subtaskPieChartInstance) {
                subtaskPieChartInstance.destroy();
            }

            const subtaskLabels = [];
            const subtaskData = [];
            const subtaskBackgroundColors = [];

            tasks.forEach(task => {
                task.subtasks.forEach(subtask => {
                    subtaskLabels.push(`${task.name.toUpperCase()}: ${subtask.name.toUpperCase()} (${formatTime(subtask.duration)})`); // Uppercase labels
                    subtaskData.push(subtask.duration);
                    subtaskBackgroundColors.push(subtask.name === 'Blank' ? blankSubtaskColor : softPalette[tasks.findIndex(t => t.subtasks.some(st => st.id === subtask.id)) % softPalette.length]);
                });
            });

            subtaskPieChartInstance = new Chart(ctx, {
                type: 'pie',
                data: {
                    labels: subtaskLabels,
                    datasets: [{
                        data: subtaskData,
                        backgroundColor: subtaskBackgroundColors,
                        borderColor: '#f8f8f8', // Soft outline color
                        borderWidth: 1,
                        hoverOffset: 4
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: {
                            display: false,
                            position: 'bottom'
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return `${context.label}: ${formatTime(context.parsed)}`;
                                }
                            }
                        }
                    },
                    onClick: function(event, elements) {
                        if (elements.length > 0) {
                            const clickedIndex = elements[0].index;
                            let currentIndex = -1;
                            let clickedSubtask, parentTask;
                            for (const task of tasks) {
                                for (const subtask of task.subtasks) {
                                    currentIndex++;
                                    if (currentIndex === clickedIndex) {
                                        clickedSubtask = subtask;
                                        parentTask = task;
                                        break;
                                    }
                                }
                                if (clickedSubtask) break;
                            }
                            selectedChartItem = { type: 'subtask', data: clickedSubtask, parent: parentTask };
                            showTaskDetailsPopup({ parentTask: parentTask, subtask: clickedSubtask });
                        }
                    }
                }
            });
        }

        function renderCombinedChart() {
            const combinedChartCanvas = document.getElementById('combinedChartCanvas');
            const ctx = combinedChartCanvas.getContext('2d');

            if (combinedChartInstance) {
                combinedChartInstance.destroy();
            }

            const orderedSubtasksData = [];
            tasks.forEach(task => {
                if (task.subtasks) {
                    orderedSubtasksData.push(...task.subtasks);
                }
            });

            const subtaskLabels = orderedSubtasksData.map(subtask => `${findParentTaskName(subtask.id).toUpperCase()}: ${subtask.name.toUpperCase()} (${formatTime(subtask.duration)})`); // Uppercase labels
            const subtaskData = orderedSubtasksData.map(subtask => subtask.duration);
            const subtaskBackgroundColors = orderedSubtasksData.map(subtask =>
                subtask.name === 'Blank' ? blankSubtaskColor : softPalette[tasks.findIndex(task => task.subtasks.some(st => st.id === subtask.id)) % softPalette.length]
            );

            const taskLabels = tasks.map(task => `${task.name.toUpperCase()} (${formatTime(calculateSubtaskTotalDuration(task))})`); // Uppercase labels
            const taskData = tasks.map(task => calculateSubtaskTotalDuration(task));
            const taskBackgroundColors = tasks.map((_, index) => softPalette[index % softPalette.length]);

            combinedChartInstance = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: [...subtaskLabels, ...taskLabels], // Subtasks now the inner part
                    datasets: [{
                        label: 'Sub-tasks',
                        data: subtaskData,
                        backgroundColor: subtaskBackgroundColors,
                        borderColor: '#f8f8f8', // Soft outline color
                        borderWidth: 1,
                        hoverOffset: 4,
                        weight: 0.7 // Make sub-tasks appear in the back (inner)
                    }, {
                        label: 'Tasks (based on subtask time)',
                        data: taskData,
                        backgroundColor: taskBackgroundColors,
                        borderColor: '#f8f8f8', // Soft outline color
                        borderWidth: 1,
                        hoverOffset: 4,
                        weight: 0.3 // Make tasks appear smaller (outer)
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    cutout: '40%', // Adjust for visual balance
                    plugins: {
                        legend: {
                            position: 'bottom',
                            align: 'start',
                            labels: {
                                boxWidth: 20,
                                padding: 10,
                                font: {
                                    size: 12
                                },
                                generateLabels: function(chart) {
                                    const original = Chart.defaults.plugins.legend.labels.generateLabels;
                                    const labelsOriginal = original.call(this, chart);
                                    return labelsOriginal.map(label => {
                                        if (label.text.includes('Blank')) {
                                            label.fillStyle = blankSubtaskColor;
                                            label.strokeStyle = blankSubtaskColor;
                                        }
                                        return label;
                                    });
                                }
                            }
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return `${context.dataset.label.includes('Task') ? context.label : context.label}: ${formatTime(context.parsed)}`;
                                }
                            }
                        }
                    },
                    onClick: function(event, elements) {
                        if (elements.length > 0) {
                            const clickedElement = elements[0];
                            const datasetIndex = clickedElement.datasetIndex;
                            const index = clickedElement.index;

                            if (datasetIndex === 0) { // Clicked on a Sub-task
                                const clickedSubtask = orderedSubtasksData[index];
                                const parentTask = tasks.find(task => task.subtasks.some(st => st.id === clickedSubtask.id));
                                selectedChartItem = { type: 'subtask', data: clickedSubtask, parent: parentTask };
                                showTaskDetailsPopup({ parentTask: parentTask, subtask: clickedSubtask });
                            } else if (datasetIndex === 1) { // Clicked on a Task
                                const taskIndex = index - orderedSubtasksData.length;
                                if (taskIndex >= 0 && taskIndex < tasks.length) {
                                    const task = tasks[taskIndex];
                                    selectedChartItem = { type: 'task', data: task };
                                    showTaskDetailsPopup(task);
                                }
                            }
                        }
                    }
                }
            });
        }

        function calculateSubtaskTotalDuration(task) {
            if (task.subtasks && task.subtasks.length > 0) {
                return task.subtasks.reduce((sum, subtask) => sum + subtask.duration, 0);
            } else {
                return task.duration; // If no subtasks, use the task's original duration
            }
        }

        function findParentTaskName(subtaskId) {
            for (const task of tasks) {
                if (task.subtasks && task.subtasks.some(sub => sub.id === subtaskId)) {
                    return task.name;
                }
            }
            return 'Unknown Task';
        }

        function getShade(color, index) {
            const numShades = 5; // Adjust as needed
            const factor = 0.1 * (index % numShades);
            let R = parseInt(color.substring(1, 3), 16);
            let G = parseInt(color.substring(3, 5), 16);
            let B = parseInt(color.substring(5, 7), 16);

            R = Math.max(0, Math.min(255, Math.round(R * (1 - factor))));
            G = Math.max(0, Math.min(255, Math.round(G * (1 - factor))));
            B = Math.max(0, Math.min(255, Math.round(B * (1 - factor))));

            const RR = ((R.toString(16).length === 1) ? "0" + R.toString(16) : R.toString(16));
            const GG = ((G.toString(16).length === 1) ? "0" + GG.toString(16) : GG.toString(16));
            const BB = ((B.toString(16).length === 1) ? "0" + BB.toString(16) : BB.toString(16));

            return "#" + RR + GG + BB;
        }

        function renderTaskLists() {
            const currentTasksList = document.getElementById('currentTasksList');
            const finishedTasksList = document.getElementById('finishedTasksList');
            currentTasksList.innerHTML = '';
            finishedTasksList.innerHTML = '';

            tasks.forEach(task => {
                if (!task.completed) {
                    const listItem = document.createElement('li');
                    listItem.classList.add('task-item');
                    listItem.innerHTML = `
                        <span>${task.name.toUpperCase()} (${formatTime(calculateTotalDuration(task))})</span>
                        <div class="task-actions">
                            <button onclick="editTask(${task.id})">Edit</button>
                            <button onclick="completeTask(${task.id}, false, this.parentNode.parentNode)">Complete</button>
                            <button onclick="cancelTask(${task.id})">Cancel</button>
                        </div>
                    `;
                    currentTasksList.appendChild(listItem);
                }
            });

            finishedTasks.forEach(task => {
                const listItem = document.createElement('li');
                listItem.classList.add('task-item');
                listItem.textContent = `${task.name.toUpperCase()} (${formatTime(calculateTotalDuration(task))})`;
                finishedTasksList.appendChild(listItem);
            });
        }

        function editTask(taskId) {
            alert(`Editing task with ID: ${taskId} (functionality not fully implemented)`);
        }

        function manualCompleteTask() {
            if (selectedChartItem) {
                completeTask(selectedChartItem.data.id, false);
                closePopup();
            }
        }

        function editSelectedItem() {
            if (selectedChartItem) {
                alert(`Editing ${selectedChartItem.type} with ID: ${selectedChartItem.data.id} (functionality not fully implemented)`);
            }
        }

        function cancelSelectedItem() {
            if (selectedChartItem) {
                cancelTask(selectedChartItem.data.id);
                closePopup();
            }
        }

        function completeTask(itemId, fromTimer = false, listItemElement = null) {
            const taskIndex = tasks.findIndex(task => task.id === itemId);
            if (taskIndex !== -1) {
                const completedTask = tasks.splice(taskIndex, 1)[0];
                completedTask.completed = true;
                finishedTasks.push(completedTask);
                if (listItemElement) {
                    listItemElement.classList.add('fade-out');
                    setTimeout(() => renderTaskLists(), 300); // Re-render after animation
                } else {
                    renderTaskLists();
                }
                updateCurrentTaskListDisplay();
                renderTaskPieChart();
                renderSubtaskPieChart();
                renderCombinedChart(); // Update combined chart
            } else {
                tasks.forEach((task, taskIndex) => {
                    const subtaskIndex = task.subtasks.findIndex(sub => sub.id === itemId);
                    if (subtaskIndex !== -1) {
                        const completedSubtask = task.subtasks.splice(subtaskIndex, 1)[0];
                        completedSubtask.completed = true;
                        renderSubtaskPieChart();
                        renderCombinedChart(); // Update combined chart

                        // Check if all non-"Blank" subtasks of the parent task are complete
                        const nonBlankSubtasks = task.subtasks.filter(sub => sub.name !== 'Blank');
                        if (nonBlankSubtasks.length > 0 && nonBlankSubtasks.every(sub => sub.completed)) {
                            const parentTask = tasks.splice(taskIndex, 1)[0];
                            parentTask.completed = true;
                            finishedTasks.push(parentTask);
                            renderTaskLists();
                            updateCurrentTaskListDisplay();
                            renderTaskPieChart();
                        } else if (nonBlankSubtasks.length === 0 && task.subtasks.some(sub => sub.name === 'Blank')) {
                            const parentTask = tasks.splice(taskIndex, 1)[0];
                            parentTask.completed = true;
                            finishedTasks.push(parentTask);
                            renderTaskLists();
                            updateCurrentTaskListDisplay();
                            renderTaskPieChart();
                        }
                        return; // Exit the loop once the subtask is found and processed
                    }
                });
            }
            updateCurrentTaskListDisplay();
        }

        function findTaskById(taskId) {
            const task = tasks.find(t => t.id === taskId);
            if (task) return task;
            for (const t of tasks) {
                const subtask = t.subtasks.find(st => st.id === taskId);
                if (subtask) return { parentTask: t, subtask: subtask };
            }
            return null;
        }

        function cancelTask(taskId) {
            const taskIndex = tasks.findIndex(task => task.id === taskId);
            if (taskIndex !== -1) {
                tasks.splice(taskIndex, 1);
                renderTaskLists();
                updateCurrentTaskListDisplay();
                renderTaskPieChart();
                renderSubtaskPieChart();
                renderCombinedChart(); // Update combined chart
            } else {
                tasks.forEach(task => {
                    const initialTaskDuration = task.duration;
                    const removedSubtask = task.subtasks.find(sub => sub.id === taskId);
                    task.subtasks = task.subtasks.filter(sub => sub.id !== taskId);
                    if (removedSubtask) {
                        updateBlankSubtask(task);
                    }
                });
                renderTaskLists();
                updateCurrentTaskListDisplay();
                renderSubtaskPieChart();
                renderCombinedChart(); // Update combined chart
            }
        }

        function showTaskDetailsPopup(item) {
            let title = '';
            let details = '';
            let subtasksToShow = [];
            let itemId;
            let totalDurationMinutes;

            if (item && item.subtask) {
                title = item.subtask.name.toUpperCase(); // Uppercase title
                totalDurationMinutes = item.subtask.duration;
                details = `Duration: ${formatTime(totalDurationMinutes)}`;
                itemId = item.subtask.id;
            } else if (item) {
                title = item.name.toUpperCase(); // Uppercase title
                totalDurationMinutes = calculateTotalDuration(item);
                details = `Total Duration: ${formatTime(totalDurationMinutes)}`;
                subtasksToShow = item.subtasks;
                itemId = item.id;
            }

            document.getElementById('popupTitle').textContent = title;
            document.getElementById('popupDetails').textContent = details;

            const subtaskList = document.getElementById('popupSubtaskList');
            subtaskList.innerHTML = '';
            if (subtasksToShow && subtasksToShow.length > 0) {
                subtaskList.innerHTML = '<strong>Sub-tasks:</strong><ul>';
                subtasksToShow.forEach(subtask => {
                    subtaskList.innerHTML += `<li>${subtask.name.toUpperCase()} (${formatTime(subtask.duration)})</li>`; // Uppercase subtask name
                });
                subtaskList.innerHTML += '</ul>';
            } else if (item && item.subtask) {
                // No sub-tasks to show for a sub-task
            } else {
                subtaskList.innerHTML = 'No sub-tasks for this task.';
            }

            document.getElementById('timerDisplay').textContent = '';
            selectedTaskOrSubtask = { type: item && item.subtask ? 'subtask' : 'task', id: itemId, duration: totalDurationMinutes * 60 };

            popupContainer.style.display = 'block';
            setTimeout(() => {
                popup.classList.add('show');
                overlay.classList.add('show');
            }, 0);

            if (activeTimer && activeTimer.id === itemId) {
                document.getElementById('timerDisplay').textContent = `Timer running: ${formatTime(activeTimer.remainingTime / 60)}`;
            }
        }

        function showPopup(title, details, type, id, duration) {
            document.getElementById('popupTitle').textContent = title.toUpperCase(); // Uppercase title
            document.getElementById('popupDetails').textContent = details;
            document.getElementById('popupSubtaskList').innerHTML = '';
            document.getElementById('timerDisplay').textContent = '';
            selectedTaskOrSubtask = { type: type, id: id, duration: duration };
            popupContainer.style.display = 'block';
            setTimeout(() => {
                popup.classList.add('show');
                overlay.classList.add('show');
            }, 0);
            if (activeTimer && activeTimer.id === id) {
                document.getElementById('timerDisplay').textContent = `Timer running: ${formatTime(activeTimer.remainingTime / 60)}`;
            }
        }

        function closePopup() {
            popup.classList.remove('show');
            overlay.classList.remove('show');
            setTimeout(() => popupContainer.style.display = 'none', 300);
            if (timerInterval) {
                clearInterval(timerInterval);
                timerInterval = null;
            }
            selectedTaskOrSubtask = null;
            selectedChartItem = null;
        }

        function startTimer() {
            if (!selectedTaskOrSubtask) return;

            let durationInSeconds = selectedTaskOrSubtask.duration;
            let itemId = selectedTaskOrSubtask.id;
            let itemName = '';

            const taskInfo = findTaskById(itemId);
            if (taskInfo) {
                itemName = taskInfo.subtask ? taskInfo.subtask.name : taskInfo.name;
            } else if (selectedChartItem && selectedChartItem.type === 'task') {
                itemName = selectedChartItem.data.name;
            } else if (selectedChartItem && selectedChartItem.type === 'subtask') {
                itemName = selectedChartItem.data.name;
            }

            if (durationInSeconds <= 0) return;

            const startTime = Date.now();
            const endTime = startTime + durationInSeconds * 1000;
            let remainingTime = durationInSeconds;

            if (activeTimer) {
                clearInterval(timerInterval);
            }

            activeTimer = {
                id: itemId,
                endTime: endTime,
                remainingTime: remainingTime
            };

            timerInterval = setInterval(function() {
                const now = Date.now();
                const difference = Math.max(0, endTime - now);
                remainingTime = Math.floor(difference / 1000);
                activeTimer.remainingTime = remainingTime;

                document.getElementById('timerDisplay').textContent = `Time Remaining: ${formatTime(remainingTime / 60)}`; // Uppercase timer label

                if (difference <= 0) {
                    clearInterval(timerInterval);
                    timerInterval = null;
                    activeTimer = null;
                    alert(`${itemName.toUpperCase()} COMPLETED!`); // Uppercase alert
                    completeTask(itemId, true);
                    closePopup();
                }
            }, 1000);
        }

        function startTimerForSubtask(subtaskId, subtaskName, durationMinutes) {
            showPopup(subtaskName, `Duration: ${formatTime(durationMinutes)}`, 'subtask', subtaskId, durationMinutes * 60);
            startTimer();
        }

        function formatTime(totalMinutes) {
            const hours = Math.floor(totalMinutes / 60);
            const minutes = Math.floor(totalMinutes % 60);
            const seconds = Number.isInteger(totalMinutes) ? 0 : Math.floor((totalMinutes - Math.floor(totalMinutes)) * 60);
            if (seconds > 0) {
                return `${hours}H ${minutes}M ${seconds}S`; // Uppercase time units
            } else {
                return `${hours}H ${minutes}M`; // Uppercase time units
            }
        }

        // Initial rendering
        updateTaskListDropdown();
        updateCurrentTaskListDisplay();
        renderTaskLists();
        openTab('combinedChart'); // Default to the combined chart tab

        // Add fade-out animation class
        const styleSheet = document.createElement("style");
        styleSheet.type = "text/css";
        styleSheet.innerText = `
            .task-item.fade-out {
                opacity: 0;
                transform: translateX(-20px);
                transition: opacity 0.3s ease-in-out, transform 0.3s ease-in-out;
            }
        `;
        document.head.appendChild(styleSheet);
    </script>

</body>
</html>
