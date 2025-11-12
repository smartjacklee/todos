# Todo 앱 기능 및 요구사항
_Exported on 11/12/2025 at 12:42:43 GMT+9 from Cursor (2.0.69)_

---

**User**

매일 해야 할 일을 기록하는 Todo 앱을 만들고 싶어.



필요한 기능

- 할 일 입력하고 ‘추가’ 버튼을 클릭하면 목록에 추가

- 항목에는 체크박스가 있어서 체크박스를 클릭하면 내용 위로 취소선 표시

- 항목에는 ‘삭제’ 버튼이 있어서 삭제 버튼을 클릭하면 항목 삭제

- 앱을 다시 시작해도 기존 내용이 유지되도록 하기

사용 언어/프레임워크

- HTML, CSS, 바닐라 자바스크립트

---

**Cursor**


프로젝트 구조 확인 중. 이후 HTML, CSS, JavaScript로 Todo 앱을 구현합니다.



Todo 앱 구현을 시작합니다. HTML, CSS, JavaScript 파일을 생성합니다.


```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo 앱</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>📝 할 일 목록</h1>
        </header>
        
        <div class="input-section">
            <input 
                type="text" 
                id="todoInput" 
                placeholder="할 일을 입력하세요..." 
                autocomplete="off"
            >
            <button id="addBtn">추가</button>
        </div>
        
        <ul id="todoList" class="todo-list"></ul>
        
        <div class="info">
            <p>완료된 항목: <span id="completedCount">0</span> / 전체: <span id="totalCount">0</span></p>
        </div>
    </div>
    
    <script src="app.js"></script>
</body>
</html>

```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 30px;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    todoInput.focus();
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    if (todos.length === 0) {
        todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        return;
    }
    
    todos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        const textSpan = document.createElement('span');
        textSpan.className = 'todo-text';
        textSpan.textContent = todo.text;
        
        const deleteBtn = document.createElement('button');
        deleteBtn.className = 'delete-btn';
        deleteBtn.textContent = '삭제';
        deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
        
        li.appendChild(checkbox);
        li.appendChild(textSpan);
        li.appendChild(deleteBtn);
        
        todoList.appendChild(li);
    });
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

```


Todo 앱을 구현했습니다.

## 생성된 파일

1. **index.html** - 메인 HTML 구조
2. **style.css** - 스타일
3. **app.js** - JavaScript 로직

## 구현된 기능

✅ **할 일 추가**: 입력 필드에 텍스트를 입력하고 '추가' 버튼을 클릭하거나 Enter 키를 누르면 목록에 추가됩니다.

✅ **완료 표시**: 각 항목의 체크박스를 클릭하면 취소선이 표시되고 완료 상태로 변경됩니다.

✅ **항목 삭제**: 각 항목의 '삭제' 버튼을 클릭하면 확인 후 삭제됩니다.

✅ **데이터 영구 저장**: localStorage를 사용하여 브라우저를 닫았다가 다시 열어도 기존 할 일 목록이 유지됩니다.

## 추가 기능

- 완료된 항목과 전체 항목 수 표시
- Enter 키로 할 일 추가
- 빈 목록일 때 안내 메시지 표시
- 반응형 디자인

## 사용 방법

브라우저에서 `index.html` 파일을 열면 바로 사용할 수 있습니다. 할 일을 추가하고, 체크박스로 완료 표시를 하며, 삭제 버튼으로 항목을 제거할 수 있습니다. 모든 데이터는 브라우저의 localStorage에 자동으로 저장됩니다.

---

**User**

추가 기능도 추가해줘

---

**Cursor**


현재 코드를 확인하고, 실용적인 추가 기능을 구현합니다.



다음 기능을 추가합니다:
1. 할 일 수정 (더블클릭으로 편집)
2. 필터링 (전체/완료/미완료)
3. 검색
4. 완료된 항목 일괄 삭제


```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo 앱</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>📝 할 일 목록</h1>
        </header>
        
        <div class="input-section">
            <input 
                type="text" 
                id="todoInput" 
                placeholder="할 일을 입력하세요..." 
                autocomplete="off"
            >
            <button id="addBtn">추가</button>
        </div>
        
        <div class="search-section">
            <input 
                type="text" 
                id="searchInput" 
                placeholder="🔍 검색..." 
                autocomplete="off"
            >
        </div>
        
        <div class="filter-section">
            <button class="filter-btn active" data-filter="all">전체</button>
            <button class="filter-btn" data-filter="active">미완료</button>
            <button class="filter-btn" data-filter="completed">완료</button>
        </div>
        
        <ul id="todoList" class="todo-list"></ul>
        
        <div class="info">
            <p>완료된 항목: <span id="completedCount">0</span> / 전체: <span id="totalCount">0</span></p>
            <button id="clearCompletedBtn" class="clear-btn">완료된 항목 모두 삭제</button>
        </div>
    </div>
    
    <script src="app.js"></script>
</body>
</html>


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    todoInput.focus();
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    if (todos.length === 0) {
        todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        return;
    }
    
    todos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        const textSpan = document.createElement('span');
        textSpan.className = 'todo-text';
        textSpan.textContent = todo.text;
        
        const deleteBtn = document.createElement('button');
        deleteBtn.className = 'delete-btn';
        deleteBtn.textContent = '삭제';
        deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
        
        li.appendChild(checkbox);
        li.appendChild(textSpan);
        li.appendChild(deleteBtn);
        
        todoList.appendChild(li);
    });
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textSpan);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textSpan);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```


추가 기능을 구현했습니다.

## 새로 추가된 기능

### 1. 할 일 수정 기능
- 더블클릭 또는 '수정' 버튼으로 편집 모드 진입
- Enter로 저장, Escape로 취소
- 입력 필드에서 포커스가 벗어나면 자동 저장

### 2. 필터링 기능
- 전체: 모든 할 일 표시
- 미완료: 완료되지 않은 항목만 표시
- 완료: 완료된 항목만 표시

### 3. 검색 기능
- 검색창에 키워드 입력 시 실시간 검색
- 대소문자 구분 없음

### 4. 완료된 항목 일괄 삭제
- '완료된 항목 모두 삭제' 버튼으로 한 번에 삭제
- 삭제 전 확인 메시지 표시

## 사용 방법

- 수정: 항목 텍스트를 더블클릭하거나 '수정' 버튼 클릭
- 필터: 상단 필터 버튼(전체/미완료/완료)으로 목록 필터링
- 검색: 검색창에 키워드 입력
- 일괄 삭제: 하단 '완료된 항목 모두 삭제' 버튼 클릭

모든 기능은 기존 localStorage 저장 기능과 함께 동작합니다.

---

**User**

전체적인 색감을 모던한 그레이톤으로 바꿔줘

---

**Cursor**


모던한 그레이톤으로 색상 변경 중입니다.



모던한 그레이톤으로 색상 변경 중입니다.


```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #4a5568;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #4a5568;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #2d3748;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(74, 85, 104, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #4a5568;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #4a5568;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #4a5568;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #2d3748;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(74, 85, 104, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #4a5568;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #4a5568;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #718096;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #4a5568;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #4a5568;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #2d3748;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(74, 85, 104, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #4a5568;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #4a5568;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #718096;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #4a5568;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f7fafc;
    border-radius: 10px;
    color: #718096;
    font-size: 14px;
    border: 1px solid #e2e8f0;
}

.info span {
    font-weight: 600;
    color: #4a5568;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #4a5568;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #2d3748;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(74, 85, 104, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #4a5568;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #4a5568;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #718096;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #4a5568;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f7fafc;
    border-radius: 10px;
    color: #718096;
    font-size: 14px;
    border: 1px solid #e2e8f0;
}

.info span {
    font-weight: 600;
    color: #4a5568;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #a0aec0;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #718096;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(113, 128, 150, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #4a5568;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #2d3748;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(74, 85, 104, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #4a5568;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #4a5568;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #718096;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #4a5568;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f7fafc;
    border-radius: 10px;
    color: #718096;
    font-size: 14px;
    border: 1px solid #e2e8f0;
}

.info span {
    font-weight: 600;
    color: #4a5568;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #a0aec0;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #718096;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(113, 128, 150, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #4a5568;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #2d3748;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #4a5568;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #2d3748;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(74, 85, 104, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #4a5568;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #4a5568;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #718096;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #4a5568;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f7fafc;
    border-radius: 10px;
    color: #718096;
    font-size: 14px;
    border: 1px solid #e2e8f0;
}

.info span {
    font-weight: 600;
    color: #4a5568;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #a0aec0;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #718096;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(113, 128, 150, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #4a5568;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #2d3748;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #718096;
    font-size: 18px;
}


```


완료된 항목 텍스트 색상도 그레이톤으로 통일 중입니다.


```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #e2e8f0 0%, #cbd5e0 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: #ffffff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #2d3748;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e2e8f0;
    border-color: #718096;
}

