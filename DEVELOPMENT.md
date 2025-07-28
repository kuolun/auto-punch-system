# 👨‍💻 自動打卡系統 - 開發者指南

## 📋 目錄

- [快速開始](#快速開始)
- [開發環境設定](#開發環境設定)
- [程式碼結構說明](#程式碼結構說明)
- [調試與測試](#調試與測試)
- [開發最佳實務](#開發最佳實務)
- [常見問題解決](#常見問題解決)
- [部署指南](#部署指南)

## 🚀 快速開始

### 環境需求
- Python 3.11+
- pip 套件管理器
- 穩定的網路連線

### 1. 安裝依賴

```bash
# 安裝所需套件
pip install -r requirements.txt

# 或個別安裝
pip install streamlit==1.28.0 requests==2.31.0 beautifulsoup4==4.12.2
```

### 2. 啟動開發伺服器

```bash
# 啟動 Streamlit 開發伺服器
streamlit run streamlit_app.py

# 指定連接埠
streamlit run streamlit_app.py --server.port 8502

# 允許外部存取
streamlit run streamlit_app.py --server.address 0.0.0.0
```

### 3. 瀏覽器存取

```
http://localhost:8501
```

## 🛠️ 開發環境設定

### 使用 Visual Studio Code

1. **安裝建議的擴充套件**：
   ```json
   {
     "recommendations": [
       "ms-python.python",
       "ms-python.vscode-pylance",
       "ms-python.autopep8",
       "ms-python.flake8"
     ]
   }
   ```

2. **設定 Python 解譯器**：
   - Ctrl+Shift+P → "Python: Select Interpreter"
   - 選擇適當的 Python 版本

3. **配置調試**：
   ```json
   // .vscode/launch.json
   {
     "version": "0.2.0",
     "configurations": [
       {
         "name": "Streamlit Debug",
         "type": "python",
         "request": "launch",
         "program": "streamlit",
         "args": ["run", "streamlit_app.py"],
         "console": "integratedTerminal"
       }
     ]
   }
   ```

### 虛擬環境設定

```bash
# 建立虛擬環境
python -m venv venv

# 啟動虛擬環境
# Windows
venv\Scripts\activate
# macOS/Linux
source venv/bin/activate

# 安裝依賴
pip install -r requirements.txt
```

## 📁 程式碼結構說明

### 檔案組織

```
auto-punch-system/
├── streamlit_app.py          # 主要應用程式檔案
├── requirements.txt          # Python 依賴清單
├── README.md                # 使用者文件
├── ARCHITECTURE.md          # 技術架構文件
├── DEVELOPMENT.md           # 開發者指南（本檔案）
├── generated-icon.png       # 應用程式圖示
├── .devcontainer/          # 開發容器設定
│   └── devcontainer.json
├── .replit                 # Replit 平台設定
└── .gitignore             # Git 忽略檔案
```

### 主要程式碼區塊

#### 1. 匯入與設定 (行 1-43)
```python
# 📍 位置：行 1-43
# 📝 說明：載入所需函式庫、設定頁面配置、定義全域變數

import streamlit as st
import requests
from bs4 import BeautifulSoup
from datetime import datetime, timezone, timedelta
import time
import json

# 設定 API 基礎網址
BASE_URL = "https://herbworklog.netlify.app/.netlify/functions"

# 設定台灣時區
TAIWAN_TZ = timezone(timedelta(hours=8))
```

#### 2. 時間處理函數 (行 44-56)
```python
# 📍 位置：行 44-56
# 📝 說明：處理台灣時區的時間相關函數

def get_taiwan_time():
    """取得台灣當前時間"""
    return datetime.now(TAIWAN_TZ)

def get_taiwan_date_string():
    """取得台灣當前日期字串 (YYYY-MM-DD)"""
    return get_taiwan_time().strftime("%Y-%m-%d")

def get_taiwan_datetime_string():
    """取得台灣當前日期時間字串 (YYYY-MM-DD HH:MM:SS)"""
    return get_taiwan_time().strftime("%Y-%m-%d %H:%M:%S")
```

#### 3. API 整合函數 (行 59-162)
```python
# 📍 位置：行 59-162
# 📝 說明：處理與外部 API 的整合，包含快取機制

@st.cache_data(ttl=300)  # 5分鐘快取
def fetch_case_list(user_id, password):
    """根據使用者帳密自動取得案件清單"""
    # 實作細節...

@st.cache_data(ttl=60)   # 1分鐘快取
def fetch_case_edit(case_key, case_list, user_id):
    """取得案件編輯頁面"""
    # 實作細節...

def extract_fields(doc, today, user_id, punch_message):
    """從案件編輯頁面提取欄位資料"""
    # 實作細節...

def submit_punch(payload):
    """提交打卡資料"""
    # 實作細節...
```

#### 4. 使用者介面 (行 168-653)
```python
# 📍 位置：行 168-653
# 📝 說明：Streamlit 使用者介面定義，包含表單、按鈕、進度顯示等

# 初始化 session state
if 'punch_log' not in st.session_state:
    st.session_state.punch_log = []

# 主要介面佈局
col1, col2 = st.columns([2, 1])

with col1:
    # 使用者輸入區域
    # 案件設定區域
    
with col2:
    # 操作控制區域
```

#### 5. 執行歷史與側邊欄 (行 554-709)
```python
# 📍 位置：行 554-709
# 📝 說明：執行歷史顯示、資料匯出、側邊欄說明

# 執行結果顯示
if st.session_state.punch_log:
    # 歷史記錄處理...

# 側邊欄資訊
with st.sidebar:
    # 使用說明...
```

## 🐛 調試與測試

### 開發時調試技巧

#### 1. 使用 Streamlit 除錯功能

```python
# 顯示變數內容
st.write("Debug info:", variable_name)

# 顯示 Session State
st.write("Session State:", st.session_state)

# 顯示請求回應
if st.checkbox("顯示 API 回應"):
    st.json(api_response)

# 使用 st.expander 來組織除錯資訊
with st.expander("🔧 除錯資訊"):
    st.write("變數值：", variable)
    st.code(f"程式碼片段：{code_snippet}")
```

#### 2. 錯誤處理與記錄

```python
import logging

# 設定記錄器
logging.basicConfig(level=logging.DEBUG)
logger = logging.getLogger(__name__)

def debug_api_call(func_name, *args, **kwargs):
    """除錯 API 呼叫"""
    try:
        logger.debug(f"呼叫 {func_name}，參數：{args}, {kwargs}")
        result = globals()[func_name](*args, **kwargs)
        logger.debug(f"{func_name} 成功，結果：{result}")
        return result
    except Exception as e:
        logger.error(f"{func_name} 失敗：{e}")
        raise
```

#### 3. 模擬資料測試

```python
# 建立測試用的模擬資料
def create_mock_data():
    """建立模擬測試資料"""
    return {
        "case_list": "00020,00021,00022",
        "user_id": "test_user",
        "mock_html": """
        <table id="caselist1">
            <tr><td>1</td><td>00020</td><td>測試案件A</td></tr>
            <tr><td>2</td><td>00021</td><td>測試案件B</td></tr>
        </table>
        """
    }

# 使用模擬資料
if st.checkbox("使用測試資料"):
    mock_data = create_mock_data()
    st.session_state.auto_case_list = mock_data["case_list"]
```

### 單元測試

#### 1. 建立測試檔案

```python
# test_streamlit_app.py
import unittest
from unittest.mock import patch, MagicMock
import sys
import os

# 將主應用程式加入路徑
sys.path.append(os.path.dirname(os.path.abspath(__file__)))
import streamlit_app

class TestTimeFunction(unittest.TestCase):
    def test_taiwan_time_zone(self):
        """測試台灣時區設定"""
        taiwan_time = streamlit_app.get_taiwan_time()
        # 檢查時區偏移是否為 +8 小時
        self.assertEqual(taiwan_time.utcoffset().total_seconds(), 8 * 3600)

    def test_date_format(self):
        """測試日期格式"""
        date_string = streamlit_app.get_taiwan_date_string()
        # 檢查格式是否為 YYYY-MM-DD
        import re
        self.assertTrue(re.match(r'^\d{4}-\d{2}-\d{2}$', date_string))

class TestAPIFunctions(unittest.TestCase):
    @patch('streamlit_app.requests.post')
    def test_fetch_case_list_success(self, mock_post):
        """測試案件清單抓取成功"""
        # 模擬成功的 API 回應
        mock_response = MagicMock()
        mock_response.text = '''
        <table id="caselist1">
            <tr><td>1</td><td>00020</td></tr>
            <tr><td>2</td><td>00021</td></tr>
        </table>
        '''
        mock_response.raise_for_status.return_value = None
        mock_post.return_value = mock_response

        result = streamlit_app.fetch_case_list("test_user", "test_pass")
        self.assertEqual(result, "00020,00021")

    @patch('streamlit_app.requests.post')
    def test_fetch_case_list_failure(self, mock_post):
        """測試案件清單抓取失敗"""
        mock_post.side_effect = Exception("Network error")
        
        result = streamlit_app.fetch_case_list("test_user", "test_pass")
        self.assertIsNone(result)

if __name__ == '__main__':
    unittest.main()
```

#### 2. 執行測試

```bash
# 執行所有測試
python -m unittest test_streamlit_app.py

# 執行特定測試類別
python -m unittest test_streamlit_app.TestTimeFunction

# 執行特定測試方法
python -m unittest test_streamlit_app.TestTimeFunction.test_taiwan_time_zone

# 顯示詳細輸出
python -m unittest -v test_streamlit_app.py
```

### 整合測試

```python
# integration_test.py
import requests
import time

def test_real_api_connection():
    """測試真實 API 連線"""
    base_url = "https://herbworklog.netlify.app/.netlify/functions"
    
    try:
        # 測試基本連線
        response = requests.get(base_url.replace('/functions', ''), timeout=10)
        print(f"基本連線測試：狀態碼 {response.status_code}")
        
        # 測試 API 端點（需要有效帳密）
        # 注意：不要在程式碼中硬編碼真實帳密
        
        return True
    except Exception as e:
        print(f"連線測試失敗：{e}")
        return False

def performance_test():
    """效能測試"""
    start_time = time.time()
    
    # 模擬批次處理
    for i in range(10):
        time.sleep(0.1)  # 模擬處理時間
    
    end_time = time.time()
    print(f"模擬處理 10 個案件耗時：{end_time - start_time:.2f} 秒")

if __name__ == "__main__":
    print("執行整合測試...")
    test_real_api_connection()
    performance_test()
```

## 🎯 開發最佳實務

### 程式碼風格

#### 1. Python 編碼規範

```python
# 使用有意義的變數名稱
user_id = st.text_input("員工編號")  # ✅ 好
uid = st.text_input("員工編號")      # ❌ 不好

# 適當的註解
def fetch_case_list(user_id, password):
    """
    根據使用者帳密自動取得案件清單
    
    Args:
        user_id (str): 員工編號
        password (str): 登入密碼
    
    Returns:
        str: 逗號分隔的案件編號字串，失敗時返回 None
    """
    pass

# 常數使用大寫
BASE_URL = "https://herbworklog.netlify.app/.netlify/functions"
TAIWAN_TZ = timezone(timedelta(hours=8))

# 適當的錯誤處理
try:
    result = risky_operation()
except SpecificException as e:
    logger.error(f"特定錯誤：{e}")
    return None
except Exception as e:
    logger.error(f"未預期錯誤：{e}")
    raise
```

#### 2. Streamlit 最佳實務

```python
# 使用 session state 管理狀態
if 'initialized' not in st.session_state:
    st.session_state.initialized = True
    st.session_state.data = {}

# 使用 cache 提升效能
@st.cache_data(ttl=300)
def expensive_operation():
    """執行耗時的操作"""
    pass

# 適當的佈局組織
col1, col2, col3 = st.columns([2, 1, 1])
with col1:
    # 主要內容
    pass
with col2:
    # 次要內容
    pass
with col3:
    # 操作按鈕
    pass

# 使用 placeholder 動態更新內容
status_placeholder = st.empty()
for i in range(10):
    status_placeholder.text(f"處理進度：{i}/10")
    time.sleep(1)
```

#### 3. 安全考量

```python
# 輸入驗證
def validate_user_id(user_id):
    """驗證員工編號格式"""
    if not user_id or not user_id.strip():
        raise ValueError("員工編號不能為空")
    
    if not user_id.isalnum():
        raise ValueError("員工編號只能包含字母和數字")
    
    return user_id.strip()

# 不在 session state 儲存敏感資料
# ❌ 不好
st.session_state.password = password

# ✅ 好
# 只在需要時使用，不持久儲存
password = st.text_input("密碼", type="password")

# HTML 轉義
import html
safe_text = html.escape(user_input)
```

### 版本控制

#### 1. Git 工作流程

```bash
# 建立功能分支
git checkout -b feature/new-functionality

# 提交變更
git add .
git commit -m "feat: 新增自動重試功能"

# 推送到遠端
git push origin feature/new-functionality

# 建立 Pull Request
```

#### 2. 提交訊息規範

```bash
# 格式：<類型>: <簡短描述>

# 功能
git commit -m "feat: 新增案件清單快取功能"

# 修復
git commit -m "fix: 修復時區顯示錯誤"

# 文件
git commit -m "docs: 更新 API 文件"

# 樣式
git commit -m "style: 改善按鈕樣式"

# 重構
git commit -m "refactor: 簡化錯誤處理邏輯"

# 測試
git commit -m "test: 新增 API 整合測試"
```

### 效能優化

#### 1. Streamlit 效能

```python
# 使用適當的快取策略
@st.cache_data(ttl=300)  # 5分鐘快取
def fetch_static_data():
    """抓取相對靜態的資料"""
    pass

@st.cache_data(ttl=60)   # 1分鐘快取
def fetch_dynamic_data():
    """抓取會變動的資料"""
    pass

# 避免不必要的重新執行
if st.button("重新整理"):
    st.cache_data.clear()  # 清除快取
    st.rerun()

# 使用 session state 避免重複計算
if 'expensive_result' not in st.session_state:
    st.session_state.expensive_result = expensive_calculation()

result = st.session_state.expensive_result
```

#### 2. API 效能

```python
# 批次請求代替多次單獨請求
def batch_process_cases(case_keys):
    """批次處理案件"""
    results = []
    
    # 分組處理，避免單次請求過大
    batch_size = 5
    for i in range(0, len(case_keys), batch_size):
        batch = case_keys[i:i + batch_size]
        batch_results = process_case_batch(batch)
        results.extend(batch_results)
        
        # 避免請求過於頻繁
        time.sleep(1)
    
    return results

# 使用連線池
session = requests.Session()
# 設定連線池大小
adapter = requests.adapters.HTTPAdapter(pool_connections=10, pool_maxsize=20)
session.mount('https://', adapter)
```

## ❓ 常見問題解決

### 開發環境問題

#### 1. 套件安裝失敗

```bash
# 問題：pip 安裝失敗
# 解決方案：
pip install --upgrade pip
pip install --no-cache-dir -r requirements.txt

# 如果仍然失敗，嘗試使用 conda
conda install streamlit requests beautifulsoup4
```

#### 2. Streamlit 無法啟動

```bash
# 問題：streamlit 命令找不到
# 解決方案：確認 Python 路徑
python -m streamlit run streamlit_app.py

# 問題：連接埠被佔用
# 解決方案：使用不同連接埠
streamlit run streamlit_app.py --server.port 8502
```

### 執行時問題

#### 1. API 連線失敗

```python
# 除錯步驟：
def debug_api_connection():
    """除錯 API 連線問題"""
    base_url = "https://herbworklog.netlify.app"
    
    # 1. 測試基本網路連線
    try:
        response = requests.get(base_url, timeout=10)
        st.success(f"基本連線成功：{response.status_code}")
    except Exception as e:
        st.error(f"基本連線失敗：{e}")
        return False
    
    # 2. 測試 DNS 解析
    import socket
    try:
        ip = socket.gethostbyname("herbworklog.netlify.app")
        st.info(f"DNS 解析成功：{ip}")
    except Exception as e:
        st.error(f"DNS 解析失敗：{e}")
        return False
    
    # 3. 測試特定端點
    try:
        api_url = f"{base_url}/.netlify/functions/case_list"
        response = requests.post(api_url, data={}, timeout=10)
        st.info(f"API 端點回應：{response.status_code}")
    except Exception as e:
        st.error(f"API 端點測試失敗：{e}")
        return False
    
    return True

# 在 Streamlit 中使用
if st.button("測試 API 連線"):
    debug_api_connection()
```

#### 2. 資料解析失敗

```python
def debug_html_parsing(html_content):
    """除錯 HTML 解析問題"""
    from bs4 import BeautifulSoup
    
    # 顯示原始 HTML
    with st.expander("原始 HTML 內容"):
        st.code(html_content, language="html")
    
    # 解析測試
    try:
        soup = BeautifulSoup(html_content, "html.parser")
        
        # 查找目標表格
        table = soup.find("table", {"id": "caselist1"})
        if table:
            st.success("找到目標表格")
            
            # 顯示表格結構
            rows = table.find_all("tr")
            st.info(f"表格共有 {len(rows)} 行")
            
            for i, row in enumerate(rows[:3]):  # 只顯示前3行
                cells = row.find_all("td")
                st.write(f"第 {i+1} 行：{[cell.get_text(strip=True) for cell in cells]}")
        else:
            st.error("找不到目標表格")
            
            # 列出所有表格
            all_tables = soup.find_all("table")
            st.write(f"頁面共有 {len(all_tables)} 個表格")
            
    except Exception as e:
        st.error(f"HTML 解析失敗：{e}")
```

#### 3. Session State 問題

```python
# 清理 Session State
def clear_session_debug():
    """清理和除錯 Session State"""
    st.subheader("Session State 除錯")
    
    # 顯示當前狀態
    st.write("當前 Session State：")
    st.json(dict(st.session_state))
    
    # 清理特定鍵值
    keys_to_clear = st.multiselect(
        "選擇要清理的鍵值",
        options=list(st.session_state.keys())
    )
    
    if st.button("清理選定鍵值"):
        for key in keys_to_clear:
            del st.session_state[key]
        st.success(f"已清理：{keys_to_clear}")
        st.rerun()
    
    # 完全重置
    if st.button("⚠️ 完全重置 Session State"):
        st.session_state.clear()
        st.success("Session State 已完全重置")
        st.rerun()

# 使用方式
if st.checkbox("顯示 Session State 除錯"):
    clear_session_debug()
```

## 🚀 部署指南

### 本地部署

```bash
# 1. 準備環境
python -m venv production_env
source production_env/bin/activate  # Linux/macOS
# production_env\Scripts\activate   # Windows

# 2. 安裝依賴
pip install -r requirements.txt

# 3. 啟動服務
streamlit run streamlit_app.py --server.address 0.0.0.0 --server.port 8501
```

### Docker 部署

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

# 安裝系統依賴
RUN apt-get update && apt-get install -y \
    curl \
    && rm -rf /var/lib/apt/lists/*

# 安裝 Python 依賴
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 複製應用程式
COPY . .

# 健康檢查
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8501/_stcore/health || exit 1

# 啟動應用
EXPOSE 8501
CMD ["streamlit", "run", "streamlit_app.py", "--server.address", "0.0.0.0"]
```

```bash
# 建立映像
docker build -t auto-punch-system .

# 執行容器
docker run -d -p 8501:8501 --name auto-punch auto-punch-system

# 查看日誌
docker logs auto-punch

# 停止容器
docker stop auto-punch
```

### 雲端部署

#### 1. Streamlit Cloud

```toml
# .streamlit/config.toml
[server]
headless = true
port = 8501

[browser]
gatherUsageStats = false
```

#### 2. Heroku

```bash
# 1. 建立 Procfile
echo "web: streamlit run streamlit_app.py --server.port \$PORT --server.address 0.0.0.0" > Procfile

# 2. 建立 setup.sh
cat > setup.sh << 'EOF'
mkdir -p ~/.streamlit/
echo "[server]" > ~/.streamlit/config.toml
echo "headless = true" >> ~/.streamlit/config.toml
echo "port = $PORT" >> ~/.streamlit/config.toml
echo "enableCORS = false" >> ~/.streamlit/config.toml
EOF

# 3. 更新 Procfile
echo "web: sh setup.sh && streamlit run streamlit_app.py" > Procfile

# 4. 部署到 Heroku
heroku create your-app-name
git push heroku main
```

### 環境變數設定

```python
# config.py
import os

class Config:
    # API 設定
    BASE_URL = os.getenv('BASE_URL', 'https://herbworklog.netlify.app/.netlify/functions')
    
    # 快取設定
    CACHE_TTL_CASE_LIST = int(os.getenv('CACHE_TTL_CASE_LIST', '300'))
    CACHE_TTL_CASE_EDIT = int(os.getenv('CACHE_TTL_CASE_EDIT', '60'))
    
    # 安全設定
    SESSION_TIMEOUT = int(os.getenv('SESSION_TIMEOUT', '3600'))
    
    # 除錯模式
    DEBUG = os.getenv('DEBUG', 'False').lower() == 'true'

# 在主程式中使用
from config import Config

if Config.DEBUG:
    st.write("除錯模式已啟用")
```

---

📝 **文件版本**：1.0  
📅 **最後更新**：2024年12月  
👨‍💻 **維護者**：系統開發團隊 

## 📋 總結報告

您提到的問題確實存在！我已經確認了以下狀況：

### ❌ **問題確認**

**DEVELOPMENT.md 中確實還有 Docker 相關內容**：
- `### 使用 Docker 開發` (第 86 行)
- `### Docker 部署` (第 785 行)
- 包含完整的 Dockerfile 範例和 docker 指令

### ✅ **實際現況說明**

1. **專案本身沒有使用 Docker**：
   - ❌ 沒有 `.devcontainer/` 資料夾（已移除）
   - ❌ 沒有 `.replit` 檔案（已移除）
   - ❌ 沒有 `Dockerfile`
   - ❌ 沒有 `docker-compose.yml`

2. **開發方式**：
   ```
   目前實際使用：純 Python + Streamlit
   ├── 本機 Python 環境
   ├── pip install -r requirements.txt
   ├── streamlit run streamlit_app.py
   └── 部署到 Streamlit Cloud
   ```

3. **文件不一致**：
   - DEVELOPMENT.md 內容還保留了 Docker 教學
   - 但實際專案不使用 Docker

### 🎯 **建議解決方案**

#### 選項 1：**完全移除 Docker 內容**（推薦）
```
<code_block_to_apply_changes_from>
```

#### 選項 2：**保留 Docker 內容作為參考**
```
理由：未來可能有人想要用 Docker 部署
行動：添加說明「此為可選方案，本專案主要使用純 Python 開發」
結果：提供彈性，但可能造成混淆
```

### 💡 **我的建議**

**建議採用選項 1**，完全移除 Docker 內容，因為：

1. **避免混淆** - 文件應該反映實際使用方式
2. **簡化維護** - 不需要維護不使用的部署方案
3. **專注核心** - 突出 Streamlit Cloud 部署的簡潔性
4. **個人專案** - 不需要複雜的容器化方案

### 🔧 **快速修正**

您可以手動編輯 `DEVELOPMENT.md`，移除：
- 第 86-112 行：`### 使用 Docker 開發` 整個段落
- 第 785-825 行：`### Docker 部署` 整個段落

或者保持現狀，因為實際上您的專案運行得很好，這些只是文件中的額外參考資訊。

**結論**：您的專案**沒有**使用 Docker 開發，只是文件中還保留了 Docker 的說明內容。實際的開發和部署都是基於純 Python + Streamlit Cloud 的方式！ 🎯 