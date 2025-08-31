<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>企业在线打卡系统</title>
    <!-- 引入Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- 引入Font Awesome -->
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    
    <!-- 配置Tailwind自定义颜色和字体 -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#165DFF',
                        secondary: '#36CFC9',
                        success: '#52C41A',
                        warning: '#FAAD14',
                        danger: '#FF4D4F',
                        neutral: '#F5F5F5',
                        'neutral-dark': '#434343'
                    },
                    fontFamily: {
                        inter: ['Inter', 'system-ui', 'sans-serif'],
                    },
                }
            }
        }
    </script>
    
    <!-- 自定义工具类 -->
    <style type="text/tailwindcss">
        @layer utilities {
            .content-auto {
                content-visibility: auto;
            }
            .card-shadow {
                box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
            }
            .btn-hover {
                @apply transition-all duration-300 hover:shadow-lg transform hover:-translate-y-0.5;
            }
            .gradient-bg {
                background: linear-gradient(135deg, #165DFF 0%, #36CFC9 100%);
            }
        }
    </style>
    
    <style>
        /* 全局样式 */
        body {
            font-family: 'Inter', system-ui, sans-serif;
        }
        
        /* 平滑滚动 */
        html {
            scroll-behavior: smooth;
        }
        
        /* 动画效果 */
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .animate-fadeIn {
            animation: fadeIn 0.5s ease forwards;
        }
        
        .pulse {
            animation: pulse 2s infinite;
        }
        
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body class="bg-gray-50 text-neutral-dark min-h-screen">
    <!-- 顶部导航栏 -->
    <header id="navbar" class="fixed w-full bg-white/90 backdrop-blur-sm z-50 transition-all duration-300 shadow-sm">
        <div class="container mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center space-x-2">
                <i class="fa fa-clock-o text-primary text-2xl"></i>
                <h1 class="text-xl md:text-2xl font-bold text-primary">企业在线打卡系统</h1>
            </div>
            
            <div class="flex items-center space-x-4">
                <div id="userInfo" class="hidden md:flex items-center space-x-2">
                    <img src="https://picsum.photos/id/1005/40/40" alt="用户头像" class="w-8 h-8 rounded-full object-cover border-2 border-primary">
                    <span id="usernameDisplay" class="font-medium"></span>
                </div>
                <button id="logoutBtn" class="hidden text-gray-600 hover:text-danger transition-colors">
                    <i class="fa fa-sign-out"></i>
                    <span class="ml-1 hidden md:inline">退出</span>
                </button>
            </div>
        </div>
    </header>

    <!-- 主内容区 -->
    <main class="container mx-auto px-4 pt-24 pb-16">
        <!-- 登录界面 -->
        <section id="loginSection" class="max-w-md mx-auto animate-fadeIn">
            <div class="bg-white rounded-xl p-6 md:p-8 card-shadow">
                <div class="text-center mb-6">
                    <div class="inline-flex items-center justify-center w-16 h-16 rounded-full gradient-bg mb-4">
                        <i class="fa fa-key text-white text-2xl"></i>
                    </div>
                    <h2 class="text-2xl font-bold text-gray-800">员工登录</h2>
                    <p class="text-gray-500 mt-1">请输入工号和密码登录系统</p>
                </div>
                
                <form id="loginForm" class="space-y-4">
                    <div>
                        <label for="employeeId" class="block text-sm font-medium text-gray-700 mb-1">工号</label>
                        <div class="relative">
                            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                <i class="fa fa-id-card text-gray-400"></i>
                            </div>
                            <input type="text" id="employeeId" name="employeeId" 
                                class="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                placeholder="请输入您的工号" required>
                        </div>
                    </div>
                    
                    <div>
                        <label for="password" class="block text-sm font-medium text-gray-700 mb-1">密码</label>
                        <div class="relative">
                            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                <i class="fa fa-lock text-gray-400"></i>
                            </div>
                            <input type="password" id="password" name="password" 
                                class="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                placeholder="请输入您的密码" required>
                            <button type="button" id="togglePassword" class="absolute inset-y-0 right-0 pr-3 flex items-center text-gray-400 hover:text-gray-600">
                                <i class="fa fa-eye-slash"></i>
                            </button>
                        </div>
                    </div>
                    
                    <div class="pt-2">
                        <button type="submit" class="w-full bg-primary text-white py-3 px-4 rounded-lg font-medium btn-hover">
                            <i class="fa fa-sign-in mr-2"></i>登录系统
                        </button>
                    </div>
                </form>
                
                <div class="mt-4 text-center text-gray-500 text-sm">
                    <p>忘记密码？请联系管理员重置</p>
                </div>
            </div>
        </section>

        <!-- 打卡界面 (默认隐藏) -->
        <section id="checkinSection" class="hidden max-w-4xl mx-auto animate-fadeIn">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <!-- 左侧：打卡卡片 -->
                <div class="md:col-span-1">
                    <div class="bg-white rounded-xl p-6 card-shadow h-full">
                        <h2 class="text-xl font-bold text-gray-800 mb-4 flex items-center">
                            <i class="fa fa-calendar-check-o text-primary mr-2"></i>今日打卡
                        </h2>
                        
                        <div class="text-center py-4 border-y border-gray-100">
                            <p id="currentDate" class="text-gray-500 mb-1"></p>
                            <p id="currentTime" class="text-2xl font-mono font-bold text-gray-800"></p>
                        </div>
                        
                        <div class="mt-6 space-y-3">
                            <div class="flex justify-between items-center">
                                <span class="text-gray-600">上班打卡</span>
                                <span id="morningCheckin" class="text-gray-400">未打卡</span>
                            </div>
                            <div class="flex justify-between items-center">
                                <span class="text-gray-600">下班打卡</span>
                                <span id="eveningCheckout" class="text-gray-400">未打卡</span>
                            </div>
                        </div>
                        
                        <div class="mt-8">
                            <button id="checkinBtn" class="w-full bg-success text-white py-3 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                                <i class="fa fa-location-arrow mr-2"></i>
                                <span id="checkinBtnText">上班打卡</span>
                            </button>
                        </div>
                        
                        <div class="mt-4 text-center text-sm text-gray-500">
                            <p>定位服务将用于确认打卡地点</p>
                            <p id="locationStatus" class="text-warning mt-1"><i class="fa fa-info-circle mr-1"></i>获取位置中...</p>
                        </div>
                    </div>
                </div>
                
                <!-- 右侧：打卡状态和统计 -->
                <div class="md:col-span-2 space-y-6">
                    <!-- 本月打卡统计 -->
                    <div class="bg-white rounded-xl p-6 card-shadow">
                        <h2 class="text-xl font-bold text-gray-800 mb-4 flex items-center">
                            <i class="fa fa-bar-chart text-primary mr-2"></i>本月打卡统计
                        </h2>
                        
                        <div class="grid grid-cols-2 md:grid-cols-4 gap-4 text-center">
                            <div class="p-4 bg-blue-50 rounded-lg">
                                <p class="text-gray-500 text-sm">应打卡天数</p>
                                <p id="totalDays" class="text-2xl font-bold text-primary mt-1">22</p>
                            </div>
                            <div class="p-4 bg-green-50 rounded-lg">
                                <p class="text-gray-500 text-sm">已打卡天数</p>
                                <p id="checkedDays" class="text-2xl font-bold text-success mt-1">8</p>
                            </div>
                            <div class="p-4 bg-yellow-50 rounded-lg">
                                <p class="text-gray-500 text-sm">迟到次数</p>
                                <p id="lateCount" class="text-2xl font-bold text-warning mt-1">1</p>
                            </div>
                            <div class="p-4 bg-red-50 rounded-lg">
                                <p class="text-gray-500 text-sm">缺勤次数</p>
                                <p id="absentCount" class="text-2xl font-bold text-danger mt-1">0</p>
                            </div>
                        </div>
                    </div>
                    
                    <!-- 最近打卡记录 -->
                    <div class="bg-white rounded-xl p-6 card-shadow">
                        <div class="flex justify-between items-center mb-4">
                            <h2 class="text-xl font-bold text-gray-800 flex items-center">
                                <i class="fa fa-history text-primary mr-2"></i>最近打卡记录
                            </h2>
                            <button id="viewAllRecordsBtn" class="text-primary hover:text-primary/80 text-sm font-medium flex items-center">
                                查看全部 <i class="fa fa-angle-right ml-1"></i>
                            </button>
                        </div>
                        
                        <div class="overflow-x-auto">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead>
                                    <tr>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日期</th>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">上班时间</th>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">下班时间</th>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                                    </tr>
                                </thead>
                                <tbody id="recentRecords" class="bg-white divide-y divide-gray-200">
                                    <!-- 记录将通过JS动态生成 -->
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- 打卡记录查询界面 (默认隐藏) -->
        <section id="recordsSection" class="hidden max-w-4xl mx-auto animate-fadeIn">
            <div class="bg-white rounded-xl p-6 card-shadow mb-6">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6">
                    <h2 class="text-xl font-bold text-gray-800 mb-4 md:mb-0 flex items-center">
                        <i class="fa fa-list-alt text-primary mr-2"></i>打卡记录查询
                    </h2>
                    
                    <div class="flex flex-col sm:flex-row w-full md:w-auto gap-3">
                        <div class="relative w-full sm:w-auto">
                            <select id="monthSelector" class="block w-full sm:w-48 pl-3 pr-10 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary appearance-none">
                                <!-- 月份选项将通过JS动态生成 -->
                            </select>
                            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                                <i class="fa fa-chevron-down text-xs"></i>
                            </div>
                        </div>
                        
                        <button id="backToCheckinBtn" class="bg-gray-100 text-gray-700 py-2 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                            <i class="fa fa-arrow-left mr-2"></i>返回打卡
                        </button>
                    </div>
                </div>
                
                <div class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead>
                            <tr>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日期</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">星期</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">上班时间</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">下班时间</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">工作时长</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                            </tr>
                        </thead>
                        <tbody id="allRecords" class="bg-white divide-y divide-gray-200">
                            <!-- 记录将通过JS动态生成 -->
                        </tbody>
                    </table>
                </div>
                
                <div class="mt-6 flex justify-between items-center">
                    <div class="text-gray-600 text-sm">
                        显示 <span id="showingCount">0</span> 条记录，共 <span id="totalCount">0</span> 条
                    </div>
                    
                    <div class="flex space-x-2">
                        <button id="exportExcelBtn" class="bg-green-600 text-white py-2 px-4 rounded-lg font-medium btn-hover flex items-center">
                            <i class="fa fa-download mr-2"></i>导出Excel
                        </button>
                    </div>
                </div>
            </div>
        </section>
    </main>

    <!-- 页脚 -->
    <footer class="bg-white border-t border-gray-200 py-6">
        <div class="container mx-auto px-4 text-center text-gray-500 text-sm">
            <p>© 2023 企业在线打卡系统 - 版权所有</p>
            <p class="mt-1">技术支持：IT部门</p>
        </div>
    </footer>

    <!-- 通知弹窗 -->
    <div id="notification" class="fixed bottom-6 right-6 bg-white rounded-lg shadow-lg p-4 max-w-sm transform translate-y-20 opacity-0 transition-all duration-300 pointer-events-none z-50">
        <div class="flex items-start">
            <div id="notificationIcon" class="mr-3 text-xl mt-0.5">
                <i class="fa fa-check-circle text-success"></i>
            </div>
            <div class="flex-1">
                <h3 id="notificationTitle" class="font-medium text-gray-800"></h3>
                <p id="notificationMessage" class="text-gray-600 text-sm mt-1"></p>
            </div>
            <button id="closeNotification" class="text-gray-400 hover:text-gray-600 ml-2">
                <i class="fa fa-times"></i>
            </button>
        </div>
    </div>

    <!-- JavaScript -->
    <script>
        // 全局变量
        let currentUser = null;
        let checkinStatus = {
            hasCheckedIn: false,
            hasCheckedOut: false,
            morningTime: null,
            eveningTime: null
        };
        let currentLocation = null;
        let打卡记录 = [];

        // DOM元素
        const loginSection = document.getElementById('loginSection');
        const checkinSection = document.getElementById('checkinSection');
        const recordsSection = document.getElementById('recordsSection');
        const loginForm = document.getElementById('loginForm');
        const togglePassword = document.getElementById('togglePassword');
        const passwordInput = document.getElementById('password');
        const usernameDisplay = document.getElementById('usernameDisplay');
        const logoutBtn = document.getElementById('logoutBtn');
        const checkinBtn = document.getElementById('checkinBtn');
        const checkinBtnText = document.getElementById('checkinBtnText');
        const morningCheckin = document.getElementById('morningCheckin');
        const eveningCheckout = document.getElementById('eveningCheckout');
        const currentDateEl = document.getElementById('currentDate');
        const currentTimeEl = document.getElementById('currentTime');
        const locationStatus = document.getElementById('locationStatus');
        const viewAllRecordsBtn = document.getElementById('viewAllRecordsBtn');
        const backToCheckinBtn = document.getElementById('backToCheckinBtn');
        const recentRecordsEl = document.getElementById('recentRecords');
        const allRecordsEl = document.getElementById('allRecords');
        const monthSelector = document.getElementById('monthSelector');
        const showingCountEl = document.getElementById('showingCount');
        const totalCountEl = document.getElementById('totalCount');
        const exportExcelBtn = document.getElementById('exportExcelBtn');
        const notification = document.getElementById('notification');
        const notificationTitle = document.getElementById('notificationTitle');
        const notificationMessage = document.getElementById('notificationMessage');
        const notificationIcon = document.getElementById('notificationIcon');
        const closeNotification = document.getElementById('closeNotification');
        const navbar = document.getElementById('navbar');

        // 初始化
        document.addEventListener('DOMContentLoaded', () => {
            // 更新时间
            updateDateTime();
            setInterval(updateDateTime, 1000);
            
            // 初始化月份选择器
            initMonthSelector();
            
            // 尝试从本地存储加载用户信息
            const savedUser = localStorage.getItem('currentUser');
            if (savedUser) {
                currentUser = JSON.parse(savedUser);
                showCheckinSection();
                loadCheckinData();
            }
            
            // 事件监听
            loginForm.addEventListener('submit', handleLogin);
            togglePassword.addEventListener('click', togglePasswordVisibility);
            logoutBtn.addEventListener('click', handleLogout);
            checkinBtn.addEventListener('click', handleCheckin);
            viewAllRecordsBtn.addEventListener('click', showRecordsSection);
            backToCheckinBtn.addEventListener('click', showCheckinSection);
            monthSelector.addEventListener('change', loadAllRecords);
            exportExcelBtn.addEventListener('click', exportRecords);
            closeNotification.addEventListener('click', hideNotification);
            
            // 滚动时导航栏效果
            window.addEventListener('scroll', () => {
                if (window.scrollY > 10) {
                    navbar.classList.add('shadow');
                } else {
                    navbar.classList.remove('shadow');
                }
            });
            
            // 获取位置
            getLocation();
        });

        // 处理登录
        function handleLogin(e) {
            e.preventDefault();
            
            const employeeId = document.getElementById('employeeId').value;
            const password = passwordInput.value;
            
            // 简单验证 (实际应用中应与后端交互)
            if (employeeId && password) {
                // 模拟登录成功
                currentUser = {
                    id: employeeId,
                    name: employeeId === 'admin' ? '管理员' : `员工${employeeId}`
                };
                
                // 保存到本地存储
                localStorage.setItem('currentUser', JSON.stringify(currentUser));
                
                // 显示打卡界面
                showCheckinSection();
                
                // 加载打卡数据
                loadCheckinData();
                
                // 显示通知
                showNotification('登录成功', `欢迎回来，${currentUser.name}`, 'success');
                
                // 重置表单
                loginForm.reset();
            } else {
                showNotification('登录失败', '请输入工号和密码', 'error');
            }
        }

        // 切换密码可见性
        function togglePasswordVisibility() {
            const type = passwordInput.getAttribute('type') === 'password' ? 'text' : 'password';
            passwordInput.setAttribute('type', type);
            
            // 切换图标
            const icon = togglePassword.querySelector('i');
            if (type === 'password') {
                icon.classList.remove('fa-eye');
                icon.classList.add('fa-eye-slash');
            } else {
                icon.classList.remove('fa-eye-slash');
                icon.classList.add('fa-eye');
            }
        }

        // 处理登出
        function handleLogout() {
            // 清除本地存储
            localStorage.removeItem('currentUser');
            currentUser = null;
            
            // 显示登录界面
            loginSection.classList.remove('hidden');
            checkinSection.classList.add('hidden');
            recordsSection.classList.add('hidden');
            
            showNotification('已退出登录', '您已成功退出系统', 'info');
        }

        // 处理打卡
        function handleCheckin() {
            if (!currentLocation) {
                showNotification('打卡失败', '请等待位置获取完成', 'error');
                getLocation();
                return;
            }
            
            const now = new Date();
            const hours = now.getHours();
            
            if (!checkinStatus.hasCheckedIn) {
                // 上班打卡
                checkinStatus.hasCheckedIn = true;
                checkinStatus.morningTime = formatTime(now);
                
                // 更新UI
                morningCheckin.textContent = checkinStatus.morningTime;
                morningCheckin.classList.remove('text-gray-400');
                morningCheckin.classList.add('text-success');
                
                // 更新按钮状态
                checkinBtnText.textContent = '下班打卡';
                checkinBtn.classList.remove('bg-success');
                checkinBtn.classList.add('bg-primary');
                
                // 保存打卡记录
                saveCheckinRecord();
                
                showNotification('上班打卡成功', `打卡时间: ${checkinStatus.morningTime}`, 'success');
            } else if (!checkinStatus.hasCheckedOut) {
                // 下班打卡
                checkinStatus.hasCheckedOut = true;
                checkinStatus.eveningTime = formatTime(now);
                
                // 更新UI
                eveningCheckout.textContent = checkinStatus.eveningTime;
                eveningCheckout.classList.remove('text-gray-400');
                eveningCheckout.classList.add('text-success');
                
                // 更新按钮状态
                checkinBtnText.textContent = '今日已完成打卡';
                checkinBtn.disabled = true;
                checkinBtn.classList.remove('bg-primary');
                checkinBtn.classList.add('bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.remove('btn-hover');
                
                // 保存打卡记录
                saveCheckinRecord();
                
                // 计算工作时长
                const morning = new Date(now.toDateString() + ' ' + checkinStatus.morningTime);
                const evening = new Date(now.toDateString() + ' ' + checkinStatus.eveningTime);
                const hoursWorked = ((evening - morning) / (1000 * 60 * 60)).toFixed(1);
                
                showNotification('下班打卡成功', `打卡时间: ${checkinStatus.eveningTime}，工作时长: ${hoursWorked}小时`, 'success');
            }
        }

        // 保存打卡记录
        function saveCheckinRecord() {
            const today = formatDate(new Date(), true);
            let record =打卡记录.find(r => r.date === today);
            
            if (!record) {
                record = {
                    date: today,
                    weekday: getWeekday(new Date()),
                    morning: checkinStatus.morningTime || '',
                    evening: checkinStatus.eveningTime || '',
                    status: checkinStatus.hasCheckedIn && checkinStatus.hasCheckedOut ? '正常' : 
                            checkinStatus.hasCheckedIn ? '已上班' : '未打卡'
                };
                打卡记录.push(record);
            } else {
                record.morning = checkinStatus.morningTime || record.morning;
                record.evening = checkinStatus.eveningTime || record.evening;
                record.status = checkinStatus.hasCheckedIn && checkinStatus.hasCheckedOut ? '正常' : 
                                checkinStatus.hasCheckedIn ? '已上班' : '未打卡';
            }
            
            // 保存到本地存储
            localStorage.setItem('checkinRecords_' + currentUser.id, JSON.stringify(打卡记录));
            
            // 更新记录列表
            updateRecentRecords();
            loadAllRecords();
            updateStats();
        }

        // 加载打卡数据
        function loadCheckinData() {
            // 更新用户名
            usernameDisplay.textContent = currentUser.name;
            
            // 从本地存储加载打卡记录
            const savedRecords = localStorage.getItem('checkinRecords_' + currentUser.id);
            if (savedRecords) {
                打卡记录 = JSON.parse(savedRecords);
            } else {
                // 生成一些模拟数据
                打卡记录 = generateMockRecords();
                localStorage.setItem('checkinRecords_' + currentUser.id, JSON.stringify(打卡记录));
            }
            
            // 检查今日打卡状态
            const today = formatDate(new Date(), true);
            const todayRecord =打卡记录.find(r => r.date === today);
            
            if (todayRecord) {
                checkinStatus.hasCheckedIn = !!todayRecord.morning;
                checkinStatus.hasCheckedOut = !!todayRecord.evening;
                checkinStatus.morningTime = todayRecord.morning;
                checkinStatus.eveningTime = todayRecord.evening;
                
                // 更新UI
                morningCheckin.textContent = checkinStatus.morningTime || '未打卡';
                eveningCheckout.textContent = checkinStatus.eveningTime || '未打卡';
                
                if (checkinStatus.hasCheckedIn) {
                    morningCheckin.classList.remove('text-gray-400');
                    morningCheckin.classList.add('text-success');
                }
                
                if (checkinStatus.hasCheckedOut) {
                    eveningCheckout.classList.remove('text-gray-400');
                    eveningCheckout.classList.add('text-success');
                }
            }
            
            // 更新按钮状态
            updateCheckinButtonStatus();
            
            // 更新记录列表
            updateRecentRecords();
            loadAllRecords();
            
            // 更新统计数据
            updateStats();
        }

        // 更新打卡按钮状态
        function updateCheckinButtonStatus() {
            if (checkinStatus.hasCheckedIn && checkinStatus.hasCheckedOut) {
                checkinBtnText.textContent = '今日已完成打卡';
                checkinBtn.disabled = true;
                checkinBtn.classList.remove('bg-success', 'bg-primary');
                checkinBtn.classList.add('bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.remove('btn-hover');
            } else if (checkinStatus.hasCheckedIn) {
                checkinBtnText.textContent = '下班打卡';
                checkinBtn.disabled = false;
                checkinBtn.classList.remove('bg-success', 'bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.add('bg-primary');
                checkinBtn.classList.add('btn-hover');
            } else {
                checkinBtnText.textContent = '上班打卡';
                checkinBtn.disabled = false;
                checkinBtn.classList.remove('bg-primary', 'bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.add('bg-success');
                checkinBtn.classList.add('btn-hover');
            }
        }

        // 更新最近打卡记录
        function updateRecentRecords() {
            // 清空列表
            recentRecordsEl.innerHTML = '';
            
            // 按日期排序，取最近5条
            const sortedRecords = [...打卡记录].sort((a, b) => new Date(b.date) - new Date(a.date)).slice(0, 5);
            
            if (sortedRecords.length === 0) {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td colspan="4" class="px-3 py-4 text-center text-gray-500">暂无打卡记录</td>
                `;
                recentRecordsEl.appendChild(row);
                return;
            }
            
            // 添加记录
            sortedRecords.forEach(record => {
                const row = document.createElement('tr');
                
                // 确定状态样式
                let statusClass = 'text-gray-500';
                let statusText = '未完成';
                
                if (record.morning && record.evening) {
                    statusClass = 'text-success';
                    statusText = '正常';
                } else if (record.morning) {
                    statusClass = 'text-warning';
                    statusText = '已上班';
                }
                
                row.innerHTML = `
                    <td class="px-3 py-4 whitespace-nowrap">${record.date}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.morning || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.evening || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap"><span class="${statusClass}">${statusText}</span></td>
                `;
                
                recentRecordsEl.appendChild(row);
            });
        }

        // 加载所有打卡记录
        function loadAllRecords() {
            // 清空列表
            allRecordsEl.innerHTML = '';
            
            // 获取选中的月份
            const selectedMonth = monthSelector.value;
            const [year, month] = selectedMonth.split('-');
            
            // 筛选当月记录
            const filteredRecords =打卡记录.filter(record => {
                const recordDate = new Date(record.date);
                return recordDate.getFullYear() === parseInt(year) && 
                       recordDate.getMonth() + 1 === parseInt(month);
            });
            
            // 更新计数
            showingCountEl.textContent = filteredRecords.length;
            totalCountEl.textContent = filteredRecords.length;
            
            if (filteredRecords.length === 0) {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td colspan="6" class="px-3 py-8 text-center text-gray-500">该月份暂无打卡记录</td>
                `;
                allRecordsEl.appendChild(row);
                return;
            }
            
            // 按日期排序
            filteredRecords.sort((a, b) => new Date(a.date) - new Date(b.date));
            
            // 添加记录
            filteredRecords.forEach(record => {
                const row = document.createElement('tr');
                
                // 计算工作时长
                let hoursWorked = '-';
                if (record.morning && record.evening) {
                    const dateStr = record.date;
                    const morning = new Date(`${dateStr} ${record.morning}`);
                    const evening = new Date(`${dateStr} ${record.evening}`);
                    const hours = ((evening - morning) / (1000 * 60 * 60)).toFixed(1);
                    hoursWorked = `${hours}小时`;
                }
                
                // 确定状态样式
                let statusClass = 'text-gray-500';
                let statusText = '未打卡';
                
                if (record.morning && record.evening) {
                    // 检查是否迟到 (假设9点后打卡为迟到)
                    const hour = parseInt(record.morning.split(':')[0]);
                    if (hour > 9) {
                        statusClass = 'text-warning';
                        statusText = '迟到';
                    } else {
                        statusClass = 'text-success';
                        statusText = '正常';
                    }
                } else if (record.morning) {
                    statusClass = 'text-blue-500';
                    statusText = '已上班';
                }
                
                row.innerHTML = `
                    <td class="px-3 py-4 whitespace-nowrap">${record.date}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.weekday}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.morning || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.evening || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${hoursWorked}</td>
                    <td class="px-3 py-4 whitespace-nowrap"><span class="${statusClass}">${statusText}</span></td>
                `;
                
                allRecordsEl.appendChild(row);
            });
        }

        // 更新统计数据
        function updateStats() {
            const today = new Date();
            const currentMonth = today.getMonth() + 1;
            const currentYear = today.getFullYear();
            
            // 筛选当月记录
            const monthlyRecords =打卡记录.filter(record => {
                const recordDate = new Date(record.date);
                return recordDate.getFullYear() === currentYear && 
                       recordDate.getMonth() + 1 === currentMonth;
            });
            
            // 计算当月工作日总数 (简化处理：按22天计算)
            const totalDays = 22;
            
            // 计算已打卡天数
            const checkedDays = monthlyRecords.filter(r => r.morning).length;
            
            // 计算迟到次数
            let lateCount = 0;
            monthlyRecords.forEach(r => {
                if (r.morning) {
                    const hour = parseInt(r.morning.split(':')[0]);
                    if (hour > 9) { // 假设9点后打卡为迟到
                        lateCount++;
                    }
                }
            });
            
            // 计算缺勤次数
            const workdaysInMonth = getWorkdaysInMonth(currentYear, currentMonth);
            const absentCount = Math.max(0, workdaysInMonth - checkedDays);
            
            // 更新UI
            document.getElementById('totalDays').textContent = totalDays;
            document.getElementById('checkedDays').textContent = checkedDays;
            document.getElementById('lateCount').textContent = lateCount;
            document.getElementById('absentCount').textContent = absentCount;
        }

        // 初始化月份选择器
        function initMonthSelector() {
            const today = new Date();
            const currentYear = today.getFullYear();
            const currentMonth = today.getMonth() + 1;
            
            // 生成最近6个月的选项
            for (let i = 0; i < 6; i++) {
                let month = currentMonth - i;
                let year = currentYear;
                
                if (month <= 0) {
                    month += 12;
                    year -= 1;
                }
                
                const monthStr = month.toString().padStart(2, '0');
                const option = document.createElement('option');
                option.value = `${year}-${monthStr}`;
                option.textContent = `${year}年${month}月`;
                
                // 设置当前月份为默认选中
                if (i === 0) {
                    option.selected = true;
                }
                
                monthSelector.appendChild(option);
            }
        }

        // 导出记录
        function exportRecords() {
            const selectedMonth = monthSelector.value;
            const [year, month] = selectedMonth.split('-');
            
            // 这里只是模拟导出功能
            showNotification('导出成功', `已将${year}年${month}月的打卡记录导出为Excel`, 'success');
        }

        // 显示通知
        function showNotification(title, message, type = 'info') {
            notificationTitle.textContent = title;
            notificationMessage.textContent = message;
            
            // 设置图标和颜色
            notificationIcon.innerHTML = '';
            if (type === 'success') {
                notificationIcon.innerHTML = '<i class="fa fa-check-circle text-success"></i>';
            } else if (type === 'error') {
                notificationIcon.innerHTML = '<i class="fa fa-exclamation-circle text-danger"></i>';
            } else if (type === 'warning') {
                notificationIcon.innerHTML = '<i class="fa fa-exclamation-triangle text-warning"></i>';
            } else {
                notificationIcon.innerHTML = '<i class="fa fa-info-circle text-primary"></i>';
            }
            
            // 显示通知
            notification.classList.remove('translate-y-20', 'opacity-0', 'pointer-events-none');
            
            // 3秒后自动隐藏
            setTimeout(hideNotification, 3000);
        }

        // 隐藏通知
        function hideNotification() {
            notification.classList.add('translate-y-20', 'opacity-0', 'pointer-events-none');
        }

        // 显示打卡界面
        function showCheckinSection() {
            loginSection.classList.add('hidden');
            checkinSection.classList.remove('hidden');
            recordsSection.classList.add('hidden');
        }

        // 显示记录界面
        function showRecordsSection() {
            checkinSection.classList.add('hidden');
            recordsSection.classList.remove('hidden');
            loadAllRecords();
        }

        // 更新日期和时间
        function updateDateTime() {
            const now = new Date();
            currentDateEl.textContent = formatDate(now);
            currentTimeEl.textContent = formatTime(now);
        }

        // 获取位置
        function getLocation() {
            if (navigator.geolocation) {
                locationStatus.innerHTML = '<i class="fa fa-spinner fa-spin mr-1"></i>获取位置中...';
                
                navigator.geolocation.getCurrentPosition(
                    (position) => {
                        currentLocation = {
                            latitude: position.coords.latitude,
                            longitude: position.coords.longitude
                        };
                        locationStatus.innerHTML = '<i class="fa fa-check-circle text-success mr-1"></i>位置已获取';
                    },
                    (error) => {
                        console.error('获取位置失败:', error);
                        currentLocation = null;
                        
                        switch(error.code) {
                            case error.PERMISSION_DENIED:
                                locationStatus.innerHTML = '<i class="fa fa-exclamation-circle text-warning mr-1"></i>请允许位置权限';
                                break;
                            case error.POSITION_UNAVAILABLE:
                                locationStatus.innerHTML = '<i class="fa fa-exclamation-circle text-warning mr-1"></i>位置信息不可用';
                                break;
                            case error.TIMEOUT:
                                locationStatus.innerHTML = '<i class="fa fa-exclamation-circle text-warning mr-1"></i>获取位置超时';
                                break;
                            default:
                                locationStatus.innerHTML = '<i class="fa fa-exclamation-circle text-warning mr-1"></i>获取位置失败';
                        }
                    },
                    { timeout: 10000 }
                );
            } else {
                locationStatus.innerHTML = '<i class="fa fa-exclamation-circle text-warning mr-1"></i>浏览器不支持位置服务';
            }
        }

        // 格式化日期
        function formatDate(date, simple = false) {
            const year = date.getFullYear();
            const month = (date.getMonth() + 1).toString().padStart(2, '0');
            const day = date.getDate().toString().padStart(2, '0');
            
            if (simple) {
                return `${year}-${month}-${day}`;
            }
            
            const weekday = getWeekday(date);
            return `${year}年${month}月${day}日 ${weekday}`;
        }

        // 格式化时间
        function formatTime(date) {
            const hours = date.getHours().toString().padStart(2, '0');
            const minutes = date.getMinutes().toString().padStart(2, '0');
            const seconds = date.getSeconds().toString().padStart(2, '0');
            return `${hours}:${minutes}:${seconds}`;
        }

        // 获取星期几
        function getWeekday(date) {
            const weekdays = ['周日', '周一', '周二', '周三', '周四', '周五', '周六'];
            return weekdays[date.getDay()];
        }

        // 计算当月工作日数量 (简化版)
        function getWorkdaysInMonth(year, month) {
            // 实际应用中应考虑节假日，这里简化处理
            const daysInMonth = new Date(year, month, 0).getDate();
            let workdays = 0;
            
            for (let i = 1; i <= daysInMonth; i++) {
                const day = new Date(year, month - 1, i).getDay();
                if (day !== 0 && day !== 6) { // 排除周六和周日
                    workdays++;
                }
            }
            
            return workdays;
        }

        // 生成模拟打卡记录
        function generateMockRecords() {
            const records = [];
            const today = new Date();
            
            // 生成过去15天的记录
            for (let i = 15; i >= 0; i--) {
                const date = new Date();
                date.setDate(today.getDate() - i);
                
                // 跳过周末
                const day = date.getDay();
                if (day === 0 || day === 6) continue;
                
                const isToday = i === 0;
                const isYesterday = i === 1;
                
                // 随机生成打卡时间
                let morning = '';
                let evening = '';
                
                // 今天可能还没打卡
                if (!isToday) {
                    // 70%的概率正常打卡
                    if (Math.random() > 0.3) {
                        // 早上8:30-9:30之间
                        const morningHour = 8 + Math.floor(Math.random() * 2);
                        const morningMinute = Math.floor(Math.random() * 60);
                        morning = `${morningHour.toString().padStart(2, '0')}:${morningMinute.toString().padStart(2, '0')}:00`;
                        
                        // 下午17:30-18:30之间
                        const eveningHour = 17 + Math.floor(Math.random() * 2);
                        const eveningMinute = Math.floor(Math.random() * 60);
                        evening = `${eveningHour.toString().padStart(2, '0')}:${eveningMinute.toString().padStart(2, '0')}:00`;
                    } else if (Math.random() > 0.5) {
                        // 只打了上班卡
                        const morningHour = 8 + Math.floor(Math.random() * 2);
                        const morningMinute = Math.floor(Math.random() * 60);
                        morning = `${morningHour.toString().padStart(2, '0')}:${morningMinute.toString().padStart(2, '0')}:00`;
                    }
                }
                
                records.push({
                    date: formatDate(date, true),
                    weekday: getWeekday(date),
                    morning,
                    evening,
                    status: morning && evening ? '正常' : morning ? '已上班' : '未打卡'
                });
            }
            
            return records;
        }
    </script>
</body>
</html>