.filter-btn.active {
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border-color: #4a5568;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #4a5568;
    box-shadow: 0 0 0 3px rgba(74, 85, 104, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #4a5568 0%, #2d3748 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(45, 55, 72, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f7fafc;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    border: 1px solid #e2e8f0;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #edf2f7;
    transform: translateX(5px);
    border-color: #cbd5e0;
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #718096;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #4a5568;
}

.todo-text {
    flex: 1;
    font-size: 16px;
    color: #2d3748;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(74, 85, 104, 0.1);
}

.todo-text.editing {
    background: white;
    border: 2px solid #4a5568;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #4a5568;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #718096;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #4a5568;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f7fafc;
    border-radius: 10px;
    color: #718096;
    font-size: 14px;
    border: 1px solid #e2e8f0;
}

.info span {
    font-weight: 600;
    color: #4a5568;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #a0aec0;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #718096;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(113, 128, 150, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #4a5568;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #2d3748;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #718096;
    font-size: 18px;
}


```


모든 색상을 모던한 그레이톤으로 변경했습니다.

## 변경된 색상

### 배경 및 컨테이너
- 배경: 보라색 그라데이션 → 밝은 그레이 그라데이션 (`#e2e8f0` → `#cbd5e0`)
- 컨테이너: 그림자 강도 조정

### 버튼 및 액센트
- 추가 버튼: 보라색 → 다크 그레이 그라데이션 (`#4a5568` → `#2d3748`)
- 필터 버튼 (활성): 보라색 → 다크 그레이 그라데이션
- 수정 버튼: 초록색 → 다크 그레이 (`#4a5568`)
- 삭제 버튼: 빨간색 → 그레이 (`#718096`)
- 완료 삭제 버튼: 노란색 → 그레이 (`#a0aec0`)

### 텍스트 및 입력 필드
- 제목: 다크 그레이 (`#2d3748`)
- 입력 필드 포커스: 보라색 → 다크 그레이 (`#4a5568`)
- 체크박스: 다크 그레이 (`#4a5568`)
- 완료된 항목 텍스트: 그레이 (`#718096`)

### 기타
- 할 일 항목: 미세한 테두리 추가
- 호버 효과: 그레이 톤으로 통일

전체적으로 그레이 톤으로 통일했습니다.

---

**User**

할일의 완료 날짜와 시간을 추가해줘

---

**Cursor**


완료 날짜와 시간 표시 기능 추가 중. 완료 시점을 기록하고 표시합니다.



완료 날짜와 시간 표시 기능 추가 중.


```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textSpan);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const dateSpan = document.createElement('span');
                dateSpan.className = 'todo-date';
                dateSpan.textContent = formatDate(todo.completedDate);
                textContainer.appendChild(textSpan);
                textContainer.appendChild(dateSpan);
            } else {
                textContainer.appendChild(textSpan);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```


기존 완료 항목과의 호환성을 위해 마이그레이션 로직 추가 중.


```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const dateSpan = document.createElement('span');
                dateSpan.className = 'todo-date';
                dateSpan.textContent = formatDate(todo.completedDate);
                textContainer.appendChild(textSpan);
                textContainer.appendChild(dateSpan);
            } else {
                textContainer.appendChild(textSpan);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```


완료 날짜와 시간 표시 기능을 추가했습니다.

## 추가된 기능

### 완료 날짜/시간 기록
- 할 일을 완료하면 완료 시점의 날짜와 시간이 자동 저장됩니다.
- 완료를 해제하면 날짜 정보가 제거됩니다.

### 날짜 표시 형식
- 오늘 완료: "오늘 14:30"
- 어제 완료: "어제 18:45"
- 올해 완료: "12/25 10:15"
- 작년 이전: "2023/12/25 10:15"

### UI 개선
- 완료된 항목 아래에 작은 글씨로 날짜/시간 표시
- 회색 이탤릭체로 표시

## 작동 방식

1. 할 일을 완료하면 체크박스를 클릭한 시점의 날짜와 시간이 저장됩니다.
2. 완료된 항목에는 할 일 텍스트 아래에 완료 날짜/시간이 표시됩니다.
3. 완료를 해제하면 날짜 정보가 제거됩니다.
4. 기존에 완료된 항목(날짜 정보 없음)은 날짜 없이 표시됩니다.

완료 시점을 추적할 수 있습니다.

---

**User**

할 일을 입력할때 완료 기한을 같이 등록 할수 있게 해줘

---

**Cursor**


완료 기한 등록 기능 추가 중. 입력 시 마감일을 함께 설정할 수 있도록 합니다.



완료 기한 등록 기능 추가 중.


```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo 앱</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>📝 할 일 목록</h1>
        </header>
        
        <div class="input-section">
            <input 
                type="text" 
                id="todoInput" 
                placeholder="할 일을 입력하세요..." 
                autocomplete="off"
            >
            <input 
                type="date" 
                id="dueDateInput" 
                class="due-date-input"
                title="완료 기한 (선택사항)"
            >
            <button id="addBtn">추가</button>
        </div>
        
        <div class="search-section">
            <input 
                type="text" 
                id="searchInput" 
                placeholder="🔍 검색..." 
                autocomplete="off"
            >
        </div>
        
        <div class="filter-section">
            <button class="filter-btn active" data-filter="all">전체</button>
            <button class="filter-btn" data-filter="active">미완료</button>
            <button class="filter-btn" data-filter="completed">완료</button>
        </div>
        
        <ul id="todoList" class="todo-list"></ul>
        
        <div class="info">
            <p>완료된 항목: <span id="completedCount">0</span> / 전체: <span id="totalCount">0</span></p>
            <button id="clearCompletedBtn" class="clear-btn">완료된 항목 모두 삭제</button>
        </div>
    </div>
    
    <script src="app.js"></script>
</body>
</html>


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const dateSpan = document.createElement('span');
                dateSpan.className = 'todo-date';
                dateSpan.textContent = formatDate(todo.completedDate);
                textContainer.appendChild(textSpan);
                textContainer.appendChild(dateSpan);
            } else {
                textContainer.appendChild(textSpan);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const dateSpan = document.createElement('span');
                dateSpan.className = 'todo-date';
                dateSpan.textContent = formatDate(todo.completedDate);
                textContainer.appendChild(textSpan);
                textContainer.appendChild(dateSpan);
            } else {
                textContainer.appendChild(textSpan);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        li.className = `todo-item ${todo.completed ? 'completed' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const dateSpan = document.createElement('span');
                dateSpan.className = 'todo-date';
                dateSpan.textContent = formatDate(todo.completedDate);
                textContainer.appendChild(textSpan);
                textContainer.appendChild(dateSpan);
            } else {
                textContainer.appendChild(textSpan);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const dateSpan = document.createElement('span');
                dateSpan.className = 'todo-date';
                dateSpan.textContent = formatDate(todo.completedDate);
                textContainer.appendChild(textSpan);
                textContainer.appendChild(dateSpan);
            } else {
                textContainer.appendChild(textSpan);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```


완료 기한 등록 기능을 추가했습니다.

## 추가된 기능

### 완료 기한 등록
- 할 일 입력 시 날짜 선택 필드 추가
- 완료 기한은 선택 사항입니다
- 날짜를 선택하지 않으면 기한 없음으로 저장됩니다

### 완료 기한 표시
- 각 할 일 항목에 완료 기한이 표시됩니다
- 표시 형식:
  - 오늘까지
  - 내일까지
  - 12/25까지 (올해)
  - 2024/12/25까지 (다른 해)

### 만료 항목 시각적 표시
- 완료 기한이 지난 미완료 항목은 자동으로 강조됩니다
- 빨간색 왼쪽 테두리
- 연한 빨간색 배경
- "만료" 표시와 함께 빨간색 텍스트

### 날짜 정보 표시
- 완료 기한: 파란색으로 표시
- 만료된 기한: 빨간색으로 표시
- 완료 날짜: 초록색으로 표시 (완료된 경우)

## 사용 방법

1. 할 일을 입력합니다
2. 날짜 선택 필드에서 완료 기한을 선택합니다 (선택 사항)
3. '추가' 버튼을 클릭합니다
4. 완료 기한이 지난 미완료 항목은 자동으로 강조 표시됩니다

완료 기한을 관리하고 만료된 항목을 쉽게 확인할 수 있습니다.

---

**User**

마감 임박(예: 24시간 이내) 노란색 표시, 마감순 정렬, 또는 마감 알림(브라우저 알림)도 추가

---

**Cursor**


마감 임박 표시, 마감순 정렬, 브라우저 알림 기능 추가 중.



마감 임박 표시, 마감순 정렬, 브라우저 알림 기능 추가 중.


```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo 앱</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>📝 할 일 목록</h1>
        </header>
        
        <div class="input-section">
            <input 
                type="text" 
                id="todoInput" 
                placeholder="할 일을 입력하세요..." 
                autocomplete="off"
            >
            <input 
                type="date" 
                id="dueDateInput" 
                class="due-date-input"
                title="완료 기한 (선택사항)"
            >
            <button id="addBtn">추가</button>
        </div>
        
        <div class="search-section">
            <input 
                type="text" 
                id="searchInput" 
                placeholder="🔍 검색..." 
                autocomplete="off"
            >
        </div>
        
        <div class="filter-section">
            <button class="filter-btn active" data-filter="all">전체</button>
            <button class="filter-btn" data-filter="active">미완료</button>
            <button class="filter-btn" data-filter="completed">완료</button>
            <button class="sort-btn" id="sortBtn" title="마감순 정렬">
                <span id="sortIcon">🔀</span> 정렬
            </button>
        </div>
        
        <ul id="todoList" class="todo-list"></ul>
        
        <div class="info">
            <p>완료된 항목: <span id="completedCount">0</span> / 전체: <span id="totalCount">0</span></p>
            <button id="clearCompletedBtn" class="clear-btn">완료된 항목 모두 삭제</button>
        </div>
    </div>
    
    <script src="app.js"></script>
</body>
</html>


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    return filtered;
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderTodos();
});

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 600px;
    padding: 30px;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```


마감 임박 표시, 마감순 정렬, 브라우저 알림 기능을 추가했습니다.

## 추가된 기능

### 1. 마감 임박(24시간 이내) 노란색 표시
- 완료 기한이 24시간 이내인 미완료 항목은 자동으로 노란색으로 강조됩니다
- 노란색 왼쪽 테두리와 연한 노란색 배경
- 완료 기한 텍스트도 노란색으로 표시

### 2. 마감순 정렬
- 필터 섹션에 "정렬" 버튼 추가
- 클릭 시 마감일이 가까운 순으로 정렬
- 정렬 순서:
  - 미완료 항목이 완료 항목보다 위
  - 마감일이 가까운 항목이 위
  - 기한이 없는 항목은 맨 아래
- 다시 클릭하면 정렬 해제

### 3. 브라우저 알림
- 페이지 로드 시 알림 권한 요청
- 1분마다 자동으로 마감 임박 항목 체크
- 24시간 이내 마감인 항목이 있으면 브라우저 알림 표시
- 알림 내용:
  - 1개: "할 일 제목"의 마감이 24시간 이내입니다!
  - 여러 개: "N개의 할 일이 24시간 이내에 마감됩니다!"

## 시각적 표시

- 만료된 항목: 빨간색 테두리 + 빨간색 배경
- 마감 임박(24시간 이내): 노란색 테두리 + 노란색 배경
- 일반 항목: 기본 스타일

## 사용 방법

1. 마감 임박 표시: 자동으로 노란색으로 표시됩니다
2. 마감순 정렬: 필터 섹션의 "정렬" 버튼 클릭
3. 브라우저 알림: 첫 방문 시 알림 권한을 허용하면 자동으로 작동합니다

마감 관리가 더 쉬워집니다.

---

**User**

남은 할 일 개수 표시, 캘린더/주간 보기 기능도 추가

---

**Cursor**


남은 할 일 개수 표시와 캘린더/주간 보기 기능 추가 중.



남은 할 일 개수 표시와 캘린더/주간 보기 기능 추가 중.


```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo 앱</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>📝 할 일 목록</h1>
        </header>
        
        <div class="input-section">
            <input 
                type="text" 
                id="todoInput" 
                placeholder="할 일을 입력하세요..." 
                autocomplete="off"
            >
            <input 
                type="date" 
                id="dueDateInput" 
                class="due-date-input"
                title="완료 기한 (선택사항)"
            >
            <button id="addBtn">추가</button>
        </div>
        
        <div class="search-section">
            <input 
                type="text" 
                id="searchInput" 
                placeholder="🔍 검색..." 
                autocomplete="off"
            >
        </div>
        
        <div class="view-section">
            <button class="view-btn active" data-view="list" title="리스트 보기">📋 리스트</button>
            <button class="view-btn" data-view="week" title="주간 보기">📅 주간</button>
            <button class="view-btn" data-view="calendar" title="캘린더 보기">🗓️ 캘린더</button>
        </div>
        
        <div class="filter-section">
            <button class="filter-btn active" data-filter="all">전체</button>
            <button class="filter-btn" data-filter="active">미완료</button>
            <button class="filter-btn" data-filter="completed">완료</button>
            <button class="sort-btn" id="sortBtn" title="마감순 정렬">
                <span id="sortIcon">🔀</span> 정렬
            </button>
        </div>
        
        <!-- 리스트 뷰 -->
        <ul id="todoList" class="todo-list list-view"></ul>
        
        <!-- 주간 뷰 -->
        <div id="weekView" class="week-view hidden">
            <div class="week-header">
                <button class="week-nav-btn" id="prevWeekBtn">◀</button>
                <h2 id="weekTitle"></h2>
                <button class="week-nav-btn" id="nextWeekBtn">▶</button>
            </div>
            <div id="weekDays" class="week-days"></div>
        </div>
        
        <!-- 캘린더 뷰 -->
        <div id="calendarView" class="calendar-view hidden">
            <div class="calendar-header">
                <button class="calendar-nav-btn" id="prevMonthBtn">◀</button>
                <h2 id="calendarTitle"></h2>
                <button class="calendar-nav-btn" id="nextMonthBtn">▶</button>
            </div>
            <div id="calendarGrid" class="calendar-grid"></div>
        </div>
        
        <div class="info">
            <p>
                남은 할 일: <span id="remainingCount" class="remaining-count">0</span> | 
                완료: <span id="completedCount">0</span> | 
                전체: <span id="totalCount">0</span>
            </p>
            <button id="clearCompletedBtn" class="clear-btn">완료된 항목 모두 삭제</button>
        </div>
    </div>
    
    <script src="app.js"></script>
</body>
</html>


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderTodos();
});

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    const remaining = todos.filter(t => !t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
    document.getElementById('remainingCount').textContent = remaining;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderTodos();
});

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderTodos();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    const remaining = todos.filter(t => !t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
    document.getElementById('remainingCount').textContent = remaining;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderCurrentView();
});

// 뷰 모드 전환
const viewBtns = document.querySelectorAll('.view-btn');
viewBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        currentView = btn.dataset.view;
        viewBtns.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        renderCurrentView();
    });
});

// 현재 뷰 렌더링
function renderCurrentView() {
    if (currentView === 'list') {
        document.getElementById('todoList').classList.remove('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderTodos();
    } else if (currentView === 'week') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.remove('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderWeekView();
    } else if (currentView === 'calendar') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.remove('hidden');
        renderCalendarView();
    }
}

// 주간 뷰 렌더링
function renderWeekView() {
    const weekDays = document.getElementById('weekDays');
    weekDays.innerHTML = '';
    
    // 주의 시작일 계산 (월요일)
    const start = new Date(currentWeekStart);
    const day = start.getDay();
    const diff = start.getDate() - day + (day === 0 ? -6 : 1); // 월요일로 조정
    start.setDate(diff);
    start.setHours(0, 0, 0, 0);
    
    // 주의 종료일 (일요일)
    const end = new Date(start);
    end.setDate(end.getDate() + 6);
    end.setHours(23, 59, 59, 999);
    
    // 주 제목 업데이트
    const weekTitle = document.getElementById('weekTitle');
    const month = start.getMonth() + 1;
    const startDay = start.getDate();
    const endMonth = end.getMonth() + 1;
    const endDay = end.getDate();
    weekTitle.textContent = `${start.getFullYear()}년 ${month}월 ${startDay}일 ~ ${endMonth}월 ${endDay}일`;
    
    // 각 날짜별 할 일 표시
    for (let i = 0; i < 7; i++) {
        const date = new Date(start);
        date.setDate(date.getDate() + i);
        const dateStr = date.toISOString().split('T')[0];
        
        const dayDiv = document.createElement('div');
        dayDiv.className = 'week-day';
        
        const dayHeader = document.createElement('div');
        dayHeader.className = 'week-day-header';
        const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
        dayHeader.innerHTML = `
            <div class="week-day-name">${dayNames[date.getDay()]}</div>
            <div class="week-day-date">${date.getDate()}</div>
        `;
        
        const dayTodos = document.createElement('div');
        dayTodos.className = 'week-day-todos';
        
        // 해당 날짜의 할 일 필터링
        const dayTodosList = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodosList.length === 0) {
            dayTodos.innerHTML = '<div class="week-empty">할 일 없음</div>';
        } else {
            dayTodosList.forEach(todo => {
                const todoItem = document.createElement('div');
                todoItem.className = 'week-todo-item';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoItem.classList.add('overdue');
                if (isUrgentItem) todoItem.classList.add('urgent');
                
                todoItem.innerHTML = `
                    <input type="checkbox" class="todo-checkbox" ${todo.completed ? 'checked' : ''} 
                        onchange="toggleTodo(${todo.id})">
                    <span class="week-todo-text">${todo.text}</span>
                `;
                dayTodos.appendChild(todoItem);
            });
        }
        
        dayDiv.appendChild(dayHeader);
        dayDiv.appendChild(dayTodos);
        weekDays.appendChild(dayDiv);
    }
    
    // 이전/다음 주 버튼
    document.getElementById('prevWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() - 7);
        renderWeekView();
    };
    
    document.getElementById('nextWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() + 7);
        renderWeekView();
    };
}

// 캘린더 뷰 렌더링
function renderCalendarView() {
    const calendarGrid = document.getElementById('calendarGrid');
    calendarGrid.innerHTML = '';
    
    const year = currentMonth.getFullYear();
    const month = currentMonth.getMonth();
    
    // 캘린더 제목 업데이트
    document.getElementById('calendarTitle').textContent = `${year}년 ${month + 1}월`;
    
    // 월의 첫 날과 마지막 날
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const startDate = new Date(firstDay);
    startDate.setDate(startDate.getDate() - firstDay.getDay()); // 주의 시작일로 조정
    
    // 요일 헤더
    const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
    dayNames.forEach(day => {
        const header = document.createElement('div');
        header.className = 'calendar-day-header';
        header.textContent = day;
        calendarGrid.appendChild(header);
    });
    
    // 날짜 셀 생성
    const currentDate = new Date(startDate);
    for (let i = 0; i < 42; i++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day';
        
        const dateStr = currentDate.toISOString().split('T')[0];
        const isCurrentMonth = currentDate.getMonth() === month;
        const isToday = dateStr === new Date().toISOString().split('T')[0];
        
        if (!isCurrentMonth) {
            cell.classList.add('other-month');
        }
        if (isToday) {
            cell.classList.add('today');
        }
        
        const dayNumber = document.createElement('div');
        dayNumber.className = 'calendar-day-number';
        dayNumber.textContent = currentDate.getDate();
        cell.appendChild(dayNumber);
        
        // 해당 날짜의 할 일 표시
        const dayTodos = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodos.length > 0) {
            const todosContainer = document.createElement('div');
            todosContainer.className = 'calendar-todos';
            
            dayTodos.slice(0, 3).forEach(todo => {
                const todoDot = document.createElement('div');
                todoDot.className = 'calendar-todo-dot';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoDot.classList.add('overdue');
                else if (isUrgentItem) todoDot.classList.add('urgent');
                todosContainer.appendChild(todoDot);
            });
            
            if (dayTodos.length > 3) {
                const more = document.createElement('div');
                more.className = 'calendar-more';
                more.textContent = `+${dayTodos.length - 3}`;
                todosContainer.appendChild(more);
            }
            
            cell.appendChild(todosContainer);
        }
        
        calendarGrid.appendChild(cell);
        currentDate.setDate(currentDate.getDate() + 1);
    }
    
    // 이전/다음 월 버튼
    document.getElementById('prevMonthBtn').onclick = () => {
        currentMonth = new Date(year, month - 1, 1);
        renderCalendarView();
    };
    
    document.getElementById('nextMonthBtn').onclick = () => {
        currentMonth = new Date(year, month + 1, 1);
        renderCalendarView();
    };
}

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```

```javascript
    saveTodos();
    renderCurrentView();
    updateCount();
