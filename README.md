<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>每日打卡小程式</title>
    <style>
        body {
            font-family: 'Microsoft JhengHei', Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        h1 {
            color: #333;
            text-align: center;
        }
        .card {
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            padding: 20px;
            margin-bottom: 20px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input, textarea, select {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
            box-sizing: border-box;
        }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #45a049;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        th, td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: left;
        }
        th {
            background-color: #f2f2f2;
        }
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
        .stats {
            display: flex;
            justify-content: space-around;
            margin-bottom: 20px;
        }
        .stat-box {
            text-align: center;
            flex: 1;
            margin: 0 10px;
            padding: 15px;
            background-color: white;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .stat-value {
            font-size: 24px;
            font-weight: bold;
            color: #4CAF50;
        }
        .tabs {
            display: flex;
            margin-bottom: 20px;
        }
        .tab {
            padding: 10px 20px;
            border: 1px solid #ddd;
            background-color: #f1f1f1;
            cursor: pointer;
        }
        .tab.active {
            background-color: white;
            border-bottom: none;
        }
        .tab-content {
            display: none;
        }
        .tab-content.active {
            display: block;
        }
        #result-message {
            padding: 10px;
            margin-top: 10px;
            border-radius: 4px;
            display: none;
        }
        .success {
            background-color: #dff0d8;
            color: #3c763d;
        }
        .error {
            background-color: #f2dede;
            color: #a94442;
        }
    </style>
</head>
<body>
    <h1>每日打卡小程式</h1>
    
    <div class="tabs">
        <div class="tab active" onclick="openTab('tab-checkin')">打卡</div>
        <div class="tab" onclick="openTab('tab-history')">歷史記錄</div>
        <div class="tab" onclick="openTab('tab-stats')">統計</div>
        <div class="tab" onclick="openTab('tab-settings')">設定</div>
    </div>

    <div id="tab-checkin" class="tab-content active">
        <div class="card">
            <div class="form-group">
                <label for="name">姓名：</label>
                <input type="text" id="name" placeholder="請輸入你的姓名">
            </div>
            <div class="form-group">
                <label for="activity">打卡項目：</label>
                <select id="activity">
                    <option value="運動">運動</option>
                    <option value="閱讀">閱讀</option>
                    <option value="寫作">寫作</option>
                    <option value="學習">學習</option>
                    <option value="其他">其他</option>
                </select>
            </div>
            <div class="form-group">
                <label for="description">備註：</label>
                <textarea id="description" rows="3" placeholder="請輸入打卡備註（選填）"></textarea>
            </div>
            <button onclick="checkIn()">打卡</button>
            <div id="result-message"></div>
        </div>
    </div>

    <div id="tab-history" class="tab-content">
        <div class="card">
            <h2>打卡歷史記錄</h2>
            <div class="form-group">
                <label for="filter-name">按姓名篩選：</label>
                <input type="text" id="filter-name" placeholder="輸入姓名篩選" oninput="filterRecords()">
            </div>
            <div class="form-group">
                <label for="filter-activity">按項目篩選：</label>
                <select id="filter-activity" onchange="filterRecords()">
                    <option value="">全部</option>
                    <option value="運動">運動</option>
                    <option value="閱讀">閱讀</option>
                    <option value="寫作">寫作</option>
                    <option value="學習">學習</option>
                    <option value="其他">其他</option>
                </select>
            </div>
            <table id="history-table">
                <thead>
                    <tr>
                        <th>姓名</th>
                        <th>打卡項目</th>
                        <th>備註</th>
                        <th>時間</th>
                    </tr>
                </thead>
                <tbody id="history-body">
                    <!-- 歷史記錄將在這裡動態生成 -->
                </tbody>
            </table>
        </div>
    </div>

    <div id="tab-stats" class="tab-content">
        <div class="card">
            <h2>打卡統計</h2>
            <div class="stats">
                <div class="stat-box">
                    <div class="stat-label">總打卡次數</div>
                    <div class="stat-value" id="total-checkins">0</div>
                </div>
                <div class="stat-box">
                    <div class="stat-label">今日打卡人數</div>
                    <div class="stat-value" id="today-checkins">0</div>
                </div>
                <div class="stat-box">
                    <div class="stat-label">連續打卡天數</div>
                    <div class="stat-value" id="streak-days">0</div>
                </div>
            </div>
            <h3>按項目統計</h3>
            <table id="activity-stats">
                <thead>
                    <tr>
                        <th>項目</th>
                        <th>次數</th>
                        <th>占比</th>
                    </tr>
                </thead>
                <tbody id="activity-stats-body">
                    <!-- 項目統計將在這裡動態生成 -->
                </tbody>
            </table>
        </div>
    </div>

    <div id="tab-settings" class="tab-content">
        <div class="card">
            <h2>設定</h2>
            <div class="form-group">
                <label for="group-name">打卡群組名稱：</label>
                <input type="text" id="group-name" placeholder="請輸入打卡群組名稱" value="每日打卡群">
            </div>
            <div class="form-group">
                <label for="custom-activity">新增自定義打卡項目：</label>
                <input type="text" id="custom-activity" placeholder="請輸入新的打卡項目">
                <button onclick="addCustomActivity()" style="margin-top: 10px;">新增項目</button>
            </div>
            <div class="form-group">
                <label>資料管理：</label>
                <button onclick="exportData()" style="background-color: #3498db;">匯出資料</button>
                <button onclick="clearData()" style="background-color: #e74c3c; margin-left: 10px;">清除所有資料</button>
            </div>
        </div>
    </div>

    <script>
        // 初始化本地存儲
        if (!localStorage.getItem('checkInRecords')) {
            localStorage.setItem('checkInRecords', JSON.stringify([]));
        }
        if (!localStorage.getItem('customActivities')) {
            localStorage.setItem('customActivities', JSON.stringify([]));
        }
        if (!localStorage.getItem('groupName')) {
            localStorage.setItem('groupName', '每日打卡群');
        }

        // 載入頁面時更新群組名稱
        document.getElementById('group-name').value = localStorage.getItem('groupName');
        document.title = localStorage.getItem('groupName') + ' - 每日打卡小程式';
        document.querySelector('h1').textContent = localStorage.getItem('groupName');

        // 更新頁面時顯示歷史記錄和統計數據
        updateHistory();
        updateStats();
        loadCustomActivities();

        // 打卡功能
        function checkIn() {
            const name = document.getElementById('name').value.trim();
            const activity = document.getElementById('activity').value;
            const description = document.getElementById('description').value.trim();
            const resultMessage = document.getElementById('result-message');

            if (!name) {
                resultMessage.textContent = '請輸入姓名！';
                resultMessage.className = 'error';
                resultMessage.style.display = 'block';
                return;
            }

            const now = new Date();
            const checkInRecord = {
                name: name,
                activity: activity,
                description: description,
                timestamp: now.getTime(),
                dateString: now.toLocaleString()
            };

            const records = JSON.parse(localStorage.getItem('checkInRecords'));
            records.push(checkInRecord);
            localStorage.setItem('checkInRecords', JSON.stringify(records));

            resultMessage.textContent = '打卡成功！';
            resultMessage.className = 'success';
            resultMessage.style.display = 'block';

            // 更新歷史記錄和統計數據
            updateHistory();
            updateStats();

            // 保存用戶名稱，下次自動填入
            localStorage.setItem('lastName', name);

            // 清空表單備註
            document.getElementById('description').value = '';

            // 3秒後隱藏成功訊息
            setTimeout(() => {
                resultMessage.style.display = 'none';
            }, 3000);
        }

        // 更新歷史記錄
        function updateHistory() {
            const records = JSON.parse(localStorage.getItem('checkInRecords'));
            const tbody = document.getElementById('history-body');
            tbody.innerHTML = '';

            // 按時間倒序排列
            records.sort((a, b) => b.timestamp - a.timestamp);

            records.forEach(record => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${record.name}</td>
                    <td>${record.activity}</td>
                    <td>${record.description}</td>
                    <td>${record.dateString}</td>
                `;
                tbody.appendChild(row);
            });
        }

        // 篩選記錄
        function filterRecords() {
            const nameFilter = document.getElementById('filter-name').value.toLowerCase();
            const activityFilter = document.getElementById('filter-activity').value;
            const records = JSON.parse(localStorage.getItem('checkInRecords'));
            const tbody = document.getElementById('history-body');
            tbody.innerHTML = '';

            // 按時間倒序排列
            records.sort((a, b) => b.timestamp - a.timestamp);

            records.forEach(record => {
                if ((nameFilter === '' || record.name.toLowerCase().includes(nameFilter)) &&
                    (activityFilter === '' || record.activity === activityFilter)) {
                    const row = document.createElement('tr');
                    row.innerHTML = `
                        <td>${record.name}</td>
                        <td>${record.activity}</td>
                        <td>${record.description}</td>
                        <td>${record.dateString}</td>
                    `;
                    tbody.appendChild(row);
                }
            });
        }

        // 更新統計數據
        function updateStats() {
            const records = JSON.parse(localStorage.getItem('checkInRecords'));
            document.getElementById('total-checkins').textContent = records.length;

            // 計算今日打卡人數
            const today = new Date();
            today.setHours(0, 0, 0, 0);
            const todayCheckins = new Set();
            records.forEach(record => {
                const recordDate = new Date(record.timestamp);
                recordDate.setHours(0, 0, 0, 0);
                if (recordDate.getTime() === today.getTime()) {
                    todayCheckins.add(record.name);
                }
            });
            document.getElementById('today-checkins').textContent = todayCheckins.size;

            // 計算連續打卡天數（簡化版本 - 僅檢查是否有每天的記錄）
            let streak = 0;
            const dateMap = new Map();
            records.forEach(record => {
                const recordDate = new Date(record.timestamp);
                const dateString = `${recordDate.getFullYear()}-${recordDate.getMonth() + 1}-${recordDate.getDate()}`;
                dateMap.set(dateString, true);
            });

            // 檢查連續天數
            const checkDate = new Date();
            checkDate.setHours(0, 0, 0, 0);
            while (true) {
                const dateString = `${checkDate.getFullYear()}-${checkDate.getMonth() + 1}-${checkDate.getDate()}`;
                if (dateMap.has(dateString)) {
                    streak++;
                    checkDate.setDate(checkDate.getDate() - 1);
                } else {
                    break;
                }
            }
            document.getElementById('streak-days').textContent = streak;

            // 按項目統計
            const activityStats = {};
            records.forEach(record => {
                if (!activityStats[record.activity]) {
                    activityStats[record.activity] = 0;
                }
                activityStats[record.activity]++;
            });

            const activityStatsBody = document.getElementById('activity-stats-body');
            activityStatsBody.innerHTML = '';
            for (const activity in activityStats) {
                const row = document.createElement('tr');
                const percentage = ((activityStats[activity] / records.length) * 100).toFixed(1);
                row.innerHTML = `
                    <td>${activity}</td>
                    <td>${activityStats[activity]}</td>
                    <td>${percentage}%</td>
                `;
                activityStatsBody.appendChild(row);
            }
        }

        // 切換頁籤
        function openTab(tabId) {
            // 隱藏所有頁籤內容
            const tabContents = document.getElementsByClassName('tab-content');
            for (let i = 0; i < tabContents.length; i++) {
                tabContents[i].classList.remove('active');
            }

            // 取消所有頁籤的活躍狀態
            const tabs = document.getElementsByClassName('tab');
            for (let i = 0; i < tabs.length; i++) {
                tabs[i].classList.remove('active');
            }

            // 顯示選中的頁籤
            document.getElementById(tabId).classList.add('active');
            
            // 設置選中頁籤的活躍狀態
            event.currentTarget.classList.add('active');

            // 如果切換到統計頁籤，更新統計數據
            if (tabId === 'tab-stats') {
                updateStats();
            }
            // 如果切換到歷史記錄頁籤，更新歷史記錄
            if (tabId === 'tab-history') {
                updateHistory();
            }
        }

        // 新增自定義打卡項目
        function addCustomActivity() {
            const customActivity = document.getElementById('custom-activity').value.trim();
            if (!customActivity) return;

            const customActivities = JSON.parse(localStorage.getItem('customActivities'));
            if (!customActivities.includes(customActivity)) {
                customActivities.push(customActivity);
                localStorage.setItem('customActivities', JSON.stringify(customActivities));
                loadCustomActivities();
                document.getElementById('custom-activity').value = '';
            }
        }

        // 載入自定義打卡項目
        function loadCustomActivities() {
            const customActivities = JSON.parse(localStorage.getItem('customActivities'));
            const activitySelect = document.getElementById('activity');
            const filterActivitySelect = document.getElementById('filter-activity');

            // 保留預設選項
            const defaultOptions = ['運動', '閱讀', '寫作', '學習', '其他'];
            
            // 清除當前選項（除了預設選項）
            while (activitySelect.options.length > defaultOptions.length) {
                activitySelect.remove(defaultOptions.length);
            }
            
            // 清除篩選選項（除了全部和預設選項）
            while (filterActivitySelect.options.length > defaultOptions.length + 1) {
                filterActivitySelect.remove(defaultOptions.length + 1);
            }

            // 添加自定義選項
            customActivities.forEach(activity => {
                if (!defaultOptions.includes(activity)) {
                    const option = new Option(activity, activity);
                    activitySelect.add(option);
                    
                    const filterOption = new Option(activity, activity);
                    filterActivitySelect.add(filterOption);
                }
            });
        }

        // 匯出資料
        function exportData() {
            const records = JSON.parse(localStorage.getItem('checkInRecords'));
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(records, null, 2));
            const downloadAnchorNode = document.createElement('a');
            downloadAnchorNode.setAttribute("href", dataStr);
            downloadAnchorNode.setAttribute("download", "打卡記錄_" + new Date().toISOString().split('T')[0] + ".json");
            document.body.appendChild(downloadAnchorNode);
            downloadAnchorNode.click();
            downloadAnchorNode.remove();
        }

        // 清除所有資料
        function clearData() {
            if (confirm('確定要清除所有打卡記錄嗎？此操作不可恢復！')) {
                localStorage.setItem('checkInRecords', JSON.stringify([]));
                updateHistory();
                updateStats();
                alert('所有打卡記錄已清除！');
            }
        }

        // 當群組名稱改變時更新
        document.getElementById('group-name').addEventListener('change', function() {
            const groupName = this.value.trim() || '每日打卡群';
            localStorage.setItem('groupName', groupName);
            document.title = groupName + ' - 每日打卡小程式';
            document.querySelector('h1').textContent = groupName;
        });

        // 如果有上次使用的名稱，自動填入
        if (localStorage.getItem('lastName')) {
            document.getElementById('name').value = localStorage.getItem('lastName');
        }
    </script>
</body>
</html>