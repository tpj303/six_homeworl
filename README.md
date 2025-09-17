<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TodoList 待辦事項</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }

        .container {
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            max-width: 500px;
            width: 100%;
            padding: 30px;
        }

        .header {
            text-align: center;
            margin-bottom: 30px;
        }

        .header h1 {
            color: #333;
            font-size: 2.5rem;
            margin-bottom: 10px;
        }

        .add-todo {
            display: flex;
            margin-bottom: 30px;
            gap: 10px;
        }

        .add-todo input {
            flex: 1;
            padding: 15px;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            font-size: 16px;
            outline: none;
            transition: border-color 0.3s;
        }

        .add-todo input:focus {
            border-color: #667eea;
        }

        .add-todo button {
            padding: 15px 25px;
            background: #667eea;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            cursor: pointer;
            transition: background-color 0.3s;
        }

        .add-todo button:hover {
            background: #5a67d8;
        }

        .filter-tabs {
            display: flex;
            margin-bottom: 20px;
            border-radius: 8px;
            overflow: hidden;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }

        .filter-tab {
            flex: 1;
            padding: 12px;
            background: #f8f9fa;
            border: none;
            cursor: pointer;
            font-size: 14px;
            transition: all 0.3s;
            border-right: 1px solid #e0e0e0;
        }

        .filter-tab:last-child {
            border-right: none;
        }

        .filter-tab.active {
            background: #667eea;
            color: white;
        }

        .filter-tab:hover {
            background: #e9ecef;
        }

        .filter-tab.active:hover {
            background: #5a67d8;
        }

        .todo-list {
            min-height: 200px;
            margin-bottom: 20px;
        }

        .todo-item {
            display: flex;
            align-items: center;
            padding: 15px;
            border: 1px solid #e0e0e0;
            border-radius: 8px;
            margin-bottom: 10px;
            background: white;
            transition: all 0.3s;
        }

        .todo-item:hover {
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
        }

        .todo-item.completed {
            opacity: 0.7;
            background: #f8f9fa;
        }

        .todo-item.completed .todo-text {
            text-decoration: line-through;
            color: #6c757d;
        }

        .todo-checkbox {
            margin-right: 15px;
            width: 20px;
            height: 20px;
            cursor: pointer;
        }

        .todo-text {
            flex: 1;
            font-size: 16px;
            color: #333;
        }

        .delete-btn {
            background: #dc3545;
            color: white;
            border: none;
            border-radius: 6px;
            padding: 8px 12px;
            cursor: pointer;
            font-size: 12px;
            transition: background-color 0.3s;
        }

        .delete-btn:hover {
            background: #c82333;
        }

        .footer {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding-top: 20px;
            border-top: 1px solid #e0e0e0;
        }

        .todo-count {
            color: #6c757d;
            font-size: 14px;
        }

        .clear-completed {
            background: #28a745;
            color: white;
            border: none;
            border-radius: 6px;
            padding: 10px 15px;
            cursor: pointer;
            font-size: 14px;
            transition: background-color 0.3s;
        }

        .clear-completed:hover {
            background: #218838;
        }

        .clear-completed:disabled {
            background: #6c757d;
            cursor: not-allowed;
        }

        .empty-state {
            text-align: center;
            padding: 40px 20px;
            color: #6c757d;
        }

        .empty-state i {
            font-size: 3rem;
            margin-bottom: 15px;
            opacity: 0.5;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>📝 TodoList</h1>
            <p>管理你的待辦事項</p>
        </div>

        <div class="add-todo">
            <input type="text" id="todoInput" placeholder="輸入新的待辦事項..." maxlength="100">
            <button onclick="addTodo()">新增</button>
        </div>

        <div class="filter-tabs">
            <button class="filter-tab active" onclick="filterTodos('all')">全部</button>
            <button class="filter-tab" onclick="filterTodos('completed')">完成</button>
            <button class="filter-tab" onclick="filterTodos('pending')">未完成</button>
        </div>

        <div class="todo-list" id="todoList">
            <div class="empty-state">
                <div>📋</div>
                <p>還沒有待辦事項，開始新增一些吧！</p>
            </div>
        </div>

        <div class="footer">
            <span class="todo-count" id="todoCount">剩餘 0 項未完成</span>
            <button class="clear-completed" id="clearCompleted" onclick="clearCompleted()" disabled>
                清除已完成
            </button>
        </div>
    </div>

    <script>
        // 待辦事項數據
        let todos = [];
        let currentFilter = 'all';

        // 載入頁面時初始化
        document.addEventListener('DOMContentLoaded', function() {
            loadTodos();
            renderTodos();
            
            // 監聽 Enter 鍵新增待辦事項
            document.getElementById('todoInput').addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    addTodo();
                }
            });
        });

        // 新增待辦事項
        function addTodo() {
            const input = document.getElementById('todoInput');
            const text = input.value.trim();
            
            if (text === '') {
                alert('請輸入待辦事項內容！');
                return;
            }

            const todo = {
                id: Date.now(),
                text: text,
                completed: false,
                createdAt: new Date().toLocaleString()
            };

            todos.unshift(todo);
            input.value = '';
            saveTodos();
            renderTodos();
        }

        // 切換待辦事項完成狀態
        function toggleTodo(id) {
            const todo = todos.find(t => t.id === id);
            if (todo) {
                todo.completed = !todo.completed;
                saveTodos();
                renderTodos();
            }
        }

        // 刪除待辦事項
        function deleteTodo(id) {
            if (confirm('確定要刪除這個待辦事項嗎？')) {
                todos = todos.filter(t => t.id !== id);
                saveTodos();
                renderTodos();
            }
        }

        // 篩選待辦事項
        function filterTodos(filter) {
            currentFilter = filter;
            
            // 更新篩選按鈕樣式
            document.querySelectorAll('.filter-tab').forEach(tab => {
                tab.classList.remove('active');
            });
            event.target.classList.add('active');
            
            renderTodos();
        }

        // 清除已完成的待辦事項
        function clearCompleted() {
            if (confirm('確定要清除所有已完成的待辦事項嗎？')) {
                todos = todos.filter(t => !t.completed);
                saveTodos();
                renderTodos();
            }
        }

        // 渲染待辦事項列表
        function renderTodos() {
            const todoList = document.getElementById('todoList');
            const todoCount = document.getElementById('todoCount');
            const clearBtn = document.getElementById('clearCompleted');
            
            // 根據當前篩選條件過濾待辦事項
            let filteredTodos = todos;
            switch (currentFilter) {
                case 'completed':
                    filteredTodos = todos.filter(t => t.completed);
                    break;
                case 'pending':
                    filteredTodos = todos.filter(t => !t.completed);
                    break;
                default:
                    filteredTodos = todos;
            }

            // 如果沒有待辦事項，顯示空狀態
            if (filteredTodos.length === 0) {
                let emptyMessage = '';
                switch (currentFilter) {
                    case 'completed':
                        emptyMessage = '還沒有已完成的待辦事項';
                        break;
                    case 'pending':
                        emptyMessage = '太棒了！沒有未完成的待辦事項';
                        break;
                    default:
                        emptyMessage = '還沒有待辦事項，開始新增一些吧！';
                }
                
                todoList.innerHTML = `
                    <div class="empty-state">
                        <div>📋</div>
                        <p>${emptyMessage}</p>
                    </div>
                `;
            } else {
                // 渲染待辦事項
                todoList.innerHTML = filteredTodos.map(todo => `
                    <div class="todo-item ${todo.completed ? 'completed' : ''}">
                        <input 
                            type="checkbox" 
                            class="todo-checkbox" 
                            ${todo.completed ? 'checked' : ''}
                            onchange="toggleTodo(${todo.id})"
                        >
                        <span class="todo-text">${escapeHtml(todo.text)}</span>
                        <button class="delete-btn" onclick="deleteTodo(${todo.id})">刪除</button>
                    </div>
                `).join('');
            }

            // 更新計數和清除按鈕
            const pendingCount = todos.filter(t => !t.completed).length;
            const completedCount = todos.filter(t => t.completed).length;
            
            todoCount.textContent = `剩餘 ${pendingCount} 項未完成`;
            clearBtn.disabled = completedCount === 0;
        }

        // HTML 轉義函數
        function escapeHtml(text) {
            const div = document.createElement('div');
            div.textContent = text;
            return div.innerHTML;
        }

        // 儲存待辦事項到 localStorage
        function saveTodos() {
            // 注意：在 Claude.ai 環境中 localStorage 不可用，這裡僅作為示例代碼
            try {
                localStorage.setItem('todos', JSON.stringify(todos));
            } catch (e) {
                console.log('無法使用 localStorage，資料將在頁面重新載入後遺失');
            }
        }

        // 從 localStorage 載入待辦事項
        function loadTodos() {
            try {
                const saved = localStorage.getItem('todos');
                if (saved) {
                    todos = JSON.parse(saved);
                }
            } catch (e) {
                console.log('無法從 localStorage 載入資料');
                todos = [];
            }
        }
    </script>
</body>
</html>