```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderTodos();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    const remaining = todos.filter(t => !t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
    document.getElementById('remainingCount').textContent = remaining;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderCurrentView();
});

// 뷰 모드 전환
const viewBtns = document.querySelectorAll('.view-btn');
viewBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        currentView = btn.dataset.view;
        viewBtns.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        renderCurrentView();
    });
});

// 현재 뷰 렌더링
function renderCurrentView() {
    if (currentView === 'list') {
        document.getElementById('todoList').classList.remove('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderTodos();
    } else if (currentView === 'week') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.remove('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderWeekView();
    } else if (currentView === 'calendar') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.remove('hidden');
        renderCalendarView();
    }
}

// 주간 뷰 렌더링
function renderWeekView() {
    const weekDays = document.getElementById('weekDays');
    weekDays.innerHTML = '';
    
    // 주의 시작일 계산 (월요일)
    const start = new Date(currentWeekStart);
    const day = start.getDay();
    const diff = start.getDate() - day + (day === 0 ? -6 : 1); // 월요일로 조정
    start.setDate(diff);
    start.setHours(0, 0, 0, 0);
    
    // 주의 종료일 (일요일)
    const end = new Date(start);
    end.setDate(end.getDate() + 6);
    end.setHours(23, 59, 59, 999);
    
    // 주 제목 업데이트
    const weekTitle = document.getElementById('weekTitle');
    const month = start.getMonth() + 1;
    const startDay = start.getDate();
    const endMonth = end.getMonth() + 1;
    const endDay = end.getDate();
    weekTitle.textContent = `${start.getFullYear()}년 ${month}월 ${startDay}일 ~ ${endMonth}월 ${endDay}일`;
    
    // 각 날짜별 할 일 표시
    for (let i = 0; i < 7; i++) {
        const date = new Date(start);
        date.setDate(date.getDate() + i);
        const dateStr = date.toISOString().split('T')[0];
        
        const dayDiv = document.createElement('div');
        dayDiv.className = 'week-day';
        
        const dayHeader = document.createElement('div');
        dayHeader.className = 'week-day-header';
        const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
        dayHeader.innerHTML = `
            <div class="week-day-name">${dayNames[date.getDay()]}</div>
            <div class="week-day-date">${date.getDate()}</div>
        `;
        
        const dayTodos = document.createElement('div');
        dayTodos.className = 'week-day-todos';
        
        // 해당 날짜의 할 일 필터링
        const dayTodosList = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodosList.length === 0) {
            dayTodos.innerHTML = '<div class="week-empty">할 일 없음</div>';
        } else {
            dayTodosList.forEach(todo => {
                const todoItem = document.createElement('div');
                todoItem.className = 'week-todo-item';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoItem.classList.add('overdue');
                if (isUrgentItem) todoItem.classList.add('urgent');
                
                todoItem.innerHTML = `
                    <input type="checkbox" class="todo-checkbox" ${todo.completed ? 'checked' : ''} 
                        onchange="toggleTodo(${todo.id})">
                    <span class="week-todo-text">${todo.text}</span>
                `;
                dayTodos.appendChild(todoItem);
            });
        }
        
        dayDiv.appendChild(dayHeader);
        dayDiv.appendChild(dayTodos);
        weekDays.appendChild(dayDiv);
    }
    
    // 이전/다음 주 버튼
    document.getElementById('prevWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() - 7);
        renderWeekView();
    };
    
    document.getElementById('nextWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() + 7);
        renderWeekView();
    };
}

// 캘린더 뷰 렌더링
function renderCalendarView() {
    const calendarGrid = document.getElementById('calendarGrid');
    calendarGrid.innerHTML = '';
    
    const year = currentMonth.getFullYear();
    const month = currentMonth.getMonth();
    
    // 캘린더 제목 업데이트
    document.getElementById('calendarTitle').textContent = `${year}년 ${month + 1}월`;
    
    // 월의 첫 날과 마지막 날
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const startDate = new Date(firstDay);
    startDate.setDate(startDate.getDate() - firstDay.getDay()); // 주의 시작일로 조정
    
    // 요일 헤더
    const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
    dayNames.forEach(day => {
        const header = document.createElement('div');
        header.className = 'calendar-day-header';
        header.textContent = day;
        calendarGrid.appendChild(header);
    });
    
    // 날짜 셀 생성
    const currentDate = new Date(startDate);
    for (let i = 0; i < 42; i++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day';
        
        const dateStr = currentDate.toISOString().split('T')[0];
        const isCurrentMonth = currentDate.getMonth() === month;
        const isToday = dateStr === new Date().toISOString().split('T')[0];
        
        if (!isCurrentMonth) {
            cell.classList.add('other-month');
        }
        if (isToday) {
            cell.classList.add('today');
        }
        
        const dayNumber = document.createElement('div');
        dayNumber.className = 'calendar-day-number';
        dayNumber.textContent = currentDate.getDate();
        cell.appendChild(dayNumber);
        
        // 해당 날짜의 할 일 표시
        const dayTodos = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodos.length > 0) {
            const todosContainer = document.createElement('div');
            todosContainer.className = 'calendar-todos';
            
            dayTodos.slice(0, 3).forEach(todo => {
                const todoDot = document.createElement('div');
                todoDot.className = 'calendar-todo-dot';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoDot.classList.add('overdue');
                else if (isUrgentItem) todoDot.classList.add('urgent');
                todosContainer.appendChild(todoDot);
            });
            
            if (dayTodos.length > 3) {
                const more = document.createElement('div');
                more.className = 'calendar-more';
                more.textContent = `+${dayTodos.length - 3}`;
                todosContainer.appendChild(more);
            }
            
            cell.appendChild(todosContainer);
        }
        
        calendarGrid.appendChild(cell);
        currentDate.setDate(currentDate.getDate() + 1);
    }
    
    // 이전/다음 월 버튼
    document.getElementById('prevMonthBtn').onclick = () => {
        currentMonth = new Date(year, month - 1, 1);
        renderCalendarView();
    };
    
    document.getElementById('nextMonthBtn').onclick = () => {
        currentMonth = new Date(year, month + 1, 1);
        renderCalendarView();
    };
}

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```


나머지 `renderTodos()` 호출을 `renderCurrentView()`로 변경하고 CSS를 추가 중.


