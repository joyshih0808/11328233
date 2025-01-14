# 11328233
## 目錄(各種程式的連結)
- [設計的功能](功能清單.md)
- [程式初稿](todo.md)
- [程式草稿1](程式草稿1.md)
:增加了登入和註冊功能
- [程式草稿2](程式草稿2.md)
:增加分類功能還有輕重緩急功能
- [主程式](主程式.md)
:將程式草稿1登入頁面加上草稿2的代辦事項

## 代辦事項想要有的功能

- 標籤
    - 緊急度
        - 輕
        - 中
        - 緩
    - 預計所花時間
        - 分鐘
        - 小時
    - 科目
- 作業
- 考試
- 預習
- 複習
***
# 專案主題:代辦事項
### 電機一乙 11328233 施佳妤
## 功能
- 頁面:
   1. 登入頁面:
    - 註冊功能:
   2. 任務清單頁面:
## 學習歷程
1. 安裝PYQT6
2. 構思想要的功能
3. 主程式編寫
4. 美化圖形介面
## 用到的元件
![alt text](https://github.com/joyshih0808/11328233/blob/main/%E5%9C%96%E7%89%87/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2%202025-01-14%20142036.png)
![alt text](https://github.com/joyshih0808/11328233/blob/main/%E5%9C%96%E7%89%87/%E8%9E%A2%E5%B9%95%E6%93%B7%E5%8F%96%E7%95%AB%E9%9D%A2%202025-01-14%20142054.png)
## 程式碼介紹
這個程式有三個class:LoginPage、MainApp、ToDoApp。LoginPage負責處理登入的頁面，包含登入和註冊的功能。MainApp包含切換頁面的部份。ToDoApp包含代辦是代辦事項的程式。
### 匯入模組和設定

```python
from PyQt6.QtWidgets import (
    QApplication, QWidget, QVBoxLayout, QHBoxLayout, QLabel, QLineEdit,
    QPushButton, QStackedWidget, QComboBox, QDateEdit, QTabWidget,
    QListWidget, QMessageBox
)
from PyQt6.QtCore import Qt, QDate
import json
import os
import sys
```
- PyQt6.QtWidgets：用來建立視窗、布局與介面元件。
- PyQt6.QtCore：提供核心功能，例如日期 (QDate) 和對齊方式 (Qt 對齊旗標)。
- json 和 os：處理帳號與任務的資料存取，儲存為JSON 檔案。
- sys：用於管理應用程式的主迴圈。

### 登入頁面程式碼

```python
class LoginPage(QWidget):
    def __init__(self, switch_to_todo_callback):
        super().__init__()
        self.switch_to_todo = switch_to_todo_callback
        self.setWindowTitle("登入頁面")
        self.resize(400, 300)
```
- switch_to_todo_callback 用於切換到代辦事項頁面:
1. LoginPage 初始化時，從外部傳遞一個函數 switch_to_todo_callback。
2. 這個函數的邏輯是切換到代辦事項頁面（ToDoApp）。
3. 當用戶在登入頁面中輸入正確的帳號和密碼後，LoginPage 調用 self.switch_to_todo(username)，完成頁面切換。

- 命名頁面為登入頁面
- 調整大小

```python
        self.accounts_file = "accounts.json"
        self.load_accounts()
```
- 存放json檔的檔名
- 檢查json檔是否存在

```python
        layout = QVBoxLayout()

        self.title = QLabel("歡迎使用代辦事項應用程式")
        self.title.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.title)
```
- 加入標題並置中

```python
        self.usernameInput = QLineEdit()
        self.usernameInput.setPlaceholderText("輸入帳號")
        layout.addWidget(self.usernameInput)

        self.passwordInput = QLineEdit()
        self.passwordInput.setPlaceholderText("輸入密碼")
        self.passwordInput.setEchoMode(QLineEdit.EchoMode.Password)
        layout.addWidget(self.passwordInput)
```
- 帳號與密碼輸入框
```python
        button_layout = QHBoxLayout()
        self.loginButton = QPushButton("登入")
        self.loginButton.clicked.connect(self.login)
        button_layout.addWidget(self.loginButton)

        self.registerButton = QPushButton("註冊")
        self.registerButton.clicked.connect(self.register)
        button_layout.addWidget(self.registerButton)

        layout.addLayout(button_layout)
        self.setLayout(layout)
```
- 加入登入和註冊按鈕

``` python
    def load_accounts(self):
        if os.path.exists(self.accounts_file):
            with open(self.accounts_file, "r") as f:
                self.accounts = json.load(f)
        else:
            self.accounts = {}

    def save_accounts(self):
        with open(self.accounts_file, "w") as f:
            json.dump(self.accounts, f)
```
- 處理帳號資料
```python
    def login(self):
        username = self.usernameInput.text().strip()
        password = self.passwordInput.text().strip()

        if username in self.accounts and self.accounts[username] == password:
            QMessageBox.information(self, "成功", f"歡迎回來，{username}！")
            self.switch_to_todo(username)
        else:
            QMessageBox.warning(self, "錯誤", "帳號或密碼錯誤")
```
- 登入函數:
1. 將前面登入頁面輸入的名字和密碼放入username和password做儲存
2. self.accounts是一個字典，儲存帳號和密碼的配對，形式為{username:password}。self.accounts[username]識字點中username所對應的password。
3.  條件:
如果有兩個條件:當輸入的username在字典中，且輸入的username在字典中所對應的password和user輸入的password一樣樣時，會跳出一格訊息視窗寫:成功，歡迎回來，並顯現出user的名字
如果條件為否，則跳出訊息:錯誤，帳號或密碼錯誤

```python
    def register(self):
        username = self.usernameInput.text().strip()
        password = self.passwordInput.text().strip()

        if not username or not password:
            QMessageBox.warning(self, "錯誤", "帳號或密碼不可為空")
        elif username in self.accounts:
            QMessageBox.warning(self, "錯誤", "帳號已存在")
        else:
            self.accounts[username] = password
            self.save_accounts()
            QMessageBox.information(self, "成功", "註冊成功，請重新登入！")
```
- 註冊函數:
1. 有三種情況:沒有輸入這帳號密碼、user已存在和其他情況(成功註冊)。

### 代辦事項頁面程式

```python
class ToDoApp(QWidget):
    def __init__(self, username, switch_to_login_callback):
        super().__init__()
        self.username = username
        self.switch_to_login = switch_to_login_callback
        self.setWindowTitle(f"{username} 的代辦事項")
        self.resize(600, 400)
```
- 命名代辦事項頁面和大小

```python
        self.tasks_file = f"{self.username}_tasks.json"
        self.load_tasks()

        layout = QVBoxLayout()

``` 
1. 使用 f-string 語法來建立檔案名稱
2. 呼叫 load_tasks 方法
- 如果檔案存在：
 使用 json.load 讀取檔案內容，並將資料存入 self.tasks。
- 如果檔案不存在：
將 self.tasks 初始化為空字典 {}，表示該使用者目前還沒有任何任務。
3. 主佈局為垂直佈局

```python
        self.welcomeLabel = QLabel(f"歡迎，{self.username}！")
        self.welcomeLabel.setAlignment(Qt.AlignmentFlag.AlignCenter)
        layout.addWidget(self.welcomeLabel)

        control_layout = QHBoxLayout()
```
1. 顯現歡迎字樣並置中
2. 將歡迎的標籤加入主佈局
3. 控制佈局為水平佈局

```python
        self.taskInput = QLineEdit()
        self.taskInput.setPlaceholderText("輸入任務")
        control_layout.addWidget(self.taskInput)

        self.categoryInput = QComboBox()
        self.categoryInput.setEditable(True)
        self.categoryInput.addItems(["Work", "Personal", "Other"])
        control_layout.addWidget(self.categoryInput)

        self.priorityInput = QComboBox()
        self.priorityInput.addItems(["High", "Medium", "Low"])
        control_layout.addWidget(self.priorityInput)

        self.deadlineInput = QDateEdit()
        self.deadlineInput.setCalendarPopup(True)
        self.deadlineInput.setDate(QDate.currentDate())
        control_layout.addWidget(self.deadlineInput)

        self.addButton = QPushButton("新增任務")
        self.addButton.clicked.connect(self.add_task)
        control_layout.addWidget(self.addButton)
```
1. 任務輸入框、任務類別選擇、優先等級選擇、截止日期選擇、新增任務按鈕利用.addwidget加入控制佈局
2. 元件的排列順序是：輸入框 > 類別選擇 > 優先等級 > 截止日期 > 按鈕。
- 注意:因為控制佈局為水平佈局

```python
        layout.addLayout(control_layout)
```
- 控制佈局加入主佈局(垂直佈局)

```python
        self.tabs = QTabWidget()
        self.taskLists = {}

        for category in ["Work", "Personal", "Other"]:
            self.add_category_tab(category)

        layout.addWidget(self.tabs)

        self.logoutButton = QPushButton("登出")
        self.logoutButton.clicked.connect(self.logout)
        layout.addWidget(self.logoutButton)

        self.setLayout(layout)
```
1. 建立標籤頁容器:
- QTabWidget 是一種用來組織多個標籤頁（Tab）的元件。
每個標籤頁可以顯示不同的內容（例如不同類別的任務）。
- self.tabs 是標籤頁物件，用來管理和顯示不同分類的任務。
2. self.taskLists 是一個字典，用來儲存每個分類標籤頁對應的任務列表。key:任務分類 value:每個分類標籤頁中的 QListWidget（用來顯示任務）。
3. 將 self.tabs（標籤頁容器）作為一個元件，加入主佈局中。
這樣介面就可以顯示「分類標籤」和「對應的任務列表」。
4. 加入登出按鈕。
5. setLayout() 方法用來設定整體介面的佈局。

```python
    def load_tasks(self):
        if os.path.exists(self.tasks_file):
            with open(self.tasks_file, "r") as f:
                self.tasks = json.load(f)
        else:
            self.tasks = {}

    def save_tasks(self):
        with open(self.tasks_file, "w") as f:
            json.dump(self.tasks, f)

    def add_category_tab(self, category):
        taskList = QListWidget()
        self.taskLists[category] = taskList
        self.tabs.addTab(taskList, category)

    def add_task(self):
        task = self.taskInput.text().strip()
        category = self.categoryInput.currentText().strip()
        priority = self.priorityInput.currentText()
        deadline = self.deadlineInput.date().toString("yyyy-MM-dd")

        if not task:
            QMessageBox.warning(self, "錯誤", "任務不可為空")
            return

        if category not in self.taskLists:
            self.add_category_tab(category)

        taskItem = f"{task} - 優先等級: {priority} - 截止日期: {deadline}"
        self.taskLists[category].addItem(taskItem)

        if category not in self.tasks:
            self.tasks[category] = []

        self.tasks[category].append(taskItem)
        self.save_tasks()
        self.taskInput.clear()

    def logout(self):
        self.save_tasks()
        QMessageBox.information(self, "登出", "您已成功登出")
        self.switch_to_login()
```
- 方法:
1. 載入上次的資料方法:
2. 儲存資料方法:
- 定義了一個名為 save_tasks 的方法，用於儲存任務資料。
self： 表示這是類別中的方法，能夠訪問類別內的屬性（如 self.tasks 和 self.tasks_file）。
- open(self.tasks_file, "w")：
開啟或建立檔案，檔名來自 self.tasks_file（例如 john_tasks.json）。
"w" 模式： 代表以 寫入模式（write mode） 開啟檔案。如果檔案已存在，會覆蓋原內容；如果檔案不存在，則會自動建立。
- as f：表示將打開的檔案物件取名為 f。
- json.dump()：
將 Python 的資料結構（如字典或清單）序列化為 JSON 格式，並寫入到檔案中。
- f：檔案物件，表示將內容寫入到 self.tasks_file 指定的檔案。
3. 加入頁籤的方法:每個任務分類（如 "Work", "Personal", "Other"）建立一個新的頁籤(Tab)，並將該頁籤添加到主界面中
4. 加入任務方法
5. 登出資料的方法

### 切換頁面程式

```python
class MainApp(QStackedWidget):
    def __init__(self):
        super().__init__()
        self.loginPage = LoginPage(self.show_todo_page)
        self.addWidget(self.loginPage)
        self.todoPage = None

    def show_todo_page(self, username):
        self.todoPage = ToDoApp(username, self.show_login_page)
        self.addWidget(self.todoPage)
        self.setCurrentWidget(self.todoPage)

    def show_login_page(self):
        self.setCurrentWidget(self.loginPage)
```
1. MainApp 類別繼承自 QStackedWidget，表示這個類別能夠顯示多個頁面，但每次只能顯示一個頁面。
super().__init__()：呼叫父類別（QStackedWidget）的初始化方法來初始化 MainApp 類別。
2. self.loginPage = LoginPage(self.show_todo_page)：
- 這行創建了 LoginPage 類別的實例，並將 self.show_todo_page 方法作為參數傳遞給它。
- LoginPage 類別中的 switch_to_todo 會被設置為 self.show_todo_page，因此當用戶成功登入時，show_todo_page 方法會被呼叫，並且會顯示代辦事項頁面。
3. self.addWidget(self.loginPage)：
- 將創建的登入頁面 (self.loginPage) 添加到 QStackedWidget 中，這樣它就會成為當前顯示的頁面。
4. 初始化 todoPage 屬性
5. self.todoPage = ToDoApp(username, self.show_login_page)：
創建一個 ToDoApp 類別的實例，並將用戶名 (username) 和返回登入頁面的函數 (self.show_login_page) 傳遞給它。
ToDoApp 類別用來顯示代辦事項頁面，並將其賦值給 self.todoPage。
6.def show_login_page(self):
        self.setCurrentWidget(self.loginPage)
- 顯示登出頁面

### 主程式啟動

```python
if __name__ == "__main__":
    app = QApplication(sys.argv)
    mainApp = MainApp()
    mainApp.show()
    sys.exit(app.exec())
```
-  mainApp = MainApp():
這一行創建了 MainApp 類別的實例。這是整個應用程式的主要界面，包含了登入頁面和代辦事項頁面之間的切換。
## 成果展現
## 主題:四子棋
### 進度
- 12/25    概念構思、安裝pyQT6
- 12/27    四子棋程式碼
***
### 程式碼
***
# 學到的知識
## 什麼是GUI

GUI 是 Graphical User Interface 的縮寫，意思是「圖形使用者介面」。GUI 是一種讓使用者通過圖形元件（例如按鈕、視窗、圖標、下拉選單等）與計算機或系統進行互動的介面形式。

GUI 的特點
直觀操作：使用者可以通過滑鼠點擊、拖曳、鍵盤操作等方式與系統互動，而不需要輸入命令。
易於學習：相較於文字介面（CLI, Command Line Interface），GUI 對於新手更友好，因為圖形元素通常更容易理解。
視覺化：系統的功能和狀態通過圖形元素表現，例如進度條顯示下載進度。

