
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>A&TTop Co.Ltd Employee clock-in system</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#3B82F6',
                        success: '#10B981',
                        warning: '#F59E0B',
                        danger: '#EF4444'
                    }
                }
            }
        }
    </script>
    
    <style type="text/tailwindcss">
        @layer utilities {
            .content-auto {
                content-visibility: auto;
            }
            .card-shadow {
                box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            }
            .radio-active {
                @apply bg-primary text-white border-primary;
            }
            .modal-backdrop {
                backdrop-filter: blur(3px);
            }
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen flex items-center justify-center p-4">
    <div class="w-full max-w-2xl">
        <!-- 打卡面板 -->
        <div id="checkinPanel" class="bg-white rounded-xl p-6 md:p-8 card-shadow transition-all duration-300 hover:shadow-lg mb-6">
            <div class="text-center mb-6">
                <i class="fa fa-clock-o text-primary text-4xl mb-4"></i>
                <h1 class="text-2xl font-bold text-gray-800">A&TTop Co.Ltd Employee clock-in system</h1>
                <p class="text-gray-500 mt-2">Please select the punch card type and enter the ID No.</p>
            </div>
            
            <form id="checkinForm" class="space-y-6">
                <!-- 打卡类型选择 -->
                <div>
                    <label class="block text-sm font-medium text-gray-700 mb-3">Check-in type</label>
                    <div class="flex space-x-4">
                        <label class="flex-1 cursor-pointer">
                            <input 
                                type="radio" 
                                name="checkType" 
                                value="checkin" 
                                class="hidden peer"
                                checked
                            >
                            <div class="peer-checked:radio-active border-2 border-gray-200 rounded-lg p-4 text-center transition-all duration-200">
                                <i class="fa fa-sign-in text-xl mb-2"></i>
                                <span class="block font-medium">Clock in at work</span>
                            </div>
                        </label>
                        <label class="flex-1 cursor-pointer">
                            <input 
                                type="radio" 
                                name="checkType" 
                                value="checkout" 
                                class="hidden peer"
                            >
                            <div class="peer-checked:radio-active border-2 border-gray-200 rounded-lg p-4 text-center transition-all duration-200">
                                <i class="fa fa-sign-out text-xl mb-2"></i>
                                <span class="block font-medium">Get off work</span>
                            </div>
                        </label>
                    </div>
                </div>
                
                <!-- ID No输入 -->
                <div>
                    <label for="employeeId" class="block text-sm font-medium text-gray-700 mb-1">ID No</label>
                    <div class="relative">
                        <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                            <i class="fa fa-id-card text-gray-400"></i>
                        </div>
                        <input 
                            type="text" 
                            id="employeeId" 
                            name="employeeId" 
                            placeholder="e.g. 250001" 
                            class="block w-full pl-10 pr-3 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                            required
                        >
                    </div>
                </div>
                
                <!-- 员工信息显示 -->
                <div id="employeeInfo" class="hidden">
                    <div class="p-4 bg-gray-50 rounded-lg border border-gray-200">
                        <h3 class="text-sm font-medium text-gray-700 mb-2">Employee information</h3>
                        <div class="flex justify-between items-center">
                            <span class="text-gray-600">Name:</span>
                            <span id="employeeName" class="font-medium text-gray-900"></span>
                        </div>
                        <div class="flex justify-between items-center mt-2">
                            <span class="text-gray-600">ID No:</span>
                            <span id="employeeIdDisplay" class="font-medium text-gray-900"></span>
                        </div>
                    </div>
                </div>
                
                <!-- 打卡按钮 -->
                <button 
                    type="submit" 
                    class="w-full bg-primary hover:bg-primary/90 text-white font-medium py-3 px-4 rounded-lg transition-all duration-300 flex items-center justify-center"
                >
                    <i class="fa fa-check mr-2"></i> Confirm punch in
                </button>
            </form>
            
            <!-- 打卡记录（员工视图） -->
            <div class="mt-6">
                <div class="flex justify-between items-center mb-3">
                    <h3 class="text-sm font-medium text-gray-700">Today's check-in record</h3>
                    <button id="adminLoginBtn" class="text-sm text-primary hover:text-primary/80 transition-colors">
                        <i class="fa fa-lock mr-1"></i> Administrator entrance
                    </button>
                </div>
                <div id="checkinRecords" class="max-h-40 overflow-y-auto space-y-2">
                    <!-- 打卡记录将在这里动态显示 -->
                    <p class="text-gray-500 text-sm italic text-center">No check-in records yet</p>
                </div>
            </div>
        </div>
        
        <!-- 管理员面板（默认隐藏） -->
        <div id="adminPanel" class="bg-white rounded-xl p-6 md:p-8 card-shadow hidden">
            <div class="flex justify-between items-center mb-6">
                <div>
                    <i class="fa fa-cogs text-primary text-3xl mb-2"></i>
                    <h2 class="text-xl font-bold text-gray-800">Administrator Panel</h2>
                </div>
                <button id="backToCheckinBtn" class="text-gray-500 hover:text-gray-700 transition-colors">
                    <i class="fa fa-arrow-left mr-1"></i> Back to check-in
                </button>
            </div>
            
            <!-- 管理员登录表单 -->
            <div id="adminLoginForm" class="space-y-4">
                <h3 class="font-medium text-gray-800">Please enter administrator password</h3>
                <div>
                    <label for="adminPassword" class="block text-sm font-medium text-gray-700 mb-1">Administrator Password</label>
                    <div class="relative">
                        <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                            <i class="fa fa-key text-gray-400"></i>
                        </div>
                        <input 
                            type="password" 
                            id="adminPassword" 
                            name="adminPassword" 
                            placeholder="Enter administrator password" 
                            class="block w-full pl-10 pr-3 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                            required
                        >
                    </div>
                </div>
                <button 
                    id="doLoginBtn" 
                    class="w-full bg-primary hover:bg-primary/90 text-white font-medium py-3 px-4 rounded-lg transition-all duration-300 flex items-center justify-center"
                >
                    <i class="fa fa-sign-in mr-2"></i> Login
                </button>
            </div>
            
            <!-- 管理员功能区（默认隐藏） -->
            <div id="adminFunctions" class="hidden space-y-6">
                <div class="flex justify-between items-center">
                    <h3 class="font-medium text-gray-800">All check-in records</h3>
                    <button id="exportBtn" class="bg-success hover:bg-success/90 text-white text-sm font-medium py-2 px-4 rounded-lg transition-all duration-300 flex items-center">
                        <i class="fa fa-download mr-2"></i> Export records
                    </button>
                </div>
                
                <!-- 记录筛选 -->
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label for="filterDate" class="block text-sm font-medium text-gray-700 mb-1">Select date</label>
                        <input 
                            type="date" 
                            id="filterDate" 
                            class="block w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                        >
                    </div>
                    <div>
                        <label for="filterEmployee" class="block text-sm font-medium text-gray-700 mb-1">Filter employee</label>
                        <select 
                            id="filterEmployee" 
                            class="block w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                        >
                            <option value="all">All employees</option>
                            <!-- 员工选项将动态添加 -->
                        </select>
                    </div>
                </div>
                
                <!-- 完整打卡记录 -->
                <div class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead>
                            <tr>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Date</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">ID No</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Name</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Clock-in time</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Clock-out time</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">Operation</th>
                            </tr>
                        </thead>
                        <tbody id="adminRecordsTable" class="bg-white divide-y divide-gray-200">
                            <!-- 管理员记录将在这里动态显示 -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
        
        <!-- 修改时间弹窗 (默认隐藏) -->
        <div id="editModal" class="fixed inset-0 bg-black bg-opacity-50 modal-backdrop flex items-center justify-center z-50 hidden">
            <div class="bg-white rounded-lg shadow-xl w-full max-w-md p-6 transform transition-all">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-lg font-medium text-gray-900">Edit check-in time</h3>
                    <button id="closeModalBtn" class="text-gray-400 hover:text-gray-500">
                        <i class="fa fa-times"></i>
                    </button>
                </div>
                
                <form id="editForm">
                    <input type="hidden" id="editRecordIndex">
                    <input type="hidden" id="editRecordType">
                    
                    <div class="mb-4">
                        <label for="editEmployeeInfo" class="block text-sm font-medium text-gray-700 mb-1">Employee information</label>
                        <div class="p-2 bg-gray-50 rounded border border-gray-200">
                            <span id="editEmployeeInfo" class="text-sm"></span>
                        </div>
                    </div>
                    
                    <div class="mb-4">
                        <label for="editDate" class="block text-sm font-medium text-gray-700 mb-1">Date</label>
                        <input 
                            type="date" 
                            id="editDate" 
                            class="block w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                            required
                        >
                    </div>
                    
                    <div class="mb-6">
                        <label for="editTime" class="block text-sm font-medium text-gray-700 mb-1">Time</label>
                        <input 
                            type="time" 
                            id="editTime" 
                            step="1"
                            class="block w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                            required
                        >
                    </div>
                    
                    <div class="flex space-x-3">
                        <button 
                            type="button" 
                            id="cancelEditBtn"
                            class="flex-1 bg-gray-200 hover:bg-gray-300 text-gray-800 font-medium py-2 px-4 rounded-lg transition-all duration-300"
                        >
                            Cancel
                        </button>
                        <button 
                            type="submit" 
                            class="flex-1 bg-primary hover:bg-primary/90 text-white font-medium py-2 px-4 rounded-lg transition-all duration-300"
                        >
                            Save changes
                        </button>
                    </div>
                </form>
            </div>
        </div>
        
        <!-- 通知提示 -->
        <div id="notification" class="mt-4 p-4 rounded-lg hidden transition-all duration-300 transform translate-y-4 opacity-0">
            <div class="flex items-center">
                <i id="notificationIcon" class="mr-3 text-xl"></i>
                <div>
                    <h3 id="notificationTitle" class="font-medium"></h3>
                    <p id="notificationMessage" class="text-sm mt-1"></p>
                </div>
            </div>
        </div>
    </div>

    <script>
        // 员工数据库 - 包含原有56名员工 + 40个空ID占位符（共96个ID）
        const employees = {
            // 原有56名员工信息
            '250001': { name: 'Win Phyu Phyu Lwin' },
            '250004': { name: 'Naw Htoo Phwel Phaw' },
            '250007': { name: 'Hein Tun Soe' },
            '250009': { name: 'Phyo Thiha Kyaw' },
            '250010': { name: 'Satt Wai' },
            '250019': { name: 'Chit Su Wai' },
            '250029': { name: 'Chit Phoo Wai' },
            '250032': { name: 'Myat Thiri Hlaing' },
            '250038': { name: 'Zwe Man Hein' },
            '250039': { name: 'Kaung Lwin Han' },
            '250041': { name: 'Min Khant Kyaw' },
            '250042': { name: 'Win Nandar' },
            '250043': { name: 'Ye Htet' },
            '250045': { name: 'Han Lwin Oo' },
            '250047': { name: 'Myo Thu Aung' },
            '250048': { name: 'Ye Yint' },
            '250049': { name: 'Yarzar Tun' },
            '250050': { name: 'Zwe Thiha' },
            '250051': { name: 'Thein Zaw' },
            '250056': { name: 'Aye Chan Nyi Nyi' },
            '250058': { name: 'Pan Pwint Phoo' },
            '250061': { name: 'Thiha San' },
            '250064': { name: 'Aung Thet Paing' },
            '250067': { name: 'Mei Wadi Tun' },
            '250069': { name: 'Zin May Oo' },
            '250070': { name: 'Thel Thinzar Nwe' },
            '250071': { name: 'Aung Kaung Myat' },
            '250072': { name: 'Ngwe Kyal Sin Thant' },
            '250073': { name: 'Aung Soe Moe' },
            '250075': { name: 'Hein Htet Aung' },
            '250077': { name: 'Hnin Ei Shwe Yee' },
            '250090': { name: 'Saint Nadi Tun' },
            '250092': { name: 'Thit Aung Khant' },
            '250093': { name: 'Thu Htoo Zaw' },
            '250095': { name: 'Shwe Phoo' },
            '250096': { name: 'Thura Myo Satt' },
            '250097': { name: 'Naing Naing Moe Oo' },
            '250098': { name: 'Kyaw Min Htet' },
            '250099': { name: 'Pyae Pyae Thein' },
            '250100': { name: 'Win Kay Khaing' },
            '250101': { name: 'Cho Yun Nwe' },
            '250103': { name: 'Ye Lin Aung' },
            '250104': { name: 'Ye Lin Phyo' },
            '250105': { name: 'Tayza Htun' },
            '250106': { name: 'Phyu Phyu Zaw' },
            '250107': { name: 'Phyo Phyo Nwe' },
            '250108': { name: 'Yamin Oo' },
            '250109': { name: 'Zin lin Tun' },
            '250110': { name: 'Yoon Nadi Aung' },
            '250112': { name: 'Thet Oo Aung' },
            '250113': { name: 'Phyo Wint Kyaw' },
            '250114': { name: 'May Phoo Eaindra' },
            '250115': { name: 'ZarNi Min Htet' },
            '250116': { name: 'Pyae Phyo Aung' },
            '250117': { name: 'Ye Lin Htut' },
            '250118': { name: 'Zaw Lin Htoo' },
            
            // 额外40个空ID占位符（ID号延续原有序列）
            '250119': { name: '' },
            '250120': { name: '' },
            '250121': { name: '' },
            '250122': { name: '' },
            '250123': { name: '' },
            '250124': { name: '' },
            '250125': { name: '' },
            '250126': { name: '' },
            '250127': { name: '' },
            '250128': { name: '' },
            '250129': { name: '' },
            '250130': { name: '' },
            '250131': { name: '' },
            '250132': { name: '' },
            '250133': { name: '' },
            '250134': { name: '' },
            '250135': { name: '' },
            '250136': { name: '' },
            '250137': { name: '' },
            '250138': { name: '' },
            '250139': { name: '' },
            '250140': { name: '' },
            '250141': { name: '' },
            '250142': { name: '' },
            '250143': { name: '' },
            '250144': { name: '' },
            '250145': { name: '' },
            '250146': { name: '' },
            '250147': { name: '' },
            '250148': { name: '' },
            '250149': { name: '' },
            '250150': { name: '' },
            '250151': { name: '' },
            '250152': { name: '' },
            '250153': { name: '' },
            '250154': { name: '' },
            '250155': { name: '' },
            '250156': { name: '' },
            '250157': { name: '' },
            '250158': { name: '' }
        };
        
        // 管理员密码 (实际应用中应加密存储)
        const ADMIN_PASSWORD = 'admin123';
        
        // 数据保留时间（2个月）
        const DATA_RETENTION_MONTHS = 2;
        
        // 存储打卡记录 - 使用localStorage持久化存储，并自动清理旧数据
        let checkRecords = loadAndCleanRecords();
        
        // 加载并清理旧记录
        function loadAndCleanRecords() {
            const savedRecords = JSON.parse(localStorage.getItem('checkRecords')) || [];
            const twoMonthsAgo = new Date();
            twoMonthsAgo.setMonth(twoMonthsAgo.getMonth() - DATA_RETENTION_MONTHS);
            
            // 过滤掉超过2个月的记录
            const recentRecords = savedRecords.filter(record => {
                return new Date(record.date) >= twoMonthsAgo;
            });
            
            // 如果有记录被清理，更新存储
            if (recentRecords.length < savedRecords.length) {
                localStorage.setItem('checkRecords', JSON.stringify(recentRecords));
                console.log(`Cleaned up ${savedRecords.length - recentRecords.length} records older than ${DATA_RETENTION_MONTHS} months`);
            }
            
            return recentRecords;
        }
        
        // DOM元素
        const checkinForm = document.getElementById('checkinForm');
        const employeeIdInput = document.getElementById('employeeId');
        const employeeInfo = document.getElementById('employeeInfo');
        const employeeName = document.getElementById('employeeName');
        const employeeIdDisplay = document.getElementById('employeeIdDisplay');
        const checkRecordsContainer = document.getElementById('checkinRecords');
        const adminLoginBtn = document.getElementById('adminLoginBtn');
        const backToCheckinBtn = document.getElementById('backToCheckinBtn');
        const checkinPanel = document.getElementById('checkinPanel');
        const adminPanel = document.getElementById('adminPanel');
        const adminLoginForm = document.getElementById('adminLoginForm');
        const adminFunctions = document.getElementById('adminFunctions');
        const adminPasswordInput = document.getElementById('adminPassword');
        const doLoginBtn = document.getElementById('doLoginBtn');
        const exportBtn = document.getElementById('exportBtn');
        const filterDateInput = document.getElementById('filterDate');
        const filterEmployeeSelect = document.getElementById('filterEmployee');
        const adminRecordsTable = document.getElementById('adminRecordsTable');
        const notification = document.getElementById('notification');
        const notificationIcon = document.getElementById('notificationIcon');
        const notificationTitle = document.getElementById('notificationTitle');
        const notificationMessage = document.getElementById('notificationMessage');
        
        // 修改时间弹窗元素
        const editModal = document.getElementById('editModal');
        const closeModalBtn = document.getElementById('closeModalBtn');
        const cancelEditBtn = document.getElementById('cancelEditBtn');
        const editForm = document.getElementById('editForm');
        const editRecordIndex = document.getElementById('editRecordIndex');
        const editRecordType = document.getElementById('editRecordType');
        const editEmployeeInfo = document.getElementById('editEmployeeInfo');
        const editDate = document.getElementById('editDate');
        const editTime = document.getElementById('editTime');
        
        // 初始化页面
        document.addEventListener('DOMContentLoaded', function() {
            // 设置默认筛选日期为今天
            const today = new Date().toISOString().split('T')[0];
            filterDateInput.value = today;
            
            // 初始化员工筛选下拉框
            initEmployeeFilter();
            
            // 更新打卡记录显示
            updateCheckRecords();
            
            // 更新管理员记录显示
            updateAdminRecords();
            
            // 绑定弹窗事件
            bindModalEvents();
        });
        
        // 绑定弹窗相关事件
        function bindModalEvents() {
            // 关闭弹窗
            closeModalBtn.addEventListener('click', closeEditModal);
            cancelEditBtn.addEventListener('click', closeEditModal);
            
            // 点击弹窗外部关闭
            editModal.addEventListener('click', function(e) {
                if (e.target === editModal) {
                    closeEditModal();
                }
            });
            
            // 提交修改
            editForm.addEventListener('submit', function(e) {
                e.preventDefault();
                saveEditedRecord();
            });
        }
        
        // 打开修改弹窗
        function openEditModal(recordIndex, recordType) {
            const record = checkRecords[recordIndex];
            if (!record) return;
            
            // 填充表单数据
            editRecordIndex.value = recordIndex;
            editRecordType.value = recordType;
            
            // 处理空名称显示
            const displayName = record.name || 'Unnamed Employee';
            const checkTypeText = record.type === 'checkin' ? 'Clock-in' : 'Clock-out';
            editEmployeeInfo.textContent = `${displayName} (${record.id}) - ${checkTypeText}`;
            
            // 解析日期和时间
            const recordDate = new Date(record.date);
            editDate.value = recordDate.toISOString().split('T')[0];
            
            // 格式化时间为HH:MM:SS
            const hours = String(recordDate.getHours()).padStart(2, '0');
            const minutes = String(recordDate.getMinutes()).padStart(2, '0');
            const seconds = String(recordDate.getSeconds()).padStart(2, '0');
            editTime.value = `${hours}:${minutes}:${seconds}`;
            
            // 显示弹窗
            editModal.classList.remove('hidden');
            document.body.style.overflow = 'hidden'; // 防止背景滚动
        }
        
        // 关闭修改弹窗
        function closeEditModal() {
            editModal.classList.add('hidden');
            document.body.style.overflow = ''; // 恢复背景滚动
        }
        
        // 保存修改后的记录
        function saveEditedRecord() {
            const recordIndex = parseInt(editRecordIndex.value);
            const record = checkRecords[recordIndex];
            
            if (!record) {
                showNotification('Error', 'Record to edit not found', 'error');
                closeEditModal();
                return;
            }
            
            // 获取新的日期和时间
            const newDate = editDate.value;
            const newTime = editTime.value;
            
            if (!newDate || !newTime) {
                showNotification('Error', 'Please fill in complete date and time', 'error');
                return;
            }
            
            // 合并日期和时间
            const newDateTime = new Date(`${newDate}T${newTime}`);
            
            // 更新记录
            record.date = newDateTime.toISOString();
            record.time = newDateTime.toLocaleTimeString();
            
            // 保存到本地存储
            localStorage.setItem('checkRecords', JSON.stringify(checkRecords));
            
            // 更新显示
            updateCheckRecords();
            updateAdminRecords();
            
            // 关闭弹窗并显示成功消息
            closeEditModal();
            showNotification('Success', 'Check-in time has been updated', 'success');
        }
        
        // 初始化员工筛选下拉框
        function initEmployeeFilter() {
            // 清空现有选项（保留"所有员工"）
            const allOption = filterEmployeeSelect.querySelector('option[value="all"]');
            filterEmployeeSelect.innerHTML = '';
            filterEmployeeSelect.appendChild(allOption);
            
            // 按ID号排序添加员工选项
            const sortedEmployeeIds = Object.keys(employees).sort((a, b) => parseInt(a) - parseInt(b));
            
            sortedEmployeeIds.forEach(id => {
                const employee = employees[id];
                const option = document.createElement('option');
                option.value = id;
                
                // 处理空名称显示
                const displayName = employee.name || `Unnamed (${id})`;
                option.textContent = `${displayName}`;
                
                filterEmployeeSelect.appendChild(option);
            });
        }
        
        // 监听ID No输入，实时显示员工信息
        employeeIdInput.addEventListener('input', function() {
            const empId = this.value.trim();
            
            if (empId && employees[empId]) {
                // 显示员工信息（处理空名称）
                const displayName = employees[empId].name || 'Unnamed Employee';
                employeeName.textContent = displayName;
                employeeIdDisplay.textContent = empId;
                employeeInfo.classList.remove('hidden');
            } else {
                // 隐藏员工信息
                employeeInfo.classList.add('hidden');
            }
        });
        
        // 表单提交处理
        checkinForm.addEventListener('submit', function(e) {
            e.preventDefault();
            
            const empId = employeeIdInput.value.trim();
            const checkType = document.querySelector('input[name="checkType"]:checked').value;
            const checkTypeText = checkType === 'checkin' ? 'Clock-in' : 'Clock-out';
            const checkTypeTextCN = checkType === 'checkin' ? '上班' : '下班'; // 用于中文通知
            
            // 验证ID No
            if (!empId) {
                showNotification('Error', 'Please enter ID No', 'error');
                return;
            }
            
            // 检查员工是否存在
            if (!employees[empId]) {
                showNotification('Error', 'ID No does not exist', 'error');
                return;
            }
            
            // 检查是否重复打卡
            const now = new Date();
            const today = now.toLocaleDateString();
            const hasChecked = checkRecords.some(record => 
                record.id === empId && 
                record.type === checkType &&
                new Date(record.date).toLocaleDateString() === today
            );
            
            if (hasChecked) {
                showNotification('Notice', `You have already completed ${checkTypeText} today`, 'warning');
                return;
            }
            
            // 记录打卡信息
            const checkTime = now.toLocaleTimeString();
            const employee = employees[empId];
            const displayName = employee.name || 'Unnamed Employee';
            
            const newRecord = {
                id: empId,
                name: displayName,
                type: checkType,
                typeText: checkTypeTextCN, // 用于表格显示
                time: checkTime,
                date: now.toISOString()
            };
            
            checkRecords.push(newRecord);
            
            // 保存到localStorage
            localStorage.setItem('checkRecords', JSON.stringify(checkRecords));
            
            // 更新打卡记录显示
            updateCheckRecords();
            updateAdminRecords();
            
            // 显示成功通知
            showNotification('Success', `${displayName} ${checkTypeText} successful`, 'success');
            
            // 重置表单
            employeeIdInput.value = '';
            employeeInfo.classList.add('hidden');
        });
        
        // 切换到管理员面板
        adminLoginBtn.addEventListener('click', function() {
            checkinPanel.classList.add('hidden');
            adminPanel.classList.remove('hidden');
            adminLoginForm.classList.remove('hidden');
            adminFunctions.classList.add('hidden');
            adminPasswordInput.value = '';
        });
        
        // 返回打卡面板
        backToCheckinBtn.addEventListener('click', function() {
            adminPanel.classList.add('hidden');
            checkinPanel.classList.remove('hidden');
        });
        
        // 管理员登录
        doLoginBtn.addEventListener('click', function() {
            const password = adminPasswordInput.value.trim();
            
            if (password === ADMIN_PASSWORD) {
                adminLoginForm.classList.add('hidden');
                adminFunctions.classList.remove('hidden');
                showNotification('Success', 'Administrator logged in successfully', 'success');
            } else {
                showNotification('Error', 'Incorrect password, please try again', 'error');
            }
        });
        
        // 筛选条件变化时更新记录
        filterDateInput.addEventListener('change', updateAdminRecords);
        filterEmployeeSelect.addEventListener('change', updateAdminRecords);
        
        // 导出打卡记录
        exportBtn.addEventListener('click', function() {
            const filteredRecords = getFilteredRecords();
            
            if (filteredRecords.length === 0) {
                showNotification('Notice', 'No records to export', 'warning');
                return;
            }
            
            // 准备CSV内容（英文表头）
            let csvContent = "Date,ID No,Name,Clock-in Time,Clock-out Time\n";
            
            // 按员工和日期分组
            const groupedRecords = {};
            
            filteredRecords.forEach(record => {
                const date = new Date(record.date).toLocaleDateString();
                const key = `${record.id}-${date}`;
                
                if (!groupedRecords[key]) {
                    groupedRecords[key] = {
                        date: date,
                        id: record.id,
                        name: record.name || 'Unnamed Employee',
                        checkin: '',
                        checkout: ''
                    };
                }
                
                if (record.type === 'checkin') {
                    groupedRecords[key].checkin = record.time;
                } else {
                    groupedRecords[key].checkout = record.time;
                }
            });
            
            // 添加到CSV
            Object.values(groupedRecords).forEach(record => {
                // 处理CSV中的逗号转义
                const safeName = record.name.includes(',') ? `"${record.name}"` : record.name;
                csvContent += `${record.date},${record.id},${safeName},${record.checkin},${record.checkout}\n`;
            });
            
            // 创建并下载CSV文件
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            const fileName = `Check-in_Records_${filterDateInput.value || new Date().toLocaleDateString()}.csv`;
            link.setAttribute('href', url);
            link.setAttribute('download', fileName);
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            showNotification('Success', `Exported ${Object.values(groupedRecords).length} records`, 'success');
        });
        
        // 获取筛选后的记录
        function getFilteredRecords() {
            const selectedDate = filterDateInput.value;
            const selectedEmployee = filterEmployeeSelect.value;
            
            return checkRecords.filter(record => {
                // 日期筛选
                const recordDate = new Date(record.date).toISOString().split('T')[0];
                const dateMatch = !selectedDate || recordDate === selectedDate;
                
                // 员工筛选
                const employeeMatch = selectedEmployee === 'all' || record.id === selectedEmployee;
                
                return dateMatch && employeeMatch;
            });
        }
        
        // 更新打卡记录显示（员工视图）
        function updateCheckRecords() {
            // 清空容器
            checkRecordsContainer.innerHTML = '';
            
            // 获取今天的记录
            const today = new Date().toLocaleDateString();
            const todayRecords = checkRecords.filter(record => 
                new Date(record.date).toLocaleDateString() === today
            );
            
            if (todayRecords.length === 0) {
                checkRecordsContainer.innerHTML = '<p class="text-gray-500 text-sm italic text-center">No check-in records yet</p>';
                return;
            }
            
            // 按时间排序（最新的在前面）
            const sortedRecords = [...todayRecords].sort((a, b) => new Date(b.date) - new Date(a.date));
            
            // 添加所有打卡记录
            sortedRecords.forEach(record => {
                const recordElement = document.createElement('div');
                const typeClass = record.type === 'checkin' ? 'bg-green-50 border-green-200' : 'bg-blue-50 border-blue-200';
                const typeIcon = record.type === 'checkin' ? 
                    '<i class="fa fa-sign-in text-success mr-1"></i>' : 
                    '<i class="fa fa-sign-out text-primary mr-1"></i>';
                const checkTypeText = record.type === 'checkin' ? 'Clock-in' : 'Clock-out';
                const displayName = record.name || 'Unnamed Employee';
                
                recordElement.className = `p-3 rounded border ${typeClass} flex flex-col sm:flex-row justify-between items-start sm:items-center`;
                
                recordElement.innerHTML = `
                    <div class="mb-2 sm:mb-0">
                        <div class="font-medium">${displayName}</div>
                        <div class="text-sm text-gray-500">${record.id}</div>
                    </div>
                    <div class="flex items-center text-sm">
                        ${typeIcon}
                        <span class="mr-3">${checkTypeText}</span>
                        <span class="text-gray-500">${record.time}</span>
                    </div>
                `;
                
                checkRecordsContainer.appendChild(recordElement);
            });
        }
        
        // 更新管理员记录显示
        function updateAdminRecords() {
            const filteredRecords = getFilteredRecords();
            adminRecordsTable.innerHTML = '';
            
            if (filteredRecords.length === 0) {
                const emptyRow = document.createElement('tr');
                emptyRow.innerHTML = `
                    <td colspan="6" class="px-4 py-8 text-center text-gray-500">
                        No matching check-in records found
                    </td>
                `;
                adminRecordsTable.appendChild(emptyRow);
                return;
            }
            
            // 按员工和日期分组
            const groupedRecords = {};
            
            filteredRecords.forEach(record => {
                const date = new Date(record.date).toLocaleDateString();
                const key = `${record.id}-${date}`;
                
                if (!groupedRecords[key]) {
                    groupedRecords[key] = {
                        date: date,
                        id: record.id,
                        name: record.name || 'Unnamed Employee',
                        checkin: '',
                        checkout: '',
                        checkinIndex: -1,
                        checkoutIndex: -1
                    };
                }
                
                if (record.type === 'checkin') {
                    groupedRecords[key].checkin = record.time;
                    groupedRecords[key].checkinIndex = checkRecords.indexOf(record);
                } else {
                    groupedRecords[key].checkout = record.time;
                    groupedRecords[key].checkoutIndex = checkRecords.indexOf(record);
                }
            });
            
            // 按ID号排序显示
            const sortedGroups = Object.values(groupedRecords).sort((a, b) => parseInt(a.id) - parseInt(b.id));
            
            // 添加到表格
            sortedGroups.forEach(record => {
                const row = document.createElement('tr');
                row.className = 'hover:bg-gray-50 transition-colors';
                
                // 生成修改按钮
                const checkinEditBtn = record.checkinIndex !== -1 ? 
                    `<button onclick="openEditModal(${record.checkinIndex}, 'checkin')" class="text-primary hover:text-primary/80 text-sm">
                        <i class="fa fa-pencil mr-1"></i>Edit
                    </button>` : '';
                    
                const checkoutEditBtn = record.checkoutIndex !== -1 ? 
                    `<button onclick="openEditModal(${record.checkoutIndex}, 'checkout')" class="text-primary hover:text-primary/80 text-sm">
                        <i class="fa fa-pencil mr-1"></i>Edit
                    </button>` : '';
                
                row.innerHTML = `
                    <td class="px-4 py-3 whitespace-nowrap">${record.date}</td>
                    <td class="px-4 py-3 whitespace-nowrap">${record.id}</td>
                    <td class="px-4 py-3 whitespace-nowrap">${record.name}</td>
                    <td class="px-4 py-3 whitespace-nowrap ${record.checkin ? '' : 'text-gray-400'}">
                        ${record.checkin || 'Not checked in'}
                    </td>
                    <td class="px-4 py-3 whitespace-nowrap ${record.checkout ? '' : 'text-gray-400'}">
                        ${record.checkout || 'Not checked out'}
                    </td>
                    <td class="px-4 py-3 whitespace-nowrap">
                        <div class="flex space-x-2">
                            <div>${checkinEditBtn}</div>
                            <div>${checkoutEditBtn}</div>
                        </div>
                    </td>
                `;
                adminRecordsTable.appendChild(row);
            });
        }
        
        // 显示通知
        function showNotification(title, message, type = 'info') {
            // 设置通知内容
            notificationTitle.textContent = title;
            notificationMessage.textContent = message;
            
            // 设置通知样式
            notification.className = 'mt-4 p-4 rounded-lg transition-all duration-300 transform translate-y-0 opacity-100';
            
            // 根据类型设置不同样式
            if (type === 'success') {
                notification.classList.add('bg-green-50', 'border', 'border-green-200');
                notificationTitle.classList.add('text-green-800');
                notificationMessage.classList.add('text-green-700');
                notificationIcon.className = 'fa fa-check-circle text-green-500 mr-3 text-xl';
            } else if (type === 'error') {
                notification.classList.add('bg-red-50', 'border', 'border-red-200');
                notificationTitle.classList.add('text-red-800');
                notificationMessage.classList.add('text-red-700');
                notificationIcon.className = 'fa fa-exclamation-circle text-red-500 mr-3 text-xl';
            } else if (type === 'warning') {
                notification.classList.add('bg-yellow-50', 'border', 'border-yellow-200');
                notificationTitle.classList.add('text-yellow-800');
                notificationMessage.classList.add('text-yellow-700');
                notificationIcon.className = 'fa fa-exclamation-triangle text-yellow-500 mr-3 text-xl';
            } else {
                notification.classList.add('bg-blue-50', 'border', 'border-blue-200');
                notificationTitle.classList.add('text-blue-800');
                notificationMessage.classList.add('text-blue-700');
                notificationIcon.className = 'fa fa-info-circle text-blue-500 mr-3 text-xl';
            }
            
            // 3秒后隐藏通知
            setTimeout(() => {
                notification.classList.remove('translate-y-0', 'opacity-100');
                notification.classList.add('translate-y-4', 'opacity-0');
            }, 3000);
        }
        
        // 暴露给全局，供HTML中的onclick调用
        window.openEditModal = openEditModal;
    </script>
</body>
</html>