```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderTodos();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    const remaining = todos.filter(t => !t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
    document.getElementById('remainingCount').textContent = remaining;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderCurrentView();
});

// 뷰 모드 전환
const viewBtns = document.querySelectorAll('.view-btn');
viewBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        currentView = btn.dataset.view;
        viewBtns.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        renderCurrentView();
    });
});

// 현재 뷰 렌더링
function renderCurrentView() {
    if (currentView === 'list') {
        document.getElementById('todoList').classList.remove('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderTodos();
    } else if (currentView === 'week') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.remove('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderWeekView();
    } else if (currentView === 'calendar') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.remove('hidden');
        renderCalendarView();
    }
}

// 주간 뷰 렌더링
function renderWeekView() {
    const weekDays = document.getElementById('weekDays');
    weekDays.innerHTML = '';
    
    // 주의 시작일 계산 (월요일)
    const start = new Date(currentWeekStart);
    const day = start.getDay();
    const diff = start.getDate() - day + (day === 0 ? -6 : 1); // 월요일로 조정
    start.setDate(diff);
    start.setHours(0, 0, 0, 0);
    
    // 주의 종료일 (일요일)
    const end = new Date(start);
    end.setDate(end.getDate() + 6);
    end.setHours(23, 59, 59, 999);
    
    // 주 제목 업데이트
    const weekTitle = document.getElementById('weekTitle');
    const month = start.getMonth() + 1;
    const startDay = start.getDate();
    const endMonth = end.getMonth() + 1;
    const endDay = end.getDate();
    weekTitle.textContent = `${start.getFullYear()}년 ${month}월 ${startDay}일 ~ ${endMonth}월 ${endDay}일`;
    
    // 각 날짜별 할 일 표시
    for (let i = 0; i < 7; i++) {
        const date = new Date(start);
        date.setDate(date.getDate() + i);
        const dateStr = date.toISOString().split('T')[0];
        
        const dayDiv = document.createElement('div');
        dayDiv.className = 'week-day';
        
        const dayHeader = document.createElement('div');
        dayHeader.className = 'week-day-header';
        const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
        dayHeader.innerHTML = `
            <div class="week-day-name">${dayNames[date.getDay()]}</div>
            <div class="week-day-date">${date.getDate()}</div>
        `;
        
        const dayTodos = document.createElement('div');
        dayTodos.className = 'week-day-todos';
        
        // 해당 날짜의 할 일 필터링
        const dayTodosList = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodosList.length === 0) {
            dayTodos.innerHTML = '<div class="week-empty">할 일 없음</div>';
        } else {
            dayTodosList.forEach(todo => {
                const todoItem = document.createElement('div');
                todoItem.className = 'week-todo-item';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoItem.classList.add('overdue');
                if (isUrgentItem) todoItem.classList.add('urgent');
                
                todoItem.innerHTML = `
                    <input type="checkbox" class="todo-checkbox" ${todo.completed ? 'checked' : ''} 
                        onchange="toggleTodo(${todo.id})">
                    <span class="week-todo-text">${todo.text}</span>
                `;
                dayTodos.appendChild(todoItem);
            });
        }
        
        dayDiv.appendChild(dayHeader);
        dayDiv.appendChild(dayTodos);
        weekDays.appendChild(dayDiv);
    }
    
    // 이전/다음 주 버튼
    document.getElementById('prevWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() - 7);
        renderWeekView();
    };
    
    document.getElementById('nextWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() + 7);
        renderWeekView();
    };
}

// 캘린더 뷰 렌더링
function renderCalendarView() {
    const calendarGrid = document.getElementById('calendarGrid');
    calendarGrid.innerHTML = '';
    
    const year = currentMonth.getFullYear();
    const month = currentMonth.getMonth();
    
    // 캘린더 제목 업데이트
    document.getElementById('calendarTitle').textContent = `${year}년 ${month + 1}월`;
    
    // 월의 첫 날과 마지막 날
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const startDate = new Date(firstDay);
    startDate.setDate(startDate.getDate() - firstDay.getDay()); // 주의 시작일로 조정
    
    // 요일 헤더
    const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
    dayNames.forEach(day => {
        const header = document.createElement('div');
        header.className = 'calendar-day-header';
        header.textContent = day;
        calendarGrid.appendChild(header);
    });
    
    // 날짜 셀 생성
    const currentDate = new Date(startDate);
    for (let i = 0; i < 42; i++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day';
        
        const dateStr = currentDate.toISOString().split('T')[0];
        const isCurrentMonth = currentDate.getMonth() === month;
        const isToday = dateStr === new Date().toISOString().split('T')[0];
        
        if (!isCurrentMonth) {
            cell.classList.add('other-month');
        }
        if (isToday) {
            cell.classList.add('today');
        }
        
        const dayNumber = document.createElement('div');
        dayNumber.className = 'calendar-day-number';
        dayNumber.textContent = currentDate.getDate();
        cell.appendChild(dayNumber);
        
        // 해당 날짜의 할 일 표시
        const dayTodos = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodos.length > 0) {
            const todosContainer = document.createElement('div');
            todosContainer.className = 'calendar-todos';
            
            dayTodos.slice(0, 3).forEach(todo => {
                const todoDot = document.createElement('div');
                todoDot.className = 'calendar-todo-dot';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoDot.classList.add('overdue');
                else if (isUrgentItem) todoDot.classList.add('urgent');
                todosContainer.appendChild(todoDot);
            });
            
            if (dayTodos.length > 3) {
                const more = document.createElement('div');
                more.className = 'calendar-more';
                more.textContent = `+${dayTodos.length - 3}`;
                todosContainer.appendChild(more);
            }
            
            cell.appendChild(todosContainer);
        }
        
        calendarGrid.appendChild(cell);
        currentDate.setDate(currentDate.getDate() + 1);
    }
    
    // 이전/다음 월 버튼
    document.getElementById('prevMonthBtn').onclick = () => {
        currentMonth = new Date(year, month - 1, 1);
        renderCalendarView();
    };
    
    document.getElementById('nextMonthBtn').onclick = () => {
        currentMonth = new Date(year, month + 1, 1);
        renderCalendarView();
    };
}

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderTodos();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    const remaining = todos.filter(t => !t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
    document.getElementById('remainingCount').textContent = remaining;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderCurrentView();
});

// 뷰 모드 전환
const viewBtns = document.querySelectorAll('.view-btn');
viewBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        currentView = btn.dataset.view;
        viewBtns.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        renderCurrentView();
    });
});

// 현재 뷰 렌더링
function renderCurrentView() {
    if (currentView === 'list') {
        document.getElementById('todoList').classList.remove('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderTodos();
    } else if (currentView === 'week') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.remove('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderWeekView();
    } else if (currentView === 'calendar') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.remove('hidden');
        renderCalendarView();
    }
}

// 주간 뷰 렌더링
function renderWeekView() {
    const weekDays = document.getElementById('weekDays');
    weekDays.innerHTML = '';
    
    // 주의 시작일 계산 (월요일)
    const start = new Date(currentWeekStart);
    const day = start.getDay();
    const diff = start.getDate() - day + (day === 0 ? -6 : 1); // 월요일로 조정
    start.setDate(diff);
    start.setHours(0, 0, 0, 0);
    
    // 주의 종료일 (일요일)
    const end = new Date(start);
    end.setDate(end.getDate() + 6);
    end.setHours(23, 59, 59, 999);
    
    // 주 제목 업데이트
    const weekTitle = document.getElementById('weekTitle');
    const month = start.getMonth() + 1;
    const startDay = start.getDate();
    const endMonth = end.getMonth() + 1;
    const endDay = end.getDate();
    weekTitle.textContent = `${start.getFullYear()}년 ${month}월 ${startDay}일 ~ ${endMonth}월 ${endDay}일`;
    
    // 각 날짜별 할 일 표시
    for (let i = 0; i < 7; i++) {
        const date = new Date(start);
        date.setDate(date.getDate() + i);
        const dateStr = date.toISOString().split('T')[0];
        
        const dayDiv = document.createElement('div');
        dayDiv.className = 'week-day';
        
        const dayHeader = document.createElement('div');
        dayHeader.className = 'week-day-header';
        const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
        dayHeader.innerHTML = `
            <div class="week-day-name">${dayNames[date.getDay()]}</div>
            <div class="week-day-date">${date.getDate()}</div>
        `;
        
        const dayTodos = document.createElement('div');
        dayTodos.className = 'week-day-todos';
        
        // 해당 날짜의 할 일 필터링
        const dayTodosList = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodosList.length === 0) {
            dayTodos.innerHTML = '<div class="week-empty">할 일 없음</div>';
        } else {
            dayTodosList.forEach(todo => {
                const todoItem = document.createElement('div');
                todoItem.className = 'week-todo-item';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoItem.classList.add('overdue');
                if (isUrgentItem) todoItem.classList.add('urgent');
                
                todoItem.innerHTML = `
                    <input type="checkbox" class="todo-checkbox" ${todo.completed ? 'checked' : ''} 
                        onchange="toggleTodo(${todo.id})">
                    <span class="week-todo-text">${todo.text}</span>
                `;
                dayTodos.appendChild(todoItem);
            });
        }
        
        dayDiv.appendChild(dayHeader);
        dayDiv.appendChild(dayTodos);
        weekDays.appendChild(dayDiv);
    }
    
    // 이전/다음 주 버튼
    document.getElementById('prevWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() - 7);
        renderWeekView();
    };
    
    document.getElementById('nextWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() + 7);
        renderWeekView();
    };
}

// 캘린더 뷰 렌더링
function renderCalendarView() {
    const calendarGrid = document.getElementById('calendarGrid');
    calendarGrid.innerHTML = '';
    
    const year = currentMonth.getFullYear();
    const month = currentMonth.getMonth();
    
    // 캘린더 제목 업데이트
    document.getElementById('calendarTitle').textContent = `${year}년 ${month + 1}월`;
    
    // 월의 첫 날과 마지막 날
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const startDate = new Date(firstDay);
    startDate.setDate(startDate.getDate() - firstDay.getDay()); // 주의 시작일로 조정
    
    // 요일 헤더
    const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
    dayNames.forEach(day => {
        const header = document.createElement('div');
        header.className = 'calendar-day-header';
        header.textContent = day;
        calendarGrid.appendChild(header);
    });
    
    // 날짜 셀 생성
    const currentDate = new Date(startDate);
    for (let i = 0; i < 42; i++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day';
        
        const dateStr = currentDate.toISOString().split('T')[0];
        const isCurrentMonth = currentDate.getMonth() === month;
        const isToday = dateStr === new Date().toISOString().split('T')[0];
        
        if (!isCurrentMonth) {
            cell.classList.add('other-month');
        }
        if (isToday) {
            cell.classList.add('today');
        }
        
        const dayNumber = document.createElement('div');
        dayNumber.className = 'calendar-day-number';
        dayNumber.textContent = currentDate.getDate();
        cell.appendChild(dayNumber);
        
        // 해당 날짜의 할 일 표시
        const dayTodos = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodos.length > 0) {
            const todosContainer = document.createElement('div');
            todosContainer.className = 'calendar-todos';
            
            dayTodos.slice(0, 3).forEach(todo => {
                const todoDot = document.createElement('div');
                todoDot.className = 'calendar-todo-dot';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoDot.classList.add('overdue');
                else if (isUrgentItem) todoDot.classList.add('urgent');
                todosContainer.appendChild(todoDot);
            });
            
            if (dayTodos.length > 3) {
                const more = document.createElement('div');
                more.className = 'calendar-more';
                more.textContent = `+${dayTodos.length - 3}`;
                todosContainer.appendChild(more);
            }
            
            cell.appendChild(todosContainer);
        }
        
        calendarGrid.appendChild(cell);
        currentDate.setDate(currentDate.getDate() + 1);
    }
    
    // 이전/다음 월 버튼
    document.getElementById('prevMonthBtn').onclick = () => {
        currentMonth = new Date(year, month - 1, 1);
        renderCalendarView();
    };
    
    document.getElementById('nextMonthBtn').onclick = () => {
        currentMonth = new Date(year, month + 1, 1);
        renderCalendarView();
    };
}

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderTodos();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderCurrentView();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    const remaining = todos.filter(t => !t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
    document.getElementById('remainingCount').textContent = remaining;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderCurrentView();
});

// 뷰 모드 전환
const viewBtns = document.querySelectorAll('.view-btn');
viewBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        currentView = btn.dataset.view;
        viewBtns.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        renderCurrentView();
    });
});

// 현재 뷰 렌더링
function renderCurrentView() {
    if (currentView === 'list') {
        document.getElementById('todoList').classList.remove('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderTodos();
    } else if (currentView === 'week') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.remove('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderWeekView();
    } else if (currentView === 'calendar') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.remove('hidden');
        renderCalendarView();
    }
}

// 주간 뷰 렌더링
function renderWeekView() {
    const weekDays = document.getElementById('weekDays');
    weekDays.innerHTML = '';
    
    // 주의 시작일 계산 (월요일)
    const start = new Date(currentWeekStart);
    const day = start.getDay();
    const diff = start.getDate() - day + (day === 0 ? -6 : 1); // 월요일로 조정
    start.setDate(diff);
    start.setHours(0, 0, 0, 0);
    
    // 주의 종료일 (일요일)
    const end = new Date(start);
    end.setDate(end.getDate() + 6);
    end.setHours(23, 59, 59, 999);
    
    // 주 제목 업데이트
    const weekTitle = document.getElementById('weekTitle');
    const month = start.getMonth() + 1;
    const startDay = start.getDate();
    const endMonth = end.getMonth() + 1;
    const endDay = end.getDate();
    weekTitle.textContent = `${start.getFullYear()}년 ${month}월 ${startDay}일 ~ ${endMonth}월 ${endDay}일`;
    
    // 각 날짜별 할 일 표시
    for (let i = 0; i < 7; i++) {
        const date = new Date(start);
        date.setDate(date.getDate() + i);
        const dateStr = date.toISOString().split('T')[0];
        
        const dayDiv = document.createElement('div');
        dayDiv.className = 'week-day';
        
        const dayHeader = document.createElement('div');
        dayHeader.className = 'week-day-header';
        const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
        dayHeader.innerHTML = `
            <div class="week-day-name">${dayNames[date.getDay()]}</div>
            <div class="week-day-date">${date.getDate()}</div>
        `;
        
        const dayTodos = document.createElement('div');
        dayTodos.className = 'week-day-todos';
        
        // 해당 날짜의 할 일 필터링
        const dayTodosList = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodosList.length === 0) {
            dayTodos.innerHTML = '<div class="week-empty">할 일 없음</div>';
        } else {
            dayTodosList.forEach(todo => {
                const todoItem = document.createElement('div');
                todoItem.className = 'week-todo-item';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoItem.classList.add('overdue');
                if (isUrgentItem) todoItem.classList.add('urgent');
                
                todoItem.innerHTML = `
                    <input type="checkbox" class="todo-checkbox" ${todo.completed ? 'checked' : ''} 
                        onchange="toggleTodo(${todo.id})">
                    <span class="week-todo-text">${todo.text}</span>
                `;
                dayTodos.appendChild(todoItem);
            });
        }
        
        dayDiv.appendChild(dayHeader);
        dayDiv.appendChild(dayTodos);
        weekDays.appendChild(dayDiv);
    }
    
    // 이전/다음 주 버튼
    document.getElementById('prevWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() - 7);
        renderWeekView();
    };
    
    document.getElementById('nextWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() + 7);
        renderWeekView();
    };
}

// 캘린더 뷰 렌더링
function renderCalendarView() {
    const calendarGrid = document.getElementById('calendarGrid');
    calendarGrid.innerHTML = '';
    
    const year = currentMonth.getFullYear();
    const month = currentMonth.getMonth();
    
    // 캘린더 제목 업데이트
    document.getElementById('calendarTitle').textContent = `${year}년 ${month + 1}월`;
    
    // 월의 첫 날과 마지막 날
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const startDate = new Date(firstDay);
    startDate.setDate(startDate.getDate() - firstDay.getDay()); // 주의 시작일로 조정
    
    // 요일 헤더
    const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
    dayNames.forEach(day => {
        const header = document.createElement('div');
        header.className = 'calendar-day-header';
        header.textContent = day;
        calendarGrid.appendChild(header);
    });
    
    // 날짜 셀 생성
    const currentDate = new Date(startDate);
    for (let i = 0; i < 42; i++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day';
        
        const dateStr = currentDate.toISOString().split('T')[0];
        const isCurrentMonth = currentDate.getMonth() === month;
        const isToday = dateStr === new Date().toISOString().split('T')[0];
        
        if (!isCurrentMonth) {
            cell.classList.add('other-month');
        }
        if (isToday) {
            cell.classList.add('today');
        }
        
        const dayNumber = document.createElement('div');
        dayNumber.className = 'calendar-day-number';
        dayNumber.textContent = currentDate.getDate();
        cell.appendChild(dayNumber);
        
        // 해당 날짜의 할 일 표시
        const dayTodos = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodos.length > 0) {
            const todosContainer = document.createElement('div');
            todosContainer.className = 'calendar-todos';
            
            dayTodos.slice(0, 3).forEach(todo => {
                const todoDot = document.createElement('div');
                todoDot.className = 'calendar-todo-dot';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoDot.classList.add('overdue');
                else if (isUrgentItem) todoDot.classList.add('urgent');
                todosContainer.appendChild(todoDot);
            });
            
            if (dayTodos.length > 3) {
                const more = document.createElement('div');
                more.className = 'calendar-more';
                more.textContent = `+${dayTodos.length - 3}`;
                todosContainer.appendChild(more);
            }
            
            cell.appendChild(todosContainer);
        }
        
        calendarGrid.appendChild(cell);
        currentDate.setDate(currentDate.getDate() + 1);
    }
    
    // 이전/다음 월 버튼
    document.getElementById('prevMonthBtn').onclick = () => {
        currentMonth = new Date(year, month - 1, 1);
        renderCalendarView();
    };
    
    document.getElementById('nextMonthBtn').onclick = () => {
        currentMonth = new Date(year, month + 1, 1);
        renderCalendarView();
    };
}

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderCurrentView();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderCurrentView();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    const remaining = todos.filter(t => !t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
    document.getElementById('remainingCount').textContent = remaining;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderTodos();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderCurrentView();
});

// 뷰 모드 전환
const viewBtns = document.querySelectorAll('.view-btn');
viewBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        currentView = btn.dataset.view;
        viewBtns.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        renderCurrentView();
    });
});

// 현재 뷰 렌더링
function renderCurrentView() {
    if (currentView === 'list') {
        document.getElementById('todoList').classList.remove('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderTodos();
    } else if (currentView === 'week') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.remove('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderWeekView();
    } else if (currentView === 'calendar') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.remove('hidden');
        renderCalendarView();
    }
}

// 주간 뷰 렌더링
function renderWeekView() {
    const weekDays = document.getElementById('weekDays');
    weekDays.innerHTML = '';
    
    // 주의 시작일 계산 (월요일)
    const start = new Date(currentWeekStart);
    const day = start.getDay();
    const diff = start.getDate() - day + (day === 0 ? -6 : 1); // 월요일로 조정
    start.setDate(diff);
    start.setHours(0, 0, 0, 0);
    
    // 주의 종료일 (일요일)
    const end = new Date(start);
    end.setDate(end.getDate() + 6);
    end.setHours(23, 59, 59, 999);
    
    // 주 제목 업데이트
    const weekTitle = document.getElementById('weekTitle');
    const month = start.getMonth() + 1;
    const startDay = start.getDate();
    const endMonth = end.getMonth() + 1;
    const endDay = end.getDate();
    weekTitle.textContent = `${start.getFullYear()}년 ${month}월 ${startDay}일 ~ ${endMonth}월 ${endDay}일`;
    
    // 각 날짜별 할 일 표시
    for (let i = 0; i < 7; i++) {
        const date = new Date(start);
        date.setDate(date.getDate() + i);
        const dateStr = date.toISOString().split('T')[0];
        
        const dayDiv = document.createElement('div');
        dayDiv.className = 'week-day';
        
        const dayHeader = document.createElement('div');
        dayHeader.className = 'week-day-header';
        const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
        dayHeader.innerHTML = `
            <div class="week-day-name">${dayNames[date.getDay()]}</div>
            <div class="week-day-date">${date.getDate()}</div>
        `;
        
        const dayTodos = document.createElement('div');
        dayTodos.className = 'week-day-todos';
        
        // 해당 날짜의 할 일 필터링
        const dayTodosList = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodosList.length === 0) {
            dayTodos.innerHTML = '<div class="week-empty">할 일 없음</div>';
        } else {
            dayTodosList.forEach(todo => {
                const todoItem = document.createElement('div');
                todoItem.className = 'week-todo-item';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoItem.classList.add('overdue');
                if (isUrgentItem) todoItem.classList.add('urgent');
                
                todoItem.innerHTML = `
                    <input type="checkbox" class="todo-checkbox" ${todo.completed ? 'checked' : ''} 
                        onchange="toggleTodo(${todo.id})">
                    <span class="week-todo-text">${todo.text}</span>
                `;
                dayTodos.appendChild(todoItem);
            });
        }
        
        dayDiv.appendChild(dayHeader);
        dayDiv.appendChild(dayTodos);
        weekDays.appendChild(dayDiv);
    }
    
    // 이전/다음 주 버튼
    document.getElementById('prevWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() - 7);
        renderWeekView();
    };
    
    document.getElementById('nextWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() + 7);
        renderWeekView();
    };
}

// 캘린더 뷰 렌더링
function renderCalendarView() {
    const calendarGrid = document.getElementById('calendarGrid');
    calendarGrid.innerHTML = '';
    
    const year = currentMonth.getFullYear();
    const month = currentMonth.getMonth();
    
    // 캘린더 제목 업데이트
    document.getElementById('calendarTitle').textContent = `${year}년 ${month + 1}월`;
    
    // 월의 첫 날과 마지막 날
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const startDate = new Date(firstDay);
    startDate.setDate(startDate.getDate() - firstDay.getDay()); // 주의 시작일로 조정
    
    // 요일 헤더
    const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
    dayNames.forEach(day => {
        const header = document.createElement('div');
        header.className = 'calendar-day-header';
        header.textContent = day;
        calendarGrid.appendChild(header);
    });
    
    // 날짜 셀 생성
    const currentDate = new Date(startDate);
    for (let i = 0; i < 42; i++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day';
        
        const dateStr = currentDate.toISOString().split('T')[0];
        const isCurrentMonth = currentDate.getMonth() === month;
        const isToday = dateStr === new Date().toISOString().split('T')[0];
        
        if (!isCurrentMonth) {
            cell.classList.add('other-month');
        }
        if (isToday) {
            cell.classList.add('today');
        }
        
        const dayNumber = document.createElement('div');
        dayNumber.className = 'calendar-day-number';
        dayNumber.textContent = currentDate.getDate();
        cell.appendChild(dayNumber);
        
        // 해당 날짜의 할 일 표시
        const dayTodos = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodos.length > 0) {
            const todosContainer = document.createElement('div');
            todosContainer.className = 'calendar-todos';
            
            dayTodos.slice(0, 3).forEach(todo => {
                const todoDot = document.createElement('div');
                todoDot.className = 'calendar-todo-dot';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoDot.classList.add('overdue');
                else if (isUrgentItem) todoDot.classList.add('urgent');
                todosContainer.appendChild(todoDot);
            });
            
            if (dayTodos.length > 3) {
                const more = document.createElement('div');
                more.className = 'calendar-more';
                more.textContent = `+${dayTodos.length - 3}`;
                todosContainer.appendChild(more);
            }
            
            cell.appendChild(todosContainer);
        }
        
        calendarGrid.appendChild(cell);
        currentDate.setDate(currentDate.getDate() + 1);
    }
    
    // 이전/다음 월 버튼
    document.getElementById('prevMonthBtn').onclick = () => {
        currentMonth = new Date(year, month - 1, 1);
        renderCalendarView();
    };
    
    document.getElementById('nextMonthBtn').onclick = () => {
        currentMonth = new Date(year, month + 1, 1);
        renderCalendarView();
    };
}

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```

```javascript
// DOM 요소 가져오기
const todoInput = document.getElementById('todoInput');
const dueDateInput = document.getElementById('dueDateInput');
const addBtn = document.getElementById('addBtn');
const todoList = document.getElementById('todoList');
const completedCount = document.getElementById('completedCount');
const totalCount = document.getElementById('totalCount');
const searchInput = document.getElementById('searchInput');
const filterBtns = document.querySelectorAll('.filter-btn');
const clearCompletedBtn = document.getElementById('clearCompletedBtn');

// localStorage에서 데이터 불러오기
let todos = JSON.parse(localStorage.getItem('todos')) || [];

// 필터 및 검색 상태
let currentFilter = 'all';
let searchQuery = '';
let editingId = null;
let sortByDueDate = false;
let currentView = 'list';
let currentWeekStart = new Date();
let currentMonth = new Date();

// 페이지 로드 시 저장된 할 일 목록 표시
document.addEventListener('DOMContentLoaded', () => {
    // 기존 데이터 마이그레이션: completed가 true인데 completedDate가 없는 경우 처리
    todos.forEach(todo => {
        if (todo.completed && !todo.completedDate) {
            // 기존 완료 항목은 날짜를 null로 유지 (표시하지 않음)
            todo.completedDate = null;
        }
    });
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 브라우저 알림 권한 요청
    requestNotificationPermission();
    
    // 알림 체크 시작 (1분마다)
    setInterval(checkDueDateNotifications, 60000);
    
    // 페이지 로드 시 즉시 체크
    checkDueDateNotifications();
});

// 할 일 추가 함수
function addTodo() {
    const text = todoInput.value.trim();
    
    if (text === '') {
        alert('할 일을 입력해주세요!');
        return;
    }
    
    const dueDate = dueDateInput.value || null;
    
    const todo = {
        id: Date.now(),
        text: text,
        completed: false,
        dueDate: dueDate
    };
    
    todos.push(todo);
    saveTodos();
    renderCurrentView();
    updateCount();
    
    // 입력 필드 초기화
    todoInput.value = '';
    dueDateInput.value = '';
    todoInput.focus();
}

// 필터링 및 검색된 할 일 목록 가져오기
function getFilteredTodos() {
    let filtered = todos;
    
    // 검색 필터
    if (searchQuery.trim() !== '') {
        filtered = filtered.filter(todo => 
            todo.text.toLowerCase().includes(searchQuery.toLowerCase())
        );
    }
    
    // 상태 필터
    if (currentFilter === 'active') {
        filtered = filtered.filter(todo => !todo.completed);
    } else if (currentFilter === 'completed') {
        filtered = filtered.filter(todo => todo.completed);
    }
    
    // 마감순 정렬
    if (sortByDueDate) {
        filtered = sortTodosByDueDate(filtered);
    }
    
    return filtered;
}

// 마감순 정렬 함수
function sortTodosByDueDate(todos) {
    return [...todos].sort((a, b) => {
        // 완료된 항목은 맨 아래
        if (a.completed && !b.completed) return 1;
        if (!a.completed && b.completed) return -1;
        
        // 둘 다 완료되었거나 둘 다 미완료인 경우
        // 기한이 없는 항목은 맨 아래
        if (!a.dueDate && !b.dueDate) return 0;
        if (!a.dueDate) return 1;
        if (!b.dueDate) return -1;
        
        // 마감일이 가까운 순으로 정렬
        const dateA = new Date(a.dueDate + 'T23:59:59');
        const dateB = new Date(b.dueDate + 'T23:59:59');
        return dateA - dateB;
    });
}

// 할 일 목록 렌더링 함수
function renderTodos() {
    todoList.innerHTML = '';
    
    const filteredTodos = getFilteredTodos();
    
    if (filteredTodos.length === 0) {
        if (todos.length === 0) {
            todoList.innerHTML = '<li class="empty-message">할 일이 없습니다. 새로운 할 일을 추가해보세요! ✨</li>';
        } else {
            todoList.innerHTML = '<li class="empty-message">검색 결과가 없습니다. 🔍</li>';
        }
        return;
    }
    
    filteredTodos.forEach(todo => {
        const li = document.createElement('li');
        const isOverdueItem = !todo.completed && isOverdue(todo.dueDate);
        const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
        li.className = `todo-item ${todo.completed ? 'completed' : ''} ${isOverdueItem ? 'overdue' : ''} ${isUrgentItem ? 'urgent' : ''}`;
        
        const checkbox = document.createElement('input');
        checkbox.type = 'checkbox';
        checkbox.className = 'todo-checkbox';
        checkbox.checked = todo.completed;
        checkbox.addEventListener('change', () => toggleTodo(todo.id));
        
        if (editingId === todo.id) {
            // 편집 모드
            const editInput = document.createElement('input');
            editInput.type = 'text';
            editInput.className = 'todo-edit-input';
            editInput.value = todo.text;
            editInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveEdit(todo.id, editInput.value);
                } else if (e.key === 'Escape') {
                    cancelEdit();
                }
            });
            editInput.addEventListener('blur', () => {
                saveEdit(todo.id, editInput.value);
            });
            
            const saveBtn = document.createElement('button');
            saveBtn.className = 'edit-btn';
            saveBtn.textContent = '저장';
            saveBtn.addEventListener('click', () => saveEdit(todo.id, editInput.value));
            
            li.appendChild(checkbox);
            li.appendChild(editInput);
            li.appendChild(saveBtn);
            
            setTimeout(() => editInput.focus(), 0);
        } else {
            // 일반 모드
            const textContainer = document.createElement('div');
            textContainer.className = 'todo-text-container';
            
            const textSpan = document.createElement('span');
            textSpan.className = 'todo-text';
            textSpan.textContent = todo.text;
            textSpan.addEventListener('dblclick', () => startEdit(todo.id));
            
            const dateInfoContainer = document.createElement('div');
            dateInfoContainer.className = 'date-info-container';
            
            // 완료 날짜 표시
            if (todo.completed && todo.completedDate) {
                const completedDateSpan = document.createElement('span');
                completedDateSpan.className = 'todo-date completed-date';
                completedDateSpan.textContent = `완료: ${formatDate(todo.completedDate)}`;
                dateInfoContainer.appendChild(completedDateSpan);
            }
            
            // 완료 기한 표시
            if (todo.dueDate) {
                const dueDateSpan = document.createElement('span');
                const isUrgentItem = !todo.completed && isUrgent(todo.dueDate);
                dueDateSpan.className = `todo-date due-date ${isOverdueItem ? 'overdue-date' : ''} ${isUrgentItem ? 'urgent-date' : ''}`;
                dueDateSpan.textContent = formatDueDate(todo.dueDate);
                dateInfoContainer.appendChild(dueDateSpan);
            }
            
            textContainer.appendChild(textSpan);
            if (dateInfoContainer.children.length > 0) {
                textContainer.appendChild(dateInfoContainer);
            }
            
            const editBtn = document.createElement('button');
            editBtn.className = 'edit-btn';
            editBtn.textContent = '수정';
            editBtn.addEventListener('click', () => startEdit(todo.id));
            
            const deleteBtn = document.createElement('button');
            deleteBtn.className = 'delete-btn';
            deleteBtn.textContent = '삭제';
            deleteBtn.addEventListener('click', () => deleteTodo(todo.id));
            
            li.appendChild(checkbox);
            li.appendChild(textContainer);
            li.appendChild(editBtn);
            li.appendChild(deleteBtn);
        }
        
        todoList.appendChild(li);
    });
}

// 날짜 포맷팅 함수 (완료 날짜용)
function formatDate(dateString) {
    if (!dateString) return '';
    
    const date = new Date(dateString);
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const todoDate = new Date(date.getFullYear(), date.getMonth(), date.getDate());
    
    const year = date.getFullYear();
    const month = String(date.getMonth() + 1).padStart(2, '0');
    const day = String(date.getDate()).padStart(2, '0');
    const hours = String(date.getHours()).padStart(2, '0');
    const minutes = String(date.getMinutes()).padStart(2, '0');
    
    // 오늘인지 확인
    if (todoDate.getTime() === today.getTime()) {
        return `오늘 ${hours}:${minutes}`;
    }
    
    // 어제인지 확인
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (todoDate.getTime() === yesterday.getTime()) {
        return `어제 ${hours}:${minutes}`;
    }
    
    // 올해인지 확인
    if (date.getFullYear() === now.getFullYear()) {
        return `${month}/${day} ${hours}:${minutes}`;
    }
    
    // 다른 해
    return `${year}/${month}/${day} ${hours}:${minutes}`;
}

// 완료 기한 포맷팅 함수
function formatDueDate(dateString) {
    if (!dateString) return '';
    
    const dueDate = new Date(dateString + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(dueDate.getFullYear(), dueDate.getMonth(), dueDate.getDate());
    
    const year = dueDate.getFullYear();
    const month = String(dueDate.getMonth() + 1).padStart(2, '0');
    const day = String(dueDate.getDate()).padStart(2, '0');
    
    // 오늘인지 확인
    if (dueDateOnly.getTime() === today.getTime()) {
        return '오늘까지';
    }
    
    // 어제인지 확인 (만료됨)
    const yesterday = new Date(today);
    yesterday.setDate(yesterday.getDate() - 1);
    if (dueDateOnly.getTime() === yesterday.getTime()) {
        return '어제까지 (만료)';
    }
    
    // 과거인지 확인
    if (dueDateOnly.getTime() < today.getTime()) {
        return `${year}/${month}/${day} (만료)`;
    }
    
    // 내일인지 확인
    const tomorrow = new Date(today);
    tomorrow.setDate(tomorrow.getDate() + 1);
    if (dueDateOnly.getTime() === tomorrow.getTime()) {
        return '내일까지';
    }
    
    // 올해인지 확인
    if (dueDate.getFullYear() === now.getFullYear()) {
        return `${month}/${day}까지`;
    }
    
    // 다른 해
    return `${year}/${month}/${day}까지`;
}

// 완료 기한이 지났는지 확인
function isOverdue(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const dueDateOnly = new Date(due.getFullYear(), due.getMonth(), due.getDate());
    
    return dueDateOnly.getTime() < today.getTime();
}

// 마감 임박(24시간 이내)인지 확인
function isUrgent(dueDate) {
    if (!dueDate) return false;
    
    const due = new Date(dueDate + 'T23:59:59');
    const now = new Date();
    const hoursUntilDue = (due - now) / (1000 * 60 * 60);
    
    // 24시간 이내이고 아직 지나지 않았으면 임박
    return hoursUntilDue > 0 && hoursUntilDue <= 24;
}

// 할 일 완료 상태 토글 함수
function toggleTodo(id) {
    const todo = todos.find(t => t.id === id);
    if (todo) {
        todo.completed = !todo.completed;
        // 완료할 때 날짜/시간 저장, 미완료로 되돌릴 때는 제거
        if (todo.completed) {
            todo.completedDate = new Date().toISOString();
        } else {
            todo.completedDate = null;
        }
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// 할 일 삭제 함수
function deleteTodo(id) {
    if (confirm('정말 삭제하시겠습니까?')) {
        todos = todos.filter(t => t.id !== id);
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// localStorage에 저장하는 함수
function saveTodos() {
    localStorage.setItem('todos', JSON.stringify(todos));
}

// 할 일 수정 시작 함수
function startEdit(id) {
    editingId = id;
    renderCurrentView();
}

// 할 일 수정 저장 함수
function saveEdit(id, newText) {
    const todo = todos.find(t => t.id === id);
    if (todo && newText.trim() !== '') {
        todo.text = newText.trim();
        saveTodos();
        editingId = null;
        renderTodos();
    } else if (newText.trim() === '') {
        alert('할 일 내용을 입력해주세요!');
    } else {
        editingId = null;
        renderTodos();
    }
}

// 할 일 수정 취소 함수
function cancelEdit() {
    editingId = null;
    renderTodos();
}

// 완료된 항목 모두 삭제 함수
function clearCompleted() {
    const completedCount = todos.filter(t => t.completed).length;
    if (completedCount === 0) {
        alert('완료된 항목이 없습니다.');
        return;
    }
    
    if (confirm(`완료된 ${completedCount}개의 항목을 모두 삭제하시겠습니까?`)) {
        todos = todos.filter(t => !t.completed);
        saveTodos();
        renderCurrentView();
        updateCount();
    }
}

// 필터 변경 함수
function setFilter(filter) {
    currentFilter = filter;
    filterBtns.forEach(btn => {
        if (btn.dataset.filter === filter) {
            btn.classList.add('active');
        } else {
            btn.classList.remove('active');
        }
    });
    renderCurrentView();
}

// 카운트 업데이트 함수
function updateCount() {
    const completed = todos.filter(t => t.completed).length;
    const remaining = todos.filter(t => !t.completed).length;
    completedCount.textContent = completed;
    totalCount.textContent = todos.length;
    document.getElementById('remainingCount').textContent = remaining;
}

// 이벤트 리스너
addBtn.addEventListener('click', addTodo);

todoInput.addEventListener('keypress', (e) => {
    if (e.key === 'Enter') {
        addTodo();
    }
});

// 검색 기능
searchInput.addEventListener('input', (e) => {
    searchQuery = e.target.value;
    renderCurrentView();
});

// 필터 버튼 이벤트
filterBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        setFilter(btn.dataset.filter);
    });
});

// 완료된 항목 모두 삭제 버튼
clearCompletedBtn.addEventListener('click', clearCompleted);

// 정렬 버튼
const sortBtn = document.getElementById('sortBtn');
const sortIcon = document.getElementById('sortIcon');
sortBtn.addEventListener('click', () => {
    sortByDueDate = !sortByDueDate;
    if (sortByDueDate) {
        sortIcon.textContent = '⬇️';
        sortBtn.classList.add('active');
    } else {
        sortIcon.textContent = '🔀';
        sortBtn.classList.remove('active');
    }
    renderCurrentView();
});

// 뷰 모드 전환
const viewBtns = document.querySelectorAll('.view-btn');
viewBtns.forEach(btn => {
    btn.addEventListener('click', () => {
        currentView = btn.dataset.view;
        viewBtns.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        renderCurrentView();
    });
});

// 현재 뷰 렌더링
function renderCurrentView() {
    if (currentView === 'list') {
        document.getElementById('todoList').classList.remove('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderTodos();
    } else if (currentView === 'week') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.remove('hidden');
        document.getElementById('calendarView').classList.add('hidden');
        renderWeekView();
    } else if (currentView === 'calendar') {
        document.getElementById('todoList').classList.add('hidden');
        document.getElementById('weekView').classList.add('hidden');
        document.getElementById('calendarView').classList.remove('hidden');
        renderCalendarView();
    }
}

// 주간 뷰 렌더링
function renderWeekView() {
    const weekDays = document.getElementById('weekDays');
    weekDays.innerHTML = '';
    
    // 주의 시작일 계산 (월요일)
    const start = new Date(currentWeekStart);
    const day = start.getDay();
    const diff = start.getDate() - day + (day === 0 ? -6 : 1); // 월요일로 조정
    start.setDate(diff);
    start.setHours(0, 0, 0, 0);
    
    // 주의 종료일 (일요일)
    const end = new Date(start);
    end.setDate(end.getDate() + 6);
    end.setHours(23, 59, 59, 999);
    
    // 주 제목 업데이트
    const weekTitle = document.getElementById('weekTitle');
    const month = start.getMonth() + 1;
    const startDay = start.getDate();
    const endMonth = end.getMonth() + 1;
    const endDay = end.getDate();
    weekTitle.textContent = `${start.getFullYear()}년 ${month}월 ${startDay}일 ~ ${endMonth}월 ${endDay}일`;
    
    // 각 날짜별 할 일 표시
    for (let i = 0; i < 7; i++) {
        const date = new Date(start);
        date.setDate(date.getDate() + i);
        const dateStr = date.toISOString().split('T')[0];
        
        const dayDiv = document.createElement('div');
        dayDiv.className = 'week-day';
        
        const dayHeader = document.createElement('div');
        dayHeader.className = 'week-day-header';
        const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
        dayHeader.innerHTML = `
            <div class="week-day-name">${dayNames[date.getDay()]}</div>
            <div class="week-day-date">${date.getDate()}</div>
        `;
        
        const dayTodos = document.createElement('div');
        dayTodos.className = 'week-day-todos';
        
        // 해당 날짜의 할 일 필터링
        const dayTodosList = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodosList.length === 0) {
            dayTodos.innerHTML = '<div class="week-empty">할 일 없음</div>';
        } else {
            dayTodosList.forEach(todo => {
                const todoItem = document.createElement('div');
                todoItem.className = 'week-todo-item';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoItem.classList.add('overdue');
                if (isUrgentItem) todoItem.classList.add('urgent');
                
                todoItem.innerHTML = `
                    <input type="checkbox" class="todo-checkbox" ${todo.completed ? 'checked' : ''} 
                        onchange="toggleTodo(${todo.id})">
                    <span class="week-todo-text">${todo.text}</span>
                `;
                dayTodos.appendChild(todoItem);
            });
        }
        
        dayDiv.appendChild(dayHeader);
        dayDiv.appendChild(dayTodos);
        weekDays.appendChild(dayDiv);
    }
    
    // 이전/다음 주 버튼
    document.getElementById('prevWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() - 7);
        renderWeekView();
    };
    
    document.getElementById('nextWeekBtn').onclick = () => {
        currentWeekStart = new Date(start);
        currentWeekStart.setDate(currentWeekStart.getDate() + 7);
        renderWeekView();
    };
}

// 캘린더 뷰 렌더링
function renderCalendarView() {
    const calendarGrid = document.getElementById('calendarGrid');
    calendarGrid.innerHTML = '';
    
    const year = currentMonth.getFullYear();
    const month = currentMonth.getMonth();
    
    // 캘린더 제목 업데이트
    document.getElementById('calendarTitle').textContent = `${year}년 ${month + 1}월`;
    
    // 월의 첫 날과 마지막 날
    const firstDay = new Date(year, month, 1);
    const lastDay = new Date(year, month + 1, 0);
    const startDate = new Date(firstDay);
    startDate.setDate(startDate.getDate() - firstDay.getDay()); // 주의 시작일로 조정
    
    // 요일 헤더
    const dayNames = ['일', '월', '화', '수', '목', '금', '토'];
    dayNames.forEach(day => {
        const header = document.createElement('div');
        header.className = 'calendar-day-header';
        header.textContent = day;
        calendarGrid.appendChild(header);
    });
    
    // 날짜 셀 생성
    const currentDate = new Date(startDate);
    for (let i = 0; i < 42; i++) {
        const cell = document.createElement('div');
        cell.className = 'calendar-day';
        
        const dateStr = currentDate.toISOString().split('T')[0];
        const isCurrentMonth = currentDate.getMonth() === month;
        const isToday = dateStr === new Date().toISOString().split('T')[0];
        
        if (!isCurrentMonth) {
            cell.classList.add('other-month');
        }
        if (isToday) {
            cell.classList.add('today');
        }
        
        const dayNumber = document.createElement('div');
        dayNumber.className = 'calendar-day-number';
        dayNumber.textContent = currentDate.getDate();
        cell.appendChild(dayNumber);
        
        // 해당 날짜의 할 일 표시
        const dayTodos = todos.filter(todo => {
            if (todo.completed) return false;
            if (!todo.dueDate) return false;
            return todo.dueDate === dateStr;
        });
        
        if (dayTodos.length > 0) {
            const todosContainer = document.createElement('div');
            todosContainer.className = 'calendar-todos';
            
            dayTodos.slice(0, 3).forEach(todo => {
                const todoDot = document.createElement('div');
                todoDot.className = 'calendar-todo-dot';
                const isOverdueItem = isOverdue(todo.dueDate);
                const isUrgentItem = isUrgent(todo.dueDate);
                if (isOverdueItem) todoDot.classList.add('overdue');
                else if (isUrgentItem) todoDot.classList.add('urgent');
                todosContainer.appendChild(todoDot);
            });
            
            if (dayTodos.length > 3) {
                const more = document.createElement('div');
                more.className = 'calendar-more';
                more.textContent = `+${dayTodos.length - 3}`;
                todosContainer.appendChild(more);
            }
            
            cell.appendChild(todosContainer);
        }
        
        calendarGrid.appendChild(cell);
        currentDate.setDate(currentDate.getDate() + 1);
    }
    
    // 이전/다음 월 버튼
    document.getElementById('prevMonthBtn').onclick = () => {
        currentMonth = new Date(year, month - 1, 1);
        renderCalendarView();
    };
    
    document.getElementById('nextMonthBtn').onclick = () => {
        currentMonth = new Date(year, month + 1, 1);
        renderCalendarView();
    };
}

// 브라우저 알림 권한 요청
function requestNotificationPermission() {
    if ('Notification' in window && Notification.permission === 'default') {
        Notification.requestPermission().then(permission => {
            if (permission === 'granted') {
                console.log('알림 권한이 허용되었습니다.');
            }
        });
    }
}

// 마감 알림 체크 및 표시
function checkDueDateNotifications() {
    if (!('Notification' in window) || Notification.permission !== 'granted') {
        return;
    }
    
    const now = new Date();
    const urgentTodos = todos.filter(todo => {
        if (todo.completed || !todo.dueDate) return false;
        
        const due = new Date(todo.dueDate + 'T23:59:59');
        const hoursUntilDue = (due - now) / (1000 * 60 * 60);
        
        // 24시간 이내이고 아직 지나지 않았으면 알림 대상
        return hoursUntilDue > 0 && hoursUntilDue <= 24;
    });
    
    // 임박한 할 일이 있으면 알림 표시
    if (urgentTodos.length > 0) {
        const todoTexts = urgentTodos.map(t => t.text).join(', ');
        const message = urgentTodos.length === 1 
            ? `"${urgentTodos[0].text}"의 마감이 24시간 이내입니다!`
            : `${urgentTodos.length}개의 할 일이 24시간 이내에 마감됩니다!`;
        
        new Notification('⏰ 마감 임박 알림', {
            body: message,
            icon: 'data:image/svg+xml,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="%23ffc107"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8zm.5-13H11v6l5.25 3.15.75-1.23-4.5-2.67z"/></svg>',
            tag: 'todo-urgent-notification',
            requireInteraction: false
        });
    }
}


```


주간/캘린더 뷰용 CSS 추가 중.


```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 30px;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 30px;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}


```

```css
/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}
```


CSS 파일 끝에 스타일 추가 중.


```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 30px;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```


남은 할 일 개수 표시와 캘린더/주간 보기 기능을 추가했습니다.

## 추가된 기능

### 1. 남은 할 일 개수 표시
- 하단 정보 섹션에 "남은 할 일" 개수를 노란색으로 강조 표시
- 형식: "남은 할 일: X | 완료: Y | 전체: Z"

### 2. 주간 보기
- 주간 뷰 버튼 클릭 시 7일(월~일) 단위로 할 일 표시
- 각 날짜별로 해당 날짜에 마감인 미완료 할 일 표시
- 이전/다음 주 이동 버튼
- 만료/임박 항목은 색상으로 구분

### 3. 캘린더 보기
- 캘린더 뷰 버튼 클릭 시 월 단위 캘린더 표시
- 각 날짜에 할 일이 있으면 점으로 표시
- 점 색상:
  - 파란색: 일반
  - 노란색: 임박(24시간 이내)
  - 빨간색: 만료
- 오늘 날짜는 파란색 배경으로 강조
- 이전/다음 월 이동 버튼
- 한 날짜에 3개 이상이면 "+N" 표시

## 사용 방법

1. 뷰 모드 전환: 상단의 "리스트", "주간", "캘린더" 버튼으로 전환
2. 주간 보기: 주간 뷰에서 ◀/▶ 버튼으로 주 이동
3. 캘린더 보기: 캘린더 뷰에서 ◀/▶ 버튼으로 월 이동

## 주요 특징

- 반응형 디자인: 컨테이너 최대 너비를 1200px로 확장하여 캘린더/주간 뷰에 최적화
- 일관된 색상: 만료(빨강), 임박(노랑), 일반(파랑) 색상 통일
- 실시간 업데이트: 할 일 완료 시 모든 뷰가 자동 업데이트

할 일을 다양한 방식으로 확인하고 관리할 수 있습니다.

---

**User**

UI를 반응협웹으로 만들어줘

---

**Cursor**


반응형 웹으로 전환 중. 모바일, 태블릿, 데스크톱에서 잘 보이도록 조정합니다.



반응형 웹으로 전환 중. 모바일, 태블릿, 데스크톱에서 잘 보이도록 조정합니다.


```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 30px;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2.5em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    align-items: center;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    gap: 10px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 15px;
    padding: 15px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 8px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 10px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 8px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 10px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 10px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 10px;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 10px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 10px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 8px;
    overflow-x: auto;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 10px;
    min-height: 200px;
    background: #f8f9fa;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 10px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 10px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 8px;
    overflow-x: auto;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 8px;
    min-height: 180px;
    background: #f8f9fa;
    min-width: 0;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 5px;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 10px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 10px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 8px;
    overflow-x: auto;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 8px;
    min-height: 180px;
    background: #f8f9fa;
    min-width: 0;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 4px;
    overflow-x: auto;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}


```

```css
.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}

/* 반응형 미디어 쿼리 */

/* 태블릿 (768px ~ 1024px) */
@media (max-width: 1024px) {
    .container {
        padding: 20px;
    }
    
    header h1 {
        font-size: 1.8em;
    }
    
    .week-day {
        min-height: 150px;
    }
}

/* 모바일 (768px 이하) */
@media (max-width: 768px) {
    body {
        padding: 5px;
        align-items: flex-start;
    }
    
    .container {
        padding: 15px;
        border-radius: 15px;
        margin: 5px 0;
    }
    
    header {
        margin-bottom: 20px;
    }
    
    header h1 {
        font-size: 1.5em;
    }
    
    .input-section {
        flex-direction: column;
    }
    
    #todoInput {
        width: 100%;
    }
    
    .due-date-input {
        width: 100%;
        min-width: 100%;
    }
    
    #addBtn {
        width: 100%;
        padding: 15px;
    }
    
    .view-section,
    .filter-section {
        gap: 5px;
    }
    
    .view-btn,
    .filter-btn,
    .sort-btn {
        padding: 10px 12px;
        font-size: 12px;
        flex: 1;
        min-width: 0;
    }
    
    .view-btn {
        font-size: 11px;
    }
    
    .todo-item {
        padding: 10px;
        gap: 8px;
    }
    
    .todo-text-container {
        width: 100%;
        order: 2;
    }
    
    .todo-checkbox {
        order: 1;
    }
    
    .edit-btn,
    .delete-btn {
        flex: 1;
        min-width: 0;
        padding: 10px 8px;
        font-size: 12px;
    }
    
    .week-header h2 {
        font-size: 1em;
        padding: 0 10px;
        text-align: center;
    }
    
    .week-nav-btn,
    .calendar-nav-btn {
        padding: 8px 12px;
        font-size: 14px;
    }
    
    .week-days {
        grid-template-columns: 1fr;
        gap: 10px;
    }
    
    .week-day {
        min-height: auto;
        min-width: 100%;
    }
    
    .week-day-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding-bottom: 8px;
    }
    
    .week-day-name {
        font-size: 16px;
    }
    
    .week-day-date {
        font-size: 16px;
    }
    
    .calendar-header h2 {
        font-size: 1.2em;
        padding: 0 10px;
        text-align: center;
    }
    
    .calendar-day {
        min-height: 60px;
        padding: 5px;
    }
    
    .calendar-day-number {
        font-size: 12px;
    }
    
    .calendar-todo-dot {
        width: 6px;
        height: 6px;
    }
    
    .info p {
        font-size: 12px;
        line-height: 1.6;
        word-break: break-word;
    }
    
    .info {
        padding: 12px;
    }
    
    .clear-btn {
        padding: 12px;
        font-size: 13px;
    }
}

/* 작은 모바일 (480px 이하) */
@media (max-width: 480px) {
    .container {
        padding: 12px;
        border-radius: 12px;
    }
    
    header h1 {
        font-size: 1.3em;
    }
    
    .view-btn {
        font-size: 10px;
        padding: 8px 6px;
    }
    
    .filter-btn,
    .sort-btn {
        font-size: 11px;
        padding: 8px 10px;
    }
    
    .week-header h2,
    .calendar-header h2 {
        font-size: 0.9em;
    }
    
    .todo-text {
        font-size: 14px;
    }
    
    .todo-date {
        font-size: 11px;
    }
    
    .calendar-day {
        min-height: 50px;
        padding: 4px;
    }
    
    .calendar-day-number {
        font-size: 11px;
    }
}

/* 가로 모드 (landscape) 모바일 */
@media (max-width: 768px) and (orientation: landscape) {
    .week-days {
        grid-template-columns: repeat(7, 1fr);
    }
    
    .week-day {
        min-height: 120px;
    }
}

/* 큰 화면 (1200px 이상) */
@media (min-width: 1200px) {
    .container {
        max-width: 1400px;
    }
    
    .week-day {
        min-height: 250px;
    }
}
```


파일 끝에 미디어 쿼리 추가 중.


```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 10px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 10px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 8px;
    overflow-x: auto;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 8px;
    min-height: 180px;
    background: #f8f9fa;
    min-width: 0;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 4px;
    overflow-x: auto;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}

/* 반응형 미디어 쿼리 */

/* 태블릿 (768px ~ 1024px) */
@media (max-width: 1024px) {
    .container {
        padding: 20px;
    }
    
    header h1 {
        font-size: 1.8em;
    }
    
    .week-day {
        min-height: 150px;
    }
}

/* 모바일 (768px 이하) */
@media (max-width: 768px) {
    body {
        padding: 5px;
        align-items: flex-start;
    }
    
    .container {
        padding: 15px;
        border-radius: 15px;
        margin: 5px 0;
    }
    
    header {
        margin-bottom: 20px;
    }
    
    header h1 {
        font-size: 1.5em;
    }
    
    .input-section {
        flex-direction: column;
    }
    
    #todoInput {
        width: 100%;
    }
    
    .due-date-input {
        width: 100%;
        min-width: 100%;
    }
    
    #addBtn {
        width: 100%;
        padding: 15px;
    }
    
    .view-section,
    .filter-section {
        gap: 5px;
    }
    
    .view-btn,
    .filter-btn,
    .sort-btn {
        padding: 10px 12px;
        font-size: 12px;
        flex: 1;
        min-width: 0;
    }
    
    .view-btn {
        font-size: 11px;
    }
    
    .todo-item {
        padding: 10px;
        gap: 8px;
    }
    
    .todo-text-container {
        width: 100%;
        order: 2;
    }
    
    .todo-checkbox {
        order: 1;
    }
    
    .edit-btn,
    .delete-btn {
        flex: 1;
        min-width: 0;
        padding: 10px 8px;
        font-size: 12px;
    }
    
    .week-header h2 {
        font-size: 1em;
        padding: 0 10px;
        text-align: center;
    }
    
    .week-nav-btn,
    .calendar-nav-btn {
        padding: 8px 12px;
        font-size: 14px;
    }
    
    .week-days {
        grid-template-columns: 1fr;
        gap: 10px;
    }
    
    .week-day {
        min-height: auto;
        min-width: 100%;
    }
    
    .week-day-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding-bottom: 8px;
    }
    
    .week-day-name {
        font-size: 16px;
    }
    
    .week-day-date {
        font-size: 16px;
    }
    
    .calendar-header h2 {
        font-size: 1.2em;
        padding: 0 10px;
        text-align: center;
    }
    
    .calendar-day {
        min-height: 60px;
        padding: 5px;
    }
    
    .calendar-day-number {
        font-size: 12px;
    }
    
    .calendar-todo-dot {
        width: 6px;
        height: 6px;
    }
    
    .info p {
        font-size: 12px;
        line-height: 1.6;
        word-break: break-word;
    }
    
    .info {
        padding: 12px;
    }
    
    .clear-btn {
        padding: 12px;
        font-size: 13px;
    }
}

/* 작은 모바일 (480px 이하) */
@media (max-width: 480px) {
    .container {
        padding: 12px;
        border-radius: 12px;
    }
    
    header h1 {
        font-size: 1.3em;
    }
    
    .view-btn {
        font-size: 10px;
        padding: 8px 6px;
    }
    
    .filter-btn,
    .sort-btn {
        font-size: 11px;
        padding: 8px 10px;
    }
    
    .week-header h2,
    .calendar-header h2 {
        font-size: 0.9em;
    }
    
    .todo-text {
        font-size: 14px;
    }
    
    .todo-date {
        font-size: 11px;
    }
    
    .calendar-day {
        min-height: 50px;
        padding: 4px;
    }
    
    .calendar-day-number {
        font-size: 11px;
    }
}

/* 가로 모드 (landscape) 모바일 */
@media (max-width: 768px) and (orientation: landscape) {
    .week-days {
        grid-template-columns: repeat(7, 1fr);
    }
    
    .week-day {
        min-height: 120px;
    }
}

/* 큰 화면 (1200px 이상) */
@media (min-width: 1200px) {
    .container {
        max-width: 1400px;
    }
    
    .week-day {
        min-height: 250px;
    }
}


```


모바일에서 정보 표시 개선 중.


```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Todo 앱</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <div class="container">
        <header>
            <h1>📝 할 일 목록</h1>
        </header>
        
        <div class="input-section">
            <input 
                type="text" 
                id="todoInput" 
                placeholder="할 일을 입력하세요..." 
                autocomplete="off"
            >
            <input 
                type="date" 
                id="dueDateInput" 
                class="due-date-input"
                title="완료 기한 (선택사항)"
            >
            <button id="addBtn">추가</button>
        </div>
        
        <div class="search-section">
            <input 
                type="text" 
                id="searchInput" 
                placeholder="🔍 검색..." 
                autocomplete="off"
            >
        </div>
        
        <div class="view-section">
            <button class="view-btn active" data-view="list" title="리스트 보기">📋 리스트</button>
            <button class="view-btn" data-view="week" title="주간 보기">📅 주간</button>
            <button class="view-btn" data-view="calendar" title="캘린더 보기">🗓️ 캘린더</button>
        </div>
        
        <div class="filter-section">
            <button class="filter-btn active" data-filter="all">전체</button>
            <button class="filter-btn" data-filter="active">미완료</button>
            <button class="filter-btn" data-filter="completed">완료</button>
            <button class="sort-btn" id="sortBtn" title="마감순 정렬">
                <span id="sortIcon">🔀</span> 정렬
            </button>
        </div>
        
        <!-- 리스트 뷰 -->
        <ul id="todoList" class="todo-list list-view"></ul>
        
        <!-- 주간 뷰 -->
        <div id="weekView" class="week-view hidden">
            <div class="week-header">
                <button class="week-nav-btn" id="prevWeekBtn">◀</button>
                <h2 id="weekTitle"></h2>
                <button class="week-nav-btn" id="nextWeekBtn">▶</button>
            </div>
            <div id="weekDays" class="week-days"></div>
        </div>
        
        <!-- 캘린더 뷰 -->
        <div id="calendarView" class="calendar-view hidden">
            <div class="calendar-header">
                <button class="calendar-nav-btn" id="prevMonthBtn">◀</button>
                <h2 id="calendarTitle"></h2>
                <button class="calendar-nav-btn" id="nextMonthBtn">▶</button>
            </div>
            <div id="calendarGrid" class="calendar-grid"></div>
        </div>
        
        <div class="info">
            <p class="info-stats">
                <span class="stat-item">남은 할 일: <span id="remainingCount" class="remaining-count">0</span></span>
                <span class="stat-separator">|</span>
                <span class="stat-item">완료: <span id="completedCount">0</span></span>
                <span class="stat-separator">|</span>
                <span class="stat-item">전체: <span id="totalCount">0</span></span>
            </p>
            <button id="clearCompletedBtn" class="clear-btn">완료된 항목 모두 삭제</button>
        </div>
    </div>
    
    <script src="app.js"></script>
</body>
</html>


```

```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 10px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 10px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 8px;
    overflow-x: auto;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 8px;
    min-height: 180px;
    background: #f8f9fa;
    min-width: 0;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 4px;
    overflow-x: auto;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}

/* 반응형 미디어 쿼리 */

/* 태블릿 (768px ~ 1024px) */
@media (max-width: 1024px) {
    .container {
        padding: 20px;
    }
    
    header h1 {
        font-size: 1.8em;
    }
    
    .week-day {
        min-height: 150px;
    }
}

/* 모바일 (768px 이하) */
@media (max-width: 768px) {
    body {
        padding: 5px;
        align-items: flex-start;
    }
    
    .container {
        padding: 15px;
        border-radius: 15px;
        margin: 5px 0;
    }
    
    header {
        margin-bottom: 20px;
    }
    
    header h1 {
        font-size: 1.5em;
    }
    
    .input-section {
        flex-direction: column;
    }
    
    #todoInput {
        width: 100%;
    }
    
    .due-date-input {
        width: 100%;
        min-width: 100%;
    }
    
    #addBtn {
        width: 100%;
        padding: 15px;
    }
    
    .view-section,
    .filter-section {
        gap: 5px;
    }
    
    .view-btn,
    .filter-btn,
    .sort-btn {
        padding: 10px 12px;
        font-size: 12px;
        flex: 1;
        min-width: 0;
    }
    
    .view-btn {
        font-size: 11px;
    }
    
    .todo-item {
        padding: 10px;
        gap: 8px;
    }
    
    .todo-text-container {
        width: 100%;
        order: 2;
    }
    
    .todo-checkbox {
        order: 1;
    }
    
    .edit-btn,
    .delete-btn {
        flex: 1;
        min-width: 0;
        padding: 10px 8px;
        font-size: 12px;
    }
    
    .week-header h2 {
        font-size: 1em;
        padding: 0 10px;
        text-align: center;
    }
    
    .week-nav-btn,
    .calendar-nav-btn {
        padding: 8px 12px;
        font-size: 14px;
    }
    
    .week-days {
        grid-template-columns: 1fr;
        gap: 10px;
    }
    
    .week-day {
        min-height: auto;
        min-width: 100%;
    }
    
    .week-day-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding-bottom: 8px;
    }
    
    .week-day-name {
        font-size: 16px;
    }
    
    .week-day-date {
        font-size: 16px;
    }
    
    .calendar-header h2 {
        font-size: 1.2em;
        padding: 0 10px;
        text-align: center;
    }
    
    .calendar-day {
        min-height: 60px;
        padding: 5px;
    }
    
    .calendar-day-number {
        font-size: 12px;
    }
    
    .calendar-todo-dot {
        width: 6px;
        height: 6px;
    }
    
    .info-stats {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        align-items: center;
        gap: 8px;
        font-size: 12px;
        line-height: 1.6;
    }
    
    .stat-item {
        white-space: nowrap;
    }
    
    .stat-separator {
        color: #6c757d;
    }
    
    .info {
        padding: 12px;
    }
    
    .clear-btn {
        padding: 12px;
        font-size: 13px;
    }
}

/* 작은 모바일 (480px 이하) */
@media (max-width: 480px) {
    .container {
        padding: 12px;
        border-radius: 12px;
    }
    
    header h1 {
        font-size: 1.3em;
    }
    
    .view-btn {
        font-size: 10px;
        padding: 8px 6px;
    }
    
    .filter-btn,
    .sort-btn {
        font-size: 11px;
        padding: 8px 10px;
    }
    
    .week-header h2,
    .calendar-header h2 {
        font-size: 0.9em;
    }
    
    .todo-text {
        font-size: 14px;
    }
    
    .todo-date {
        font-size: 11px;
    }
    
    .calendar-day {
        min-height: 50px;
        padding: 4px;
    }
    
    .calendar-day-number {
        font-size: 11px;
    }
}

/* 가로 모드 (landscape) 모바일 */
@media (max-width: 768px) and (orientation: landscape) {
    .week-days {
        grid-template-columns: repeat(7, 1fr);
    }
    
    .week-day {
        min-height: 120px;
    }
}

/* 큰 화면 (1200px 이상) */
@media (min-width: 1200px) {
    .container {
        max-width: 1400px;
    }
    
    .week-day {
        min-height: 250px;
    }
}


```


기본 스타일에 info-stats 추가 중.


```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    padding: 10px;
}

.container {
    background: white;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
    width: 100%;
    max-width: 1200px;
    padding: 20px;
    margin: 10px 0;
}

.hidden {
    display: none !important;
}

header {
    text-align: center;
    margin-bottom: 30px;
}

header h1 {
    color: #333;
    font-size: 2em;
    font-weight: 700;
}

.input-section {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    margin-bottom: 20px;
    align-items: stretch;
}

.due-date-input {
    padding: 15px 12px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
    cursor: pointer;
    min-width: 150px;
    flex: 1;
    min-width: 120px;
}

.due-date-input:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.search-section {
    margin-bottom: 20px;
}

#searchInput {
    width: 100%;
    padding: 12px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 14px;
    transition: all 0.3s ease;
    outline: none;
}

#searchInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.filter-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.filter-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.filter-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.filter-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

.sort-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    display: flex;
    align-items: center;
    gap: 5px;
}

.sort-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.sort-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

#todoInput {
    flex: 1;
    padding: 15px 20px;
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    font-size: 16px;
    transition: all 0.3s ease;
    outline: none;
}

#todoInput:focus {
    border-color: #667eea;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

#addBtn {
    padding: 15px 30px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

#addBtn:hover {
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

#addBtn:active {
    transform: translateY(0);
}

.todo-list {
    list-style: none;
    margin-bottom: 20px;
}

.todo-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 12px;
    margin-bottom: 10px;
    background: #f8f9fa;
    border-radius: 10px;
    transition: all 0.3s ease;
    animation: slideIn 0.3s ease;
    flex-wrap: wrap;
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateX(-20px);
    }
    to {
        opacity: 1;
        transform: translateX(0);
    }
}

.todo-item:hover {
    background: #e9ecef;
    transform: translateX(5px);
}

.todo-item.completed {
    opacity: 0.7;
}

.todo-item.completed .todo-text {
    text-decoration: line-through;
    color: #6c757d;
}

.todo-item.overdue {
    border-left: 4px solid #dc3545;
    background: #fff5f5;
}

.todo-item.overdue:hover {
    background: #ffe0e0;
}

.todo-item.urgent {
    border-left: 4px solid #ffc107;
    background: #fffbf0;
}

.todo-item.urgent:hover {
    background: #fff8e1;
}

.todo-checkbox {
    width: 20px;
    height: 20px;
    cursor: pointer;
    accent-color: #667eea;
}

.todo-text-container {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.todo-text {
    font-size: 16px;
    color: #333;
    word-break: break-word;
    cursor: pointer;
    padding: 5px;
    border-radius: 5px;
    transition: background 0.2s ease;
}

.todo-text:hover {
    background: rgba(102, 126, 234, 0.1);
}

.date-info-container {
    display: flex;
    flex-direction: column;
    gap: 3px;
    margin-top: 5px;
}

.todo-date {
    font-size: 12px;
    color: #6c757d;
    padding-left: 5px;
    font-style: italic;
}

.todo-date.completed-date {
    color: #28a745;
}

.todo-date.due-date {
    color: #667eea;
    font-weight: 500;
}

.todo-date.overdue-date {
    color: #dc3545;
    font-weight: 600;
}

.todo-date.urgent-date {
    color: #ffc107;
    font-weight: 600;
}

.todo-text.editing {
    background: white;
    border: 2px solid #667eea;
    padding: 8px;
    outline: none;
    cursor: text;
}

.todo-edit-input {
    flex: 1;
    padding: 8px;
    border: 2px solid #667eea;
    border-radius: 5px;
    font-size: 16px;
    outline: none;
}

.delete-btn {
    padding: 10px 15px;
    background: #dc3545;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.delete-btn:hover {
    background: #c82333;
    transform: scale(1.05);
}

.delete-btn:active {
    transform: scale(0.95);
}

.info {
    text-align: center;
    padding: 15px;
    background: #f8f9fa;
    border-radius: 10px;
    color: #6c757d;
    font-size: 14px;
}

.info-stats {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    gap: 8px;
    margin-bottom: 10px;
}

.stat-item {
    white-space: nowrap;
}

.stat-separator {
    color: #6c757d;
}

.info span {
    font-weight: 600;
    color: #667eea;
}

.remaining-count {
    color: #ffc107;
    font-weight: 700;
}

.clear-btn {
    margin-top: 10px;
    padding: 10px 20px;
    background: #ffc107;
    color: #333;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    width: 100%;
}

.clear-btn:hover {
    background: #ffb300;
    transform: translateY(-2px);
    box-shadow: 0 5px 15px rgba(255, 193, 7, 0.4);
}

.clear-btn:active {
    transform: translateY(0);
}

.edit-btn {
    padding: 10px 15px;
    background: #28a745;
    color: white;
    border: none;
    border-radius: 8px;
    font-size: 14px;
    cursor: pointer;
    transition: all 0.3s ease;
    min-height: 44px;
    white-space: nowrap;
}

.edit-btn:hover {
    background: #218838;
    transform: scale(1.05);
}

.edit-btn:active {
    transform: scale(0.95);
}

/* 빈 목록 메시지 */
.empty-message {
    text-align: center;
    padding: 40px;
    color: #6c757d;
    font-size: 18px;
}

/* 뷰 모드 버튼 */
.view-section {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
    margin-bottom: 20px;
    justify-content: center;
}

.view-btn {
    padding: 10px 20px;
    background: #f8f9fa;
    color: #6c757d;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    font-size: 14px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
}

.view-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.view-btn.active {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-color: #667eea;
}

/* 주간 뷰 */
.week-view {
    margin-bottom: 20px;
}

.week-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.week-header h2 {
    font-size: 1.2em;
    color: #333;
}

.week-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.week-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.week-days {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 8px;
    overflow-x: auto;
}

.week-day {
    border: 2px solid #e0e0e0;
    border-radius: 10px;
    padding: 8px;
    min-height: 180px;
    background: #f8f9fa;
    min-width: 0;
}

.week-day-header {
    text-align: center;
    margin-bottom: 10px;
    padding-bottom: 10px;
    border-bottom: 1px solid #e0e0e0;
}

.week-day-name {
    font-weight: 600;
    color: #667eea;
    font-size: 14px;
}

.week-day-date {
    font-size: 18px;
    font-weight: 700;
    color: #333;
    margin-top: 5px;
}

.week-day-todos {
    display: flex;
    flex-direction: column;
    gap: 5px;
}

.week-empty {
    text-align: center;
    color: #6c757d;
    font-size: 12px;
    padding: 10px;
}

.week-todo-item {
    display: flex;
    align-items: center;
    gap: 8px;
    padding: 8px;
    background: white;
    border-radius: 5px;
    font-size: 13px;
    border-left: 3px solid #667eea;
}

.week-todo-item.overdue {
    border-left-color: #dc3545;
    background: #fff5f5;
}

.week-todo-item.urgent {
    border-left-color: #ffc107;
    background: #fffbf0;
}

.week-todo-text {
    flex: 1;
    word-break: break-word;
}

/* 캘린더 뷰 */
.calendar-view {
    margin-bottom: 20px;
}

.calendar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 20px;
}

.calendar-header h2 {
    font-size: 1.5em;
    color: #333;
}

.calendar-nav-btn {
    padding: 8px 15px;
    background: #f8f9fa;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s ease;
}

.calendar-nav-btn:hover {
    background: #e9ecef;
    border-color: #667eea;
}

.calendar-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 4px;
    overflow-x: auto;
}

.calendar-day-header {
    text-align: center;
    padding: 10px;
    font-weight: 600;
    color: #667eea;
    background: #f8f9fa;
    border-radius: 5px;
}

.calendar-day {
    aspect-ratio: 1;
    border: 2px solid #e0e0e0;
    border-radius: 8px;
    padding: 8px;
    background: white;
    display: flex;
    flex-direction: column;
    cursor: pointer;
    transition: all 0.2s ease;
}

.calendar-day:hover {
    background: #f8f9fa;
    border-color: #667eea;
}

.calendar-day.other-month {
    opacity: 0.4;
    background: #f5f5f5;
}

.calendar-day.today {
    background: #e3f2fd;
    border-color: #667eea;
    font-weight: 700;
}

.calendar-day-number {
    font-size: 14px;
    font-weight: 600;
    color: #333;
    margin-bottom: 5px;
}

.calendar-todos {
    display: flex;
    flex-wrap: wrap;
    gap: 3px;
    margin-top: auto;
}

.calendar-todo-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: #667eea;
}

.calendar-todo-dot.urgent {
    background: #ffc107;
}

.calendar-todo-dot.overdue {
    background: #dc3545;
}

.calendar-more {
    font-size: 10px;
    color: #6c757d;
    font-weight: 600;
}

/* 반응형 미디어 쿼리 */

/* 태블릿 (768px ~ 1024px) */
@media (max-width: 1024px) {
    .container {
        padding: 20px;
    }
    
    header h1 {
        font-size: 1.8em;
    }
    
    .week-day {
        min-height: 150px;
    }
}

/* 모바일 (768px 이하) */
@media (max-width: 768px) {
    body {
        padding: 5px;
        align-items: flex-start;
    }
    
    .container {
        padding: 15px;
        border-radius: 15px;
        margin: 5px 0;
    }
    
    header {
        margin-bottom: 20px;
    }
    
    header h1 {
        font-size: 1.5em;
    }
    
    .input-section {
        flex-direction: column;
    }
    
    #todoInput {
        width: 100%;
    }
    
    .due-date-input {
        width: 100%;
        min-width: 100%;
    }
    
    #addBtn {
        width: 100%;
        padding: 15px;
    }
    
    .view-section,
    .filter-section {
        gap: 5px;
    }
    
    .view-btn,
    .filter-btn,
    .sort-btn {
        padding: 10px 12px;
        font-size: 12px;
        flex: 1;
        min-width: 0;
    }
    
    .view-btn {
        font-size: 11px;
    }
    
    .todo-item {
        padding: 10px;
        gap: 8px;
    }
    
    .todo-text-container {
        width: 100%;
        order: 2;
    }
    
    .todo-checkbox {
        order: 1;
    }
    
    .edit-btn,
    .delete-btn {
        flex: 1;
        min-width: 0;
        padding: 10px 8px;
        font-size: 12px;
    }
    
    .week-header h2 {
        font-size: 1em;
        padding: 0 10px;
        text-align: center;
    }
    
    .week-nav-btn,
    .calendar-nav-btn {
        padding: 8px 12px;
        font-size: 14px;
    }
    
    .week-days {
        grid-template-columns: 1fr;
        gap: 10px;
    }
    
    .week-day {
        min-height: auto;
        min-width: 100%;
    }
    
    .week-day-header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding-bottom: 8px;
    }
    
    .week-day-name {
        font-size: 16px;
    }
    
    .week-day-date {
        font-size: 16px;
    }
    
    .calendar-header h2 {
        font-size: 1.2em;
        padding: 0 10px;
        text-align: center;
    }
    
    .calendar-day {
        min-height: 60px;
        padding: 5px;
    }
    
    .calendar-day-number {
        font-size: 12px;
    }
    
    .calendar-todo-dot {
        width: 6px;
        height: 6px;
    }
    
    .info-stats {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        align-items: center;
        gap: 8px;
        font-size: 12px;
        line-height: 1.6;
    }
    
    .stat-item {
        white-space: nowrap;
    }
    
    .stat-separator {
        color: #6c757d;
    }
    
    .info {
        padding: 12px;
    }
    
    .clear-btn {
        padding: 12px;
        font-size: 13px;
    }
}

/* 작은 모바일 (480px 이하) */
@media (max-width: 480px) {
    .container {
        padding: 12px;
        border-radius: 12px;
    }
    
    header h1 {
        font-size: 1.3em;
    }
    
    .view-btn {
        font-size: 10px;
        padding: 8px 6px;
    }
    
    .filter-btn,
    .sort-btn {
        font-size: 11px;
        padding: 8px 10px;
    }
    
    .week-header h2,
    .calendar-header h2 {
        font-size: 0.9em;
    }
    
    .todo-text {
        font-size: 14px;
    }
    
    .todo-date {
        font-size: 11px;
    }
    
    .calendar-day {
        min-height: 50px;
        padding: 4px;
    }
    
    .calendar-day-number {
        font-size: 11px;
    }
}

/* 가로 모드 (landscape) 모바일 */
@media (max-width: 768px) and (orientation: landscape) {
    .week-days {
        grid-template-columns: repeat(7, 1fr);
    }
    
    .week-day {
        min-height: 120px;
    }
}

/* 큰 화면 (1200px 이상) */
@media (min-width: 1200px) {
    .container {
        max-width: 1400px;
    }
    
    .week-day {
        min-height: 250px;
    }
}


```


반응형 웹 디자인을 적용했습니다.

## 반응형 개선 사항

### 모바일 최적화 (768px 이하)
- 입력 필드: 세로 배치, 전체 너비
- 버튼: 터치 친화적 크기(최소 44px 높이)
- 할 일 항목: 버튼과 텍스트를 세로로 재배치
- 주간 뷰: 7열 → 1열(세로 스크롤)
- 캘린더: 셀 크기 축소, 폰트 크기 조정
- 정보 표시: 줄바꿈 지원

### 태블릿 최적화 (768px ~ 1024px)
- 적절한 패딩과 간격
- 주간 뷰 높이 조정

### 작은 모바일 (480px 이하)
- 폰트 크기 추가 축소
- 버튼 크기 최적화
- 캘린더 셀 크기 축소

### 가로 모드 지원
- 가로 모드에서 주간 뷰는 7열 유지

### 큰 화면 (1200px 이상)
- 최대 너비 확장(1400px)
- 주간 뷰 높이 증가

## 주요 특징

- 터치 친화적: 모든 버튼 최소 44px 높이
- 유연한 레이아웃: flex-wrap으로 자동 줄바꿈
- 가독성: 화면 크기에 맞춘 폰트 크기
- 스크롤 지원: 주간/캘린더 뷰에서 가로 스크롤 가능

모바일, 태블릿, 데스크톱에서 사용할 수 있습니다.

