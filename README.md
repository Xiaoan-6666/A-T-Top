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
                    <span id="userRole" class="text-xs px-2 py-0.5 rounded-full bg-primary/10 text-primary hidden">管理员</span>
                </div>
                <!-- 管理员特有按钮 -->
                <button id="adminDashboardBtn" class="hidden text-gray-600 hover:text-primary transition-colors">
                    <i class="fa fa-tachometer"></i>
                    <span class="ml-1 hidden md:inline">管理面板</span>
                </button>
                <button id="logoutBtn" class="text-gray-600 hover:text-danger transition-colors">
                    <i class="fa fa-sign-out"></i>
                    <span class="ml-1 hidden md:inline">退出</span>
                </button>
            </div>
        </div>
    </header>

    <!-- 主内容区 -->
    <main class="container mx-auto px-4 pt-24 pb-16">
        <!-- 登录界面 - 固定显示在首页 -->
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
                                placeholder="例如：250001 或 admin" required>
                            <!-- 工号匹配的姓名预览 -->
                            <p id="employeeNamePreview" class="absolute left-10 top-full mt-1 text-xs text-success hidden"></p>
                        </div>
                        <!-- 工号不存在提示 -->
                        <p id="employeeIdError" class="mt-1 text-xs text-danger hidden">该工号不存在，请核对后重新输入</p>
                    </div>
                    
                    <div>
                        <label for="password" class="block text-sm font-medium text-gray-700 mb-1">密码</label>
                        <div class="relative">
                            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                <i class="fa fa-lock text-gray-400"></i>
                            </div>
                            <input type="password" id="password" name="password" 
                                class="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                placeholder="默认密码：123456" required>
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
                        
                        <!-- 时间规则提示 -->
                        <div class="bg-blue-50 p-3 rounded-lg mb-4 text-sm text-gray-700">
                            <p class="font-medium"><i class="fa fa-info-circle text-primary mr-1"></i>打卡规则</p>
                            <p>• 上班：8:30前正常，8:00-8:30迟到，9:00后扣半天工资</p>
                            <p>• 下班：17:00后正常，17:00前早退</p>
                        </div>
                        
                        <div class="text-center py-4 border-y border-gray-100">
                            <p id="currentDate" class="text-gray-500 mb-1"></p>
                            <p id="currentTime" class="text-2xl font-mono font-bold text-gray-800"></p>
                        </div>
                        
                        <div class="mt-6 space-y-3">
                            <div class="flex justify-between items-center">
                                <span class="text-gray-600">上班打卡</span>
                                <div>
                                    <span id="morningCheckin" class="text-gray-400">未打卡</span>
                                    <!-- 上班备注显示 -->
                                    <p id="morningRemark" class="text-xs mt-1 text-danger hidden"></p>
                                </div>
                            </div>
                            <div class="flex justify-between items-center">
                                <span class="text-gray-600">下班打卡</span>
                                <div>
                                    <span id="eveningCheckout" class="text-gray-400">未打卡</span>
                                    <!-- 下班备注显示 -->
                                    <p id="eveningRemark" class="text-xs mt-1 text-danger hidden"></p>
                                </div>
                            </div>
                        </div>
                        
                        <!-- 员工自定义备注输入框 -->
                        <div class="mt-6">
                            <label for="userRemark" class="block text-sm font-medium text-gray-700 mb-1">
                                <i class="fa fa-pencil text-primary mr-1"></i>打卡备注
                            </label>
                            <input type="text" id="userRemark" 
                                class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all text-sm"
                                placeholder="请输入备注（如：外勤、请假、加班等）">
                        </div>
                        
                        <!-- 打卡按钮 -->
                        <div class="mt-4">
                            <button id="checkinBtn" class="w-full bg-success text-white py-3 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                                <i class="fa fa-check mr-2"></i>
                                <span id="checkinBtnText">上班打卡</span>
                            </button>
                        </div>
                        
                        <div class="mt-4 text-center text-sm text-gray-500">
                            <p id="checkinTip">点击按钮完成上班签到</p>
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
                                <p id="totalDays" class="text-2xl font-bold text-primary mt-1">31</p>
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
                                <p class="text-gray-500 text-sm">扣薪次数</p>
                                <p id="salaryDeductCount" class="text-2xl font-bold text-danger mt-1">0</p>
                            </div>
                        </div>
                    </div>
                    
                    <!-- 最近打卡记录（含备注列） -->
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
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">员工姓名</th>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日期</th>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">上班时间</th>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">下班时间</th>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                                        <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">备注</th>
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

        <!-- 个人打卡记录查询界面 (含备注列) -->
        <section id="personalRecordsSection" class="hidden max-w-4xl mx-auto animate-fadeIn">
            <div class="bg-white rounded-xl p-6 card-shadow mb-6">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6">
                    <h2 class="text-xl font-bold text-gray-800 mb-4 md:mb-0 flex items-center">
                        <i class="fa fa-list-alt text-primary mr-2"></i>个人打卡记录查询
                    </h2>
                    
                    <div class="flex flex-col sm:flex-row w-full md:w-auto gap-3">
                        <div class="relative w-full sm:w-auto">
                            <select id="personalMonthSelector" class="block w-full sm:w-48 pl-3 pr-10 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary appearance-none">
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
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">员工姓名</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日期</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">星期</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">上班时间</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">下班时间</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">工作时长</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">备注</th>
                            </tr>
                        </thead>
                        <tbody id="personalAllRecords" class="bg-white divide-y divide-gray-200">
                            <!-- 记录将通过JS动态生成 -->
                        </tbody>
                    </table>
                </div>
                
                <div class="mt-6 flex justify-between items-center">
                    <div class="text-gray-600 text-sm">
                        显示 <span id="personalShowingCount">0</span> 条记录，共 <span id="personalTotalCount">0</span> 条
                    </div>
                    
                    <div class="flex space-x-2">
                        <button id="personalExportExcelBtn" class="bg-green-600 text-white py-2 px-4 rounded-lg font-medium btn-hover flex items-center">
                            <i class="fa fa-download mr-2"></i>导出Excel
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- 管理员面板 -->
        <section id="adminDashboardSection" class="hidden max-w-5xl mx-auto animate-fadeIn">
            <div class="bg-white rounded-xl p-6 card-shadow mb-6">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6">
                    <h2 class="text-xl font-bold text-gray-800 mb-4 md:mb-0 flex items-center">
                        <i class="fa fa-tachometer text-primary mr-2"></i>管理员控制面板
                    </h2>
                    
                    <div class="flex flex-col sm:flex-row w-full md:w-auto gap-3">
                        <button id="adminBackToCheckinBtn" class="bg-gray-100 text-gray-700 py-2 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                            <i class="fa fa-arrow-left mr-2"></i>返回首页
                        </button>
                    </div>
                </div>
                
                <!-- 管理员统计概览 -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                    <div class="p-4 bg-blue-50 rounded-lg">
                        <p class="text-gray-500 text-sm">员工总数</p>
                        <p id="adminTotalEmployees" class="text-2xl font-bold text-primary mt-1">0</p>
                    </div>
                    <div class="p-4 bg-green-50 rounded-lg">
                        <p class="text-gray-500 text-sm">本月总打卡次数</p>
                        <p id="adminTotalCheckins" class="text-2xl font-bold text-success mt-1">0</p>
                    </div>
                    <div class="p-4 bg-yellow-50 rounded-lg">
                        <p class="text-gray-500 text-sm">本月异常打卡</p>
                        <p id="adminAbnormalCheckins" class="text-2xl font-bold text-warning mt-1">0</p>
                    </div>
                </div>
            </div>
            
            <!-- 管理员记录查询 - 所有员工 -->
            <div class="bg-white rounded-xl p-6 card-shadow mb-6">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6">
                    <h2 class="text-xl font-bold text-gray-800 mb-4 md:mb-0 flex items-center">
                        <i class="fa fa-users text-primary mr-2"></i>所有员工打卡记录
                    </h2>
                    
                    <div class="flex flex-col sm:flex-row w-full md:w-auto gap-3">
                        <div class="relative w-full sm:w-auto">
                            <select id="adminMonthSelector" class="block w-full sm:w-48 pl-3 pr-10 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary appearance-none">
                                <!-- 月份选项将通过JS动态生成 -->
                            </select>
                            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                                <i class="fa fa-chevron-down text-xs"></i>
                            </div>
                        </div>
                        
                        <div class="relative w-full sm:w-auto">
                            <select id="adminEmployeeFilter" class="block w-full sm:w-64 pl-3 pr-10 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary appearance-none">
                                <option value="all">所有员工</option>
                                <!-- 员工选项将通过JS动态生成 -->
                            </select>
                            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                                <i class="fa fa-chevron-down text-xs"></i>
                            </div>
                        </div>
                        
                        <button id="adminExportAllBtn" class="bg-green-600 text-white py-2 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                            <i class="fa fa-download mr-2"></i>导出全部
                        </button>
                    </div>
                </div>
                
                <div class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead>
                            <tr>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">工号</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">员工姓名</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日期</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">星期</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">上班时间</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">下班时间</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">工作时长</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">备注</th>
                                <th class="px-3 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">操作</th>
                            </tr>
                        </thead>
                        <tbody id="adminAllRecords" class="bg-white divide-y divide-gray-200">
                            <!-- 记录将通过JS动态生成 -->
                        </tbody>
                    </table>
                </div>
                
                <div class="mt-6 flex justify-between items-center">
                    <div class="text-gray-600 text-sm">
                        显示 <span id="adminShowingCount">0</span> 条记录，共 <span id="adminTotalCount">0</span> 条
                    </div>
                </div>
            </div>
        </section>
    </main>

    <!-- 编辑打卡记录的模态框 -->
    <div id="editRecordModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 hidden">
        <div class="bg-white rounded-xl p-6 w-full max-w-md mx-4 animate-fadeIn">
            <div class="flex justify-between items-center mb-4">
                <h3 class="text-lg font-bold text-gray-800">编辑打卡记录</h3>
                <button id="closeEditModal" class="text-gray-400 hover:text-gray-600">
                    <i class="fa fa-times"></i>
                </button>
            </div>
            
            <form id="editRecordForm">
                <input type="hidden" id="editRecordIndex">
                
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">员工</label>
                    <p id="editEmployeeName" class="text-gray-800"></p>
                </div>
                
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1">日期</label>
                    <p id="editRecordDate" class="text-gray-800"></p>
                </div>
                
                <div class="grid grid-cols-2 gap-4 mb-4">
                    <div>
                        <label for="editMorningTime" class="block text-sm font-medium text-gray-700 mb-1">上班时间</label>
                        <input type="time" id="editMorningTime" class="w-full px-3 py-2 border border-gray-300 rounded-lg">
                    </div>
                    <div>
                        <label for="editEveningTime" class="block text-sm font-medium text-gray-700 mb-1">下班时间</label>
                        <input type="time" id="editEveningTime" class="w-full px-3 py-2 border border-gray-300 rounded-lg">
                    </div>
                </div>
                
                <div class="mb-4">
                    <label for="editRemark" class="block text-sm font-medium text-gray-700 mb-1">备注</label>
                    <textarea id="editRemark" rows="2" class="w-full px-3 py-2 border border-gray-300 rounded-lg"></textarea>
                </div>
                
                <div class="mt-6 flex space-x-3">
                    <button type="button" id="cancelEditBtn" class="flex-1 bg-gray-100 text-gray-700 py-2 px-4 rounded-lg font-medium">
                        取消
                    </button>
                    <button type="submit" class="flex-1 bg-primary text-white py-2 px-4 rounded-lg font-medium">
                        保存修改
                    </button>
                </div>
            </form>
        </div>
    </div>

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

    <!-- JavaScript：核心逻辑 -->
    <script>
        // 管理员账号信息
        const adminAccount = {
            id: "admin",
            name: "管理员",
            password: "admin123",
            role: "admin"
        };

        // 更新为用户提供的员工数据
        const employees = [
            { id: "250001", name: "Win Phyu Phyu Lwin" },
            { id: "250004", name: "Naw Htoo Phwel Phaw" },
            { id: "250007", name: "Hein Tun Soe" },
            { id: "250009", name: "Phyo Thiha Kyaw" },
            { id: "250010", name: "Satt Wai" },
            { id: "250019", name: "Chit Su Wai" },
            { id: "250029", name: "Chit Phoo Wai" },
            { id: "250032", name: "Myat Thiri Hlaing" },
            { id: "250038", name: "Zwe Man Hein" },
            { id: "250039", name: "Kaung Lwin Han" },
            { id: "250041", name: "Min Khant Kyaw" },
            { id: "250042", name: "Win Nandar" },
            { id: "250043", name: "Ye Htet" },
            { id: "250045", name: "Han Lwin Oo" },
            { id: "250047", name: "Myo Thu Aung" },
            { id: "250048", name: "Ye Yint" },
            { id: "250049", name: "Yarzar Tun" },
            { id: "250050", name: "Zwe Thiha" },
            { id: "250051", name: "Thein Zaw" },
            { id: "250056", name: "Aye Chan Nyi Nyi" },
            { id: "250058", name: "Pan Pwint Phoo" },
            { id: "250061", name: "Thiha San" },
            { id: "250064", name: "Aung Thet Paing" },
            { id: "250067", name: "Mei Wadi Tun" },
            { id: "250069", name: "Zin May Oo" },
            { id: "250070", name: "Thel Thinzar Nwe" },
            { id: "250071", name: "Aung Kaung Myat" },
            { id: "250072", name: "Ngwe Kyal Sin Thant" },
            { id: "250073", name: "Aung Soe Moe" },
            { id: "250075", name: "Hein Htet Aung" },
            { id: "250077", name: "Hnin Ei Shwe Yee" },
            { id: "250090", name: "Saint Nadi Tun" },
            { id: "250092", name: "Thit Aung Khant" },
            { id: "250093", name: "Thu Htoo Zaw" },
            { id: "250095", name: "Shwe Phoo" },
            { id: "250096", name: "Thura Myo Satt" },
            { id: "250097", name: "Naing Naing Moe Oo" },
            { id: "250098", name: "Kyaw Min Htet" },
            { id: "250099", name: "Pyae Pyae Thein" },
            { id: "250100", name: "Win Kay Khaing" },
            { id: "250101", name: "Cho Yun Nwe" },
            { id: "250103", name: "Ye Lin Aung" },
            { id: "250104", name: "Ye Lin Phyo" },
            { id: "250105", name: "Tayza Htun" },
            { id: "250106", name: "Phyu Phyu Zaw" },
            { id: "250107", name: "Phyo Phyo Nwe" },
            { id: "250108", name: "Yamin Oo" },
            { id: "250109", name: "Zin lin Tun" },
            { id: "250110", name: "Yoon Nadi Aung" },
            { id: "250112", name: "Thet Oo Aung" },
            { id: "250113", name: "Phyo Wint Kyaw" },
            { id: "250114", name: "May Phoo Eaindra" },
            { id: "250115", name: "ZarNi Min Htet" },
            { id: "250116", name: "Pyae Phyo Aung" },
            { id: "250117", name: "Ye Lin Htut" },
            { id: "250118", name: "Zaw Lin Htoo" }
        ];

        // 全局变量
        let currentUser = null;
        let checkinStatus = {
            hasCheckedIn: false,
            hasCheckedOut: false,
            morningTime: null,
            eveningTime: null,
            morningRemark: "", // 时间规则备注（迟到/扣薪等）
            eveningRemark: ""  // 时间规则备注（早退等）
        };
        let 打卡记录 = [];

        // DOM元素
        const loginSection = document.getElementById('loginSection');
        const checkinSection = document.getElementById('checkinSection');
        const personalRecordsSection = document.getElementById('personalRecordsSection');
        const adminDashboardSection = document.getElementById('adminDashboardSection');
        const loginForm = document.getElementById('loginForm');
        const togglePassword = document.getElementById('togglePassword');
        const passwordInput = document.getElementById('password');
        const employeeIdInput = document.getElementById('employeeId');
        const employeeNamePreview = document.getElementById('employeeNamePreview');
        const employeeIdError = document.getElementById('employeeIdError');
        const usernameDisplay = document.getElementById('usernameDisplay');
        const userRole = document.getElementById('userRole');
        const logoutBtn = document.getElementById('logoutBtn');
        const adminDashboardBtn = document.getElementById('adminDashboardBtn');
        const checkinBtn = document.getElementById('checkinBtn');
        const checkinBtnText = document.getElementById('checkinBtnText');
        const morningCheckin = document.getElementById('morningCheckin');
        const eveningCheckout = document.getElementById('eveningCheckout');
        const morningRemark = document.getElementById('morningRemark');
        const eveningRemark = document.getElementById('eveningRemark');
        const userRemarkInput = document.getElementById('userRemark'); // 员工自定义备注
        const currentDateEl = document.getElementById('currentDate');
        const currentTimeEl = document.getElementById('currentTime');
        const checkinTip = document.getElementById('checkinTip');
        const viewAllRecordsBtn = document.getElementById('viewAllRecordsBtn');
        const backToCheckinBtn = document.getElementById('backToCheckinBtn');
        const adminBackToCheckinBtn = document.getElementById('adminBackToCheckinBtn');
        const recentRecordsEl = document.getElementById('recentRecords');
        
        // 个人记录相关元素
        const personalAllRecordsEl = document.getElementById('personalAllRecords');
        const personalMonthSelector = document.getElementById('personalMonthSelector');
        const personalShowingCountEl = document.getElementById('personalShowingCount');
        const personalTotalCountEl = document.getElementById('personalTotalCount');
        const personalExportExcelBtn = document.getElementById('personalExportExcelBtn');
        
        // 管理员相关元素
        const adminAllRecordsEl = document.getElementById('adminAllRecords');
        const adminMonthSelector = document.getElementById('adminMonthSelector');
        const adminEmployeeFilter = document.getElementById('adminEmployeeFilter');
        const adminShowingCountEl = document.getElementById('adminShowingCount');
        const adminTotalCountEl = document.getElementById('adminTotalCount');
        const adminExportAllBtn = document.getElementById('adminExportAllBtn');
        const adminTotalEmployeesEl = document.getElementById('adminTotalEmployees');
        const adminTotalCheckinsEl = document.getElementById('adminTotalCheckins');
        const adminAbnormalCheckinsEl = document.getElementById('adminAbnormalCheckins');

        // 编辑模态框相关元素
        const editRecordModal = document.getElementById('editRecordModal');
        const closeEditModal = document.getElementById('closeEditModal');
        const cancelEditBtn = document.getElementById('cancelEditBtn');
        const editRecordForm = document.getElementById('editRecordForm');
        const editRecordIndex = document.getElementById('editRecordIndex');
        const editEmployeeName = document.getElementById('editEmployeeName');
        const editRecordDate = document.getElementById('editRecordDate');
        const editMorningTime = document.getElementById('editMorningTime');
        const editEveningTime = document.getElementById('editEveningTime');
        const editRemark = document.getElementById('editRemark');

        // 通知相关元素
        const notification = document.getElementById('notification');
        const notificationTitle = document.getElementById('notificationTitle');
        const notificationMessage = document.getElementById('notificationMessage');
        const notificationIcon = document.getElementById('notificationIcon');
        const closeNotification = document.getElementById('closeNotification');
        const navbar = document.getElementById('navbar');
        
        // 统计元素
        const lateCountEl = document.getElementById('lateCount');
        const salaryDeductCountEl = document.getElementById('salaryDeductCount');

        // 初始化
        document.addEventListener('DOMContentLoaded', () => {
            // 更新时间
            updateDateTime();
            setInterval(updateDateTime, 1000);
            
            // 初始化月份选择器
            initMonthSelector(personalMonthSelector);
            initMonthSelector(adminMonthSelector);
            
            // 初始化管理员员工筛选器
            initEmployeeFilter();
            
            // 工号输入实时匹配姓名
            employeeIdInput.addEventListener('input', handleEmployeeIdInput);
            
            // 加载本地存储的打卡记录
            const savedRecords = localStorage.getItem('checkinRecords');
            if (savedRecords) {
                打卡记录 = JSON.parse(savedRecords);
            } else {
                打卡记录 = [];
            }
            
            // 加载本地存储的用户信息
            const savedUser = localStorage.getItem('currentUser');
            if (savedUser) {
                currentUser = JSON.parse(savedUser);
                // 匹配员工姓名
                if (currentUser.role !== 'admin') {
                    const employee = employees.find(emp => emp.id === currentUser.id);
                    if (employee) currentUser.name = employee.name;
                }
                showCheckinSection();
                loadCheckinData();
            } else {
                // 确保未登录时显示登录页
                loginSection.classList.remove('hidden');
                checkinSection.classList.add('hidden');
                personalRecordsSection.classList.add('hidden');
                adminDashboardSection.classList.add('hidden');
            }
            
            // 事件监听
            loginForm.addEventListener('submit', handleLogin);
            togglePassword.addEventListener('click', togglePasswordVisibility);
            logoutBtn.addEventListener('click', handleLogout);
            checkinBtn.addEventListener('click', handleCheckin);
            viewAllRecordsBtn.addEventListener('click', showPersonalRecordsSection);
            backToCheckinBtn.addEventListener('click', showCheckinSection);
            adminBackToCheckinBtn.addEventListener('click', showCheckinSection);
            adminDashboardBtn.addEventListener('click', showAdminDashboardSection);
            personalMonthSelector.addEventListener('change', loadPersonalAllRecords);
            adminMonthSelector.addEventListener('change', loadAdminAllRecords);
            adminEmployeeFilter.addEventListener('change', loadAdminAllRecords);
            personalExportExcelBtn.addEventListener('click', exportPersonalRecords);
            adminExportAllBtn.addEventListener('click', exportAdminRecords);
            closeNotification.addEventListener('click', hideNotification);
            
            // 编辑记录相关事件
            closeEditModal.addEventListener('click', () => editRecordModal.classList.add('hidden'));
            cancelEditBtn.addEventListener('click', () => editRecordModal.classList.add('hidden'));
            editRecordForm.addEventListener('submit', handleSaveEdit);
            
            // 滚动导航效果
            window.addEventListener('scroll', () => {
                if (window.scrollY > 10) {
                    navbar.classList.add('shadow');
                } else {
                    navbar.classList.remove('shadow');
                }
            });
        });

        // 初始化员工筛选器（管理员用）
        function initEmployeeFilter() {
            // 清空现有选项（保留"所有员工"）
            while (adminEmployeeFilter.options.length > 1) {
                adminEmployeeFilter.remove(1);
            }
            
            // 添加员工选项，按工号排序
            employees.sort((a, b) => a.id.localeCompare(b.id)).forEach(emp => {
                const option = document.createElement('option');
                option.value = emp.id;
                option.textContent = `${emp.id} - ${emp.name}`;
                adminEmployeeFilter.appendChild(option);
            });
            
            // 更新员工总数统计
            adminTotalEmployeesEl.textContent = employees.length;
        }

        // 初始化月份选择器
        function initMonthSelector(selectorElement) {
            const today = new Date();
            const currentYear = today.getFullYear();
            const currentMonth = today.getMonth() + 1;
            
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
                if (i === 0) option.selected = true;
                selectorElement.appendChild(option);
            }
        }

        // 工号输入实时匹配姓名
        function handleEmployeeIdInput() {
            const inputId = employeeIdInput.value.trim();
            if (!inputId) {
                employeeNamePreview.classList.add('hidden');
                employeeIdError.classList.add('hidden');
                return;
            }
            
            // 检查是否为管理员账号
            if (inputId === adminAccount.id) {
                employeeNamePreview.textContent = `管理员账号`;
                employeeNamePreview.classList.remove('hidden');
                employeeIdError.classList.add('hidden');
                return;
            }
            
            // 匹配员工
            const matchedEmployee = employees.find(emp => emp.id === inputId);
            if (matchedEmployee) {
                // 显示匹配的姓名
                employeeNamePreview.textContent = `匹配员工：${matchedEmployee.name}`;
                employeeNamePreview.classList.remove('hidden');
                employeeIdError.classList.add('hidden');
            } else {
                // 提示工号不存在
                employeeNamePreview.classList.add('hidden');
                employeeIdError.classList.remove('hidden');
            }
        }

        // 登录处理
        function handleLogin(e) {
            e.preventDefault();
            const inputId = employeeIdInput.value.trim();
            const password = passwordInput.value;
            
            // 检查是否为管理员登录
            if (inputId === adminAccount.id) {
                if (password === adminAccount.password) {
                    currentUser = {
                        id: adminAccount.id,
                        name: adminAccount.name,
                        role: adminAccount.role
                    };
                    localStorage.setItem('currentUser', JSON.stringify(currentUser));
                    showCheckinSection();
                    updateAdminStats();
                    showNotification('登录成功', `欢迎回来，管理员`, 'success');
                    loginForm.reset();
                    return;
                } else {
                    showNotification('登录失败', '管理员密码错误', 'error');
                    return;
                }
            }
            
            // 普通员工登录
            const matchedEmployee = employees.find(emp => emp.id === inputId);
            if (!matchedEmployee) {
                employeeIdError.classList.remove('hidden');
                showNotification('登录失败', '该工号不存在，请核对后重新输入', 'error');
                return;
            }
            
            // 验证密码（默认密码123456）
            if (password && password === '123456') {
                currentUser = {
                    id: matchedEmployee.id,
                    name: matchedEmployee.name,
                    role: 'employee'
                };
                localStorage.setItem('currentUser', JSON.stringify(currentUser));
                showCheckinSection();
                loadCheckinData();
                showNotification('登录成功', `欢迎回来，${matchedEmployee.name}`, 'success');
                loginForm.reset();
                employeeNamePreview.classList.add('hidden');
                employeeIdError.classList.add('hidden');
            } else {
                showNotification('登录失败', '密码错误（默认密码：123456）', 'error');
            }
        }

        // 切换密码可见性
        function togglePasswordVisibility() {
            const type = passwordInput.getAttribute('type') === 'password' ? 'text' : 'password';
            passwordInput.setAttribute('type', type);
            const icon = togglePassword.querySelector('i');
            icon.classList.toggle('fa-eye-slash');
            icon.classList.toggle('fa-eye');
        }

        // 登出处理
        function handleLogout() {
            localStorage.removeItem('currentUser');
            currentUser = null;
            // 登出后固定显示登录页
            loginSection.classList.remove('hidden');
            checkinSection.classList.add('hidden');
            personalRecordsSection.classList.add('hidden');
            adminDashboardSection.classList.add('hidden');
            showNotification('已退出登录', '您已成功退出系统', 'info');
        }

        // 打卡处理
        function handleCheckin() {
            if (!currentUser || currentUser.role === 'admin') {
                showNotification('操作失败', '管理员无需打卡', 'error');
                return;
            }
            
            const now = new Date();
            const hours = now.getHours();
            const minutes = now.getMinutes();
            const currentTimeStr = formatTime(now);
            const userRemark = userRemarkInput.value.trim(); // 获取员工自定义备注

            // 上班打卡逻辑
            if (!checkinStatus.hasCheckedIn) {
                // 判断上班打卡状态和备注
                const { status: morningStatus, remark: morningRemarkText } = checkMorningCheckinStatus(hours, minutes);
                
                // 更新状态
                checkinStatus.hasCheckedIn = true;
                checkinStatus.morningTime = currentTimeStr;
                checkinStatus.morningRemark = morningRemarkText;
                
                // 更新UI
                morningCheckin.textContent = currentTimeStr;
                morningCheckin.classList.remove('text-gray-400');
                if (morningStatus === 'normal') {
                    morningCheckin.classList.add('text-success');
                    morningRemark.classList.add('hidden');
                } else {
                    morningCheckin.classList.add('text-warning');
                    morningRemark.textContent = morningRemarkText;
                    morningRemark.classList.remove('hidden');
                }
                
                // 切换按钮为下班打卡
                checkinBtnText.textContent = '下班打卡';
                checkinBtn.classList.remove('bg-success');
                checkinBtn.classList.add('bg-primary');
                checkinTip.textContent = '17:00后点击完成下班签退';
                
                // 保存记录（含员工备注）
                saveCheckinRecord(userRemark);
                
                // 通知（含时间备注和员工备注）
                const fullRemark = userRemark ? `${morningRemarkText} | 员工备注：${userRemark}` : morningRemarkText;
                showNotification(
                    '上班打卡成功', 
                    `签到时间: ${currentTimeStr} | ${fullRemark}`, 
                    morningStatus === 'normal' ? 'success' : 'warning'
                );
            } 
            // 下班打卡逻辑
            else if (!checkinStatus.hasCheckedOut) {
                // 判断下班打卡状态和备注
                const { status: eveningStatus, remark: eveningRemarkText } = checkEveningCheckoutStatus(hours, minutes);
                
                // 更新状态
                checkinStatus.hasCheckedOut = true;
                checkinStatus.eveningTime = currentTimeStr;
                checkinStatus.eveningRemark = eveningRemarkText;
                
                // 更新UI
                eveningCheckout.textContent = currentTimeStr;
                eveningCheckout.classList.remove('text-gray-400');
                if (eveningStatus === 'normal') {
                    eveningCheckout.classList.add('text-success');
                    eveningRemark.classList.add('hidden');
                } else {
                    eveningCheckout.classList.add('text-danger');
                    eveningRemark.textContent = eveningRemarkText;
                    eveningRemark.classList.remove('hidden');
                }
                
                // 禁用按钮
                checkinBtnText.textContent = '今日已完成打卡';
                checkinBtn.disabled = true;
                checkinBtn.classList.remove('bg-primary');
                checkinBtn.classList.add('bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.remove('btn-hover');
                checkinTip.textContent = '今日打卡已完成，明天见！';
                
                // 保存记录（含员工备注）
                saveCheckinRecord(userRemark);
                
                // 计算工作时长
                const morning = new Date(now.toDateString() + ' ' + checkinStatus.morningTime);
                const evening = new Date(now.toDateString() + ' ' + checkinStatus.eveningTime);
                const hoursWorked = ((evening - morning) / (1000 * 60 * 60)).toFixed(1);
                
                // 通知（含所有备注）
                const fullRemark = userRemark ? `${eveningRemarkText} | 员工备注：${userRemark}` : eveningRemarkText;
                showNotification(
                    '下班打卡成功', 
                    `签退时间: ${currentTimeStr} | 工作时长: ${hoursWorked}小时 | ${fullRemark}`, 
                    eveningStatus === 'normal' ? 'success' : 'error'
                );
            }
        }

        // 辅助：判断上班打卡状态（8:30前正常，8:30-9:00迟到，9:00后扣半天工资）
        function checkMorningCheckinStatus(hours, minutes) {
            const totalMinutes = hours * 60 + minutes;
            const normalEnd = 8 * 60 + 30; // 8:30（正常截止）
            const lateEnd = 9 * 60;        // 9:00（扣薪截止）
            
            if (totalMinutes <= normalEnd) {
                return { status: 'normal', remark: '正常打卡' };
            } else if (totalMinutes <= lateEnd) {
                const lateMinutes = totalMinutes - normalEnd;
                return { status: 'late', remark: `迟到${lateMinutes}分钟` };
            } else {
                return { status: 'seriousLate', remark: '严重迟到，扣半天工资' };
            }
        }

        // 辅助：判断下班打卡状态（17:00后正常，之前早退）
        function checkEveningCheckoutStatus(hours, minutes) {
            const totalMinutes = hours * 60 + minutes;
            const normalStart = 17 * 60; // 17:00（正常开始）
            
            if (totalMinutes >= normalStart) {
                return { status: 'normal', remark: '正常打卡' };
            } else {
                const earlyMinutes = normalStart - totalMinutes;
                return { status: 'earlyLeave', remark: `早退${earlyMinutes}分钟` };
            }
        }

        // 保存打卡记录时包含员工备注
        function saveCheckinRecord(userRemark) {
            const today = formatDate(new Date(), true);
            let record = 打卡记录.find(r => r.date === today && r.employeeId === currentUser.id);
            
            // 合并备注：时间规则备注 + 员工自定义备注
            const timeRemark = `${checkinStatus.morningRemark || ''} ${checkinStatus.eveningRemark || ''}`.trim();
            const fullRemark = userRemark ? `${timeRemark ? timeRemark + ' | ' : ''}员工备注：${userRemark}` : timeRemark || '无';
            
            if (!record) {
                record = {
                    employeeId: currentUser.id,
                    employeeName: currentUser.name, // 记录员工姓名
                    date: today,
                    weekday: getWeekday(new Date()),
                    morning: checkinStatus.morningTime || '',
                    evening: checkinStatus.eveningTime || '',
                    remark: fullRemark, // 保存完整备注
                    status: getRecordStatus()
                };
                打卡记录.push(record);
            } else {
                record.morning = checkinStatus.morningTime || record.morning;
                record.evening = checkinStatus.eveningTime || record.evening;
                record.remark = fullRemark; // 更新完整备注
                record.status = getRecordStatus();
            }
            
            // 保存到本地存储
            localStorage.setItem('checkinRecords', JSON.stringify(打卡记录));
            
            // 更新列表和统计
            updateRecentRecords();
            loadPersonalAllRecords();
            updateStats();
            
            // 如果当前用户是管理员，更新管理员面板数据
            if (currentUser && currentUser.role === 'admin') {
                updateAdminStats();
                loadAdminAllRecords();
            }
        }

        // 辅助：获取打卡记录的整体状态
        function getRecordStatus() {
            if (checkinStatus.hasCheckedIn && checkinStatus.hasCheckedOut) {
                return checkinStatus.morningRemark.includes('扣半天工资') 
                    ? '严重迟到' 
                    : checkinStatus.eveningRemark.includes('早退') 
                        ? '早退' 
                        : checkinStatus.morningRemark.includes('迟到') 
                            ? '迟到' 
                            : '正常';
            } else if (checkinStatus.hasCheckedIn) {
                return '已上班';
            } else {
                return '未打卡';
            }
        }

        // 加载打卡数据（含员工姓名和备注）
        function loadCheckinData() {
            if (!currentUser) return;
            
            // 显示用户信息和角色
            usernameDisplay.textContent = currentUser.name;
            if (currentUser.role === 'admin') {
                userRole.classList.remove('hidden');
                adminDashboardBtn.classList.remove('hidden');
                checkinBtn.disabled = true;
                checkinBtnText.textContent = '管理员无需打卡';
                checkinBtn.classList.remove('bg-success', 'bg-primary');
                checkinBtn.classList.add('bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.remove('btn-hover');
            } else {
                userRole.classList.add('hidden');
                adminDashboardBtn.classList.add('hidden');
            }
            
            // 重置备注输入框
            userRemarkInput.value = '';
            
            // 非管理员加载个人打卡数据
            if (currentUser.role !== 'admin') {
                // 恢复今日状态
                const today = formatDate(new Date(), true);
                const todayRecord = 打卡记录.find(r => r.date === today && r.employeeId === currentUser.id);
                if (todayRecord) {
                    checkinStatus.hasCheckedIn = !!todayRecord.morning;
                    checkinStatus.hasCheckedOut = !!todayRecord.evening;
                    checkinStatus.morningTime = todayRecord.morning;
                    checkinStatus.eveningTime = todayRecord.evening;
                    
                    // 解析时间规则备注
                    if (todayRecord.remark.includes('迟到')) {
                        checkinStatus.morningRemark = todayRecord.remark.match(/迟到\d+分钟|严重迟到，扣半天工资/)[0];
                    }
                    if (todayRecord.remark.includes('早退')) {
                        checkinStatus.eveningRemark = todayRecord.remark.match(/早退\d+分钟/)[0];
                    }
                    
                    // 更新UI
                    morningCheckin.textContent = todayRecord.morning || '未打卡';
                    eveningCheckout.textContent = todayRecord.evening || '未打卡';
                    
                    // 上班状态和备注
                    if (todayRecord.morning) {
                        morningCheckin.classList.remove('text-gray-400');
                        if (checkinStatus.morningRemark) {
                            morningCheckin.classList.add('text-warning');
                            morningRemark.textContent = checkinStatus.morningRemark;
                            morningRemark.classList.remove('hidden');
                        } else {
                            morningCheckin.classList.add('text-success');
                        }
                    }
                    
                    // 下班状态和备注
                    if (todayRecord.evening) {
                        eveningCheckout.classList.remove('text-gray-400');
                        if (checkinStatus.eveningRemark) {
                            eveningCheckout.classList.add('text-danger');
                            eveningRemark.textContent = checkinStatus.eveningRemark;
                            eveningRemark.classList.remove('hidden');
                        } else {
                            eveningCheckout.classList.add('text-success');
                        }
                    }
                    
                    // 恢复员工备注
                    const userRemarkMatch = todayRecord.remark.match(/员工备注：(.+)/);
                    if (userRemarkMatch) {
                        userRemarkInput.value = userRemarkMatch[1];
                    }
                } else {
                    // 重置今日状态
                    checkinStatus = {
                        hasCheckedIn: false,
                        hasCheckedOut: false,
                        morningTime: null,
                        eveningTime: null,
                        morningRemark: "",
                        eveningRemark: ""
                    };
                    morningCheckin.textContent = '未打卡';
                    eveningCheckout.textContent = '未打卡';
                    morningCheckin.classList.add('text-gray-400');
                    eveningCheckout.classList.add('text-gray-400');
                    morningRemark.classList.add('hidden');
                    eveningRemark.classList.add('hidden');
                }
                
                // 更新按钮状态
                updateCheckinButtonStatus();
            }
            
            // 更新列表和统计
            updateRecentRecords();
            loadPersonalAllRecords();
            updateStats();
            
            // 管理员更新统计
            if (currentUser.role === 'admin') {
                updateAdminStats();
            }
        }

        // 更新打卡按钮状态
        function updateCheckinButtonStatus() {
            if (checkinStatus.hasCheckedIn && checkinStatus.hasCheckedOut) {
                checkinBtnText.textContent = '今日已完成打卡';
                checkinBtn.disabled = true;
                checkinBtn.classList.remove('bg-success', 'bg-primary');
                checkinBtn.classList.add('bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.remove('btn-hover');
                checkinTip.textContent = '今日打卡已完成，明天见！';
            } else if (checkinStatus.hasCheckedIn) {
                checkinBtnText.textContent = '下班打卡';
                checkinBtn.disabled = false;
                checkinBtn.classList.remove('bg-success', 'bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.add('bg-primary');
                checkinBtn.classList.add('btn-hover');
                checkinTip.textContent = '17:00后点击完成下班签退';
            } else {
                checkinBtnText.textContent = '上班打卡';
                checkinBtn.disabled = false;
                checkinBtn.classList.remove('bg-primary', 'bg-gray-400', 'cursor-not-allowed');
                checkinBtn.classList.add('bg-success');
                checkinBtn.classList.add('btn-hover');
                checkinTip.textContent = '点击按钮完成上班签到';
            }
        }

        // 最近记录显示员工姓名和备注
        function updateRecentRecords() {
            recentRecordsEl.innerHTML = '';
            if (!currentUser) return;
            
            let userRecords = [];
            
            // 管理员可以看到所有员工的最近记录
            if (currentUser.role === 'admin') {
                userRecords = [...打卡记录]
                    .sort((a, b) => new Date(b.date) - new Date(a.date))
                    .slice(0, 10); // 管理员可以看到更多记录
            } 
            // 普通员工只能看到自己的记录
            else {
                userRecords = [...打卡记录]
                    .filter(r => r.employeeId === currentUser.id)
                    .sort((a, b) => new Date(b.date) - new Date(a.date))
                    .slice(0, 5);
            }
            
            if (userRecords.length === 0) {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td colspan="6" class="px-3 py-4 text-center text-gray-500">暂无打卡记录</td>
                `;
                recentRecordsEl.appendChild(row);
                return;
            }
            
            userRecords.forEach(record => {
                const row = document.createElement('tr');
                // 状态样式
                let statusClass = 'text-gray-500';
                let statusText = '未完成';
                if (record.morning && record.evening) {
                    if (record.remark.includes('扣半天工资')) {
                        statusClass = 'text-danger';
                        statusText = '严重迟到';
                    } else if (record.remark.includes('早退')) {
                        statusClass = 'text-danger';
                        statusText = '早退';
                    } else if (record.remark.includes('迟到')) {
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
                    <td class="px-3 py-4 whitespace-nowrap">${record.employeeName}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.date}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.morning || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.evening || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap"><span class="${statusClass}">${statusText}</span></td>
                    <td class="px-3 py-4 whitespace-nowrap text-sm ${record.remark.includes('扣') || record.remark.includes('早退') ? 'text-danger' : record.remark.includes('迟到') ? 'text-warning' : 'text-gray-600'}">
                        ${record.remark}
                    </td>
                `;
                recentRecordsEl.appendChild(row);
            });
        }

        // 个人历史记录显示
        function loadPersonalAllRecords() {
            personalAllRecordsEl.innerHTML = '';
            if (!currentUser || currentUser.role === 'admin') return;
            
            const selectedMonth = personalMonthSelector.value;
            const [year, month] = selectedMonth.split('-');
            // 筛选当前员工当月的记录
            const userMonthlyRecords = [...打卡记录]
                .filter(r => {
                    if (r.employeeId !== currentUser.id) return false;
                    const recordDate = new Date(r.date);
                    return recordDate.getFullYear() === parseInt(year) && 
                           recordDate.getMonth() + 1 === parseInt(month);
                })
                .sort((a, b) => new Date(a.date) - new Date(b.date));
            
            personalShowingCountEl.textContent = userMonthlyRecords.length;
            personalTotalCountEl.textContent = userMonthlyRecords.length;
            
            if (userMonthlyRecords.length === 0) {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td colspan="8" class="px-3 py-8 text-center text-gray-500">该月份暂无打卡记录</td>
                `;
                personalAllRecordsEl.appendChild(row);
                return;
            }
            
            userMonthlyRecords.forEach(record => {
                const row = document.createElement('tr');
                // 工作时长
                let hoursWorked = '-';
                if (record.morning && record.evening) {
                    const dateStr = record.date;
                    const morning = new Date(`${dateStr} ${record.morning}`);
                    const evening = new Date(`${dateStr} ${record.evening}`);
                    const hours = ((evening - morning) / (1000 * 60 * 60)).toFixed(1);
                    hoursWorked = `${hours}小时`;
                }
                
                // 状态样式
                let statusClass = 'text-gray-500';
                let statusText = '未打卡';
                if (record.morning && record.evening) {
                    if (record.remark.includes('扣半天工资')) {
                        statusClass = 'text-danger';
                        statusText = '严重迟到';
                    } else if (record.remark.includes('早退')) {
                        statusClass = 'text-danger';
                        statusText = '早退';
                    } else if (record.remark.includes('迟到')) {
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
                    <td class="px-3 py-4 whitespace-nowrap">${record.employeeName}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.date}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.weekday}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.morning || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.evening || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${hoursWorked}</td>
                    <td class="px-3 py-4 whitespace-nowrap"><span class="${statusClass}">${statusText}</span></td>
                    <td class="px-3 py-4 whitespace-nowrap text-sm ${record.remark.includes('扣') || record.remark.includes('早退') ? 'text-danger' : record.remark.includes('迟到') ? 'text-warning' : 'text-gray-600'}">
                        ${record.remark}
                    </td>
                `;
                personalAllRecordsEl.appendChild(row);
            });
        }

        // 管理员所有员工记录显示 - 增加编辑功能
        function loadAdminAllRecords() {
            adminAllRecordsEl.innerHTML = '';
            if (!currentUser || currentUser.role !== 'admin') return;
            
            const selectedMonth = adminMonthSelector.value;
            const selectedEmployee = adminEmployeeFilter.value;
            const [year, month] = selectedMonth.split('-');
            
            // 筛选记录
            let filteredRecords = [...打卡记录]
                .filter(r => {
                    // 员工筛选
                    if (selectedEmployee !== 'all' && r.employeeId !== selectedEmployee) {
                        return false;
                    }
                    
                    // 月份筛选
                    const recordDate = new Date(r.date);
                    return recordDate.getFullYear() === parseInt(year) && 
                           recordDate.getMonth() + 1 === parseInt(month);
                })
                .sort((a, b) => {
                    // 先按员工ID排序，再按日期排序
                    if (a.employeeId !== b.employeeId) {
                        return a.employeeId.localeCompare(b.employeeId);
                    }
                    return new Date(a.date) - new Date(b.date);
                });
            
            adminShowingCountEl.textContent = filteredRecords.length;
            adminTotalCountEl.textContent = filteredRecords.length;
            
            if (filteredRecords.length === 0) {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td colspan="10" class="px-3 py-8 text-center text-gray-500">该月份暂无打卡记录</td>
                `;
                adminAllRecordsEl.appendChild(row);
                return;
            }
            
            filteredRecords.forEach((record, index) => {
                const row = document.createElement('tr');
                // 工作时长
                let hoursWorked = '-';
                if (record.morning && record.evening) {
                    const dateStr = record.date;
                    const morning = new Date(`${dateStr} ${record.morning}`);
                    const evening = new Date(`${dateStr} ${record.evening}`);
                    const hours = ((evening - morning) / (1000 * 60 * 60)).toFixed(1);
                    hoursWorked = `${hours}小时`;
                }
                
                // 状态样式
                let statusClass = 'text-gray-500';
                let statusText = '未打卡';
                if (record.morning && record.evening) {
                    if (record.remark.includes('扣半天工资')) {
                        statusClass = 'text-danger';
                        statusText = '严重迟到';
                    } else if (record.remark.includes('早退')) {
                        statusClass = 'text-danger';
                        statusText = '早退';
                    } else if (record.remark.includes('迟到')) {
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
                
                // 编辑按钮
                const recordIndex = 打卡记录.findIndex(r => 
                    r.employeeId === record.employeeId && 
                    r.date === record.date
                );
                
                row.innerHTML = `
                    <td class="px-3 py-4 whitespace-nowrap">${record.employeeId}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.employeeName}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.date}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.weekday}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.morning || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${record.evening || '-'}</td>
                    <td class="px-3 py-4 whitespace-nowrap">${hoursWorked}</td>
                    <td class="px-3 py-4 whitespace-nowrap"><span class="${statusClass}">${statusText}</span></td>
                    <td class="px-3 py-4 whitespace-nowrap text-sm ${record.remark.includes('扣') || record.remark.includes('早退') ? 'text-danger' : record.remark.includes('迟到') ? 'text-warning' : 'text-gray-600'}">
                        ${record.remark}
                    </td>
                    <td class="px-3 py-4 whitespace-nowrap">
                        <button class="edit-record-btn text-primary hover:text-primary/80" data-index="${recordIndex}">
                            <i class="fa fa-edit"></i> 编辑
                        </button>
                    </td>
                `;
                adminAllRecordsEl.appendChild(row);
            });
            
            // 为编辑按钮添加事件监听
            document.querySelectorAll('.edit-record-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const index = parseInt(e.currentTarget.getAttribute('data-index'));
                    openEditModal(index);
                });
            });
        }

        // 打开编辑模态框
        function openEditModal(index) {
            const record = 打卡记录[index];
            if (!record) return;
            
            // 填充表单数据
            editRecordIndex.value = index;
            editEmployeeName.textContent = `${record.employeeId} - ${record.employeeName}`;
            editRecordDate.textContent = record.date;
            editMorningTime.value = record.morning ? record.morning.slice(0, 5) : '';
            editEveningTime.value = record.evening ? record.evening.slice(0, 5) : '';
            editRemark.value = record.remark;
            
            // 显示模态框
            editRecordModal.classList.remove('hidden');
        }

        // 保存编辑的记录
        function handleSaveEdit(e) {
            e.preventDefault();
            const index = parseInt(editRecordIndex.value);
            if (isNaN(index) || index < 0 || index >= 打卡记录.length) return;
            
            // 获取表单数据
            const morningTime = editMorningTime.value ? `${editMorningTime.value}:00` : '';
            const eveningTime = editEveningTime.value ? `${editEveningTime.value}:00` : '';
            const remark = editRemark.value.trim();
            
            // 更新记录
            const record = 打卡记录[index];
            record.morning = morningTime;
            record.evening = eveningTime;
            record.remark = remark;
            
            // 重新计算状态
            const hasCheckedIn = !!morningTime;
            const hasCheckedOut = !!eveningTime;
            
            // 简单的状态计算
            let status = '未打卡';
            if (hasCheckedIn && hasCheckedOut) {
                if (remark.includes('扣半天工资')) {
                    status = '严重迟到';
                } else if (remark.includes('早退')) {
                    status = '早退';
                } else if (remark.includes('迟到')) {
                    status = '迟到';
                } else {
                    status = '正常';
                }
            } else if (hasCheckedIn) {
                status = '已上班';
            }
            record.status = status;
            
            // 保存到本地存储
            localStorage.setItem('checkinRecords', JSON.stringify(打卡记录));
            
            // 关闭模态框
            editRecordModal.classList.add('hidden');
            
            // 更新界面
            loadAdminAllRecords();
            updateAdminStats();
            updateRecentRecords();
            
            showNotification('修改成功', '打卡记录已更新', 'success');
        }

        // 更新管理员统计数据
        function updateAdminStats() {
            if (!currentUser || currentUser.role !== 'admin') return;
            
            const today = new Date();
            const currentMonth = today.getMonth() + 1;
            const currentYear = today.getFullYear();
            
            // 本月所有打卡记录
            const monthlyRecords = [...打卡记录].filter(r => {
                const recordDate = new Date(r.date);
                return recordDate.getFullYear() === currentYear && 
                       recordDate.getMonth() + 1 === currentMonth;
            });
            
            // 异常打卡记录（迟到、早退、严重迟到）
            const abnormalRecords = monthlyRecords.filter(r => 
                r.remark.includes('迟到') || r.remark.includes('早退') || r.remark.includes('扣')
            );
            
            // 更新统计数字
            adminTotalEmployeesEl.textContent = employees.length;
            adminTotalCheckinsEl.textContent = monthlyRecords.length;
            adminAbnormalCheckinsEl.textContent = abnormalRecords.length;
        }

        // 更新个人统计
        function updateStats() {
            if (!currentUser || currentUser.role === 'admin') return;
            
            const today = new Date();
            const currentMonth = today.getMonth() + 1;
            const currentYear = today.getFullYear();
            // 筛选当前员工当月的记录
            const userMonthlyRecords = [...打卡记录].filter(r => {
                if (r.employeeId !== currentUser.id) return false;
                const recordDate = new Date(r.date);
                return recordDate.getFullYear() === currentYear && 
                       recordDate.getMonth() + 1 === currentMonth;
            });
            
            // 应打卡天数
            const totalDays = 22;
            // 已打卡天数
            const checkedDays = userMonthlyRecords.filter(r => r.morning).length;
            // 迟到次数（含严重迟到）
            const lateCount = userMonthlyRecords.filter(r => r.remark.includes('迟到')).length;
            // 扣薪次数（仅严重迟到）
            const salaryDeductCount = userMonthlyRecords.filter(r => r.remark.includes('扣半天工资')).length;
            
            // 更新UI
            document.getElementById('totalDays').textContent = totalDays;
            document.getElementById('checkedDays').textContent = checkedDays;
            lateCountEl.textContent = lateCount;
            salaryDeductCountEl.textContent = salaryDeductCount;
        }

        // 导出个人记录 - 实际项目中会发送到服务器生成Excel文件并下载
        function exportPersonalRecords() {
            if (!currentUser || currentUser.role === 'admin') return;
            
            const selectedMonth = personalMonthSelector.value;
            const [year, month] = selectedMonth.split('-');
            
            // 创建导出数据
            const exportData = [
                ["员工姓名", "日期", "星期", "上班时间", "下班时间", "工作时长", "状态", "备注"]
            ];
            
            // 收集数据
            const userMonthlyRecords = [...打卡记录]
                .filter(r => {
                    if (r.employeeId !== currentUser.id) return false;
                    const recordDate = new Date(r.date);
                    return recordDate.getFullYear() === parseInt(year) && 
                           recordDate.getMonth() + 1 === parseInt(month);
                })
                .sort((a, b) => new Date(a.date) - new Date(b.date));
            
            // 格式化数据
            userMonthlyRecords.forEach(record => {
                let hoursWorked = '-';
                if (record.morning && record.evening) {
                    const dateStr = record.date;
                    const morning = new Date(`${dateStr} ${record.morning}`);
                    const evening = new Date(`${dateStr} ${record.evening}`);
                    const hours = ((evening - morning) / (1000 * 60 * 60)).toFixed(1);
                    hoursWorked = `${hours}小时`;
                }
                
                exportData.push([
                    record.employeeName,
                    record.date,
                    record.weekday,
                    record.morning || '-',
                    record.evening || '-',
                    hoursWorked,
                    record.status,
                    record.remark
                ]);
            });
            
            // 模拟导出（实际项目中会使用如SheetJS库生成Excel文件）
            const blob = new Blob([convertToCSV(exportData)], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            link.setAttribute('href', url);
            link.setAttribute('download', `${currentUser.name}_${year}${month}_打卡记录.csv`);
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            showNotification('导出成功', `已将${year}年${month}月的个人打卡记录导出`, 'success');
        }

        // 导出管理员记录（所有员工）
        function exportAdminRecords() {
            if (!currentUser || currentUser.role !== 'admin') return;
            
            const selectedMonth = adminMonthSelector.value;
            const selectedEmployee = adminEmployeeFilter.value;
            const [year, month] = selectedMonth.split('-');
            
            // 创建导出数据
            const exportData = [
                ["工号", "员工姓名", "日期", "星期", "上班时间", "下班时间", "工作时长", "状态", "备注"]
            ];
            
            // 筛选记录
            let filteredRecords = [...打卡记录]
                .filter(r => {
                    // 员工筛选
                    if (selectedEmployee !== 'all' && r.employeeId !== selectedEmployee) {
                        return false;
                    }
                    
                    // 月份筛选
                    const recordDate = new Date(r.date);
                    return recordDate.getFullYear() === parseInt(year) && 
                           recordDate.getMonth() + 1 === parseInt(month);
                })
                .sort((a, b) => {
                    if (a.employeeId !== b.employeeId) {
                        return a.employeeId.localeCompare(b.employeeId);
                    }
                    return new Date(a.date) - new Date(b.date);
                });
            
            // 格式化数据
            filteredRecords.forEach(record => {
                let hoursWorked = '-';
                if (record.morning && record.evening) {
                    const dateStr = record.date;
                    const morning = new Date(`${dateStr} ${record.morning}`);
                    const evening = new Date(`${dateStr} ${record.evening}`);
                    const hours = ((evening - morning) / (1000 * 60 * 60)).toFixed(1);
                    hoursWorked = `${hours}小时`;
                }
                
                exportData.push([
                    record.employeeId,
                    record.employeeName,
                    record.date,
                    record.weekday,
                    record.morning || '-',
                    record.evening || '-',
                    hoursWorked,
                    record.status,
                    record.remark
                ]);
            });
            
            // 模拟导出
            const fileName = selectedEmployee === 'all' 
                ? `${year}${month}_所有员工打卡记录.csv` 
                : `${employees.find(e => e.id === selectedEmployee)?.name || '员工'}_${year}${month}_打卡记录.csv`;
                
            const blob = new Blob([convertToCSV(exportData)], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement('a');
            link.setAttribute('href', url);
            link.setAttribute('download', fileName);
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            
            let exportMsg = `${year}年${month}月的`;
            if (selectedEmployee === 'all') {
                exportMsg += '所有员工打卡记录';
            } else {
                const emp = employees.find(e => e.id === selectedEmployee) || {name: '未知员工'};
                exportMsg += `${emp.name}的打卡记录`;
            }
            
            showNotification('导出成功', `已将${exportMsg}导出`, 'success');
        }

        // 辅助函数：将数据转换为CSV格式
        function convertToCSV(arr) {
            return arr.map(row => 
                row.map(field => {
                    // 处理包含逗号或引号的字段
                    if (typeof field === 'string' && (field.includes(',') || field.includes('"'))) {
                        return `"${field.replace(/"/g, '""')}"`;
                    }
                    return field;
                }).join(',')
            ).join('\n');
        }

        // 通知功能
        function showNotification(title, message, type = 'info') {
            notificationTitle.textContent = title;
            notificationMessage.textContent = message;
            notificationIcon.innerHTML = '';
            
            if (type === 'success') {
                notificationIcon.innerHTML = '<i class="fa fa-check-circle text-success"></i>';
            } else if (type === 'warning') {
                notificationIcon.innerHTML = '<i class="fa fa-exclamation-triangle text-warning"></i>';
            } else if (type === 'error') {
                notificationIcon.innerHTML = '<i class="fa fa-exclamation-circle text-danger"></i>';
            } else {
                notificationIcon.innerHTML = '<i class="fa fa-info-circle text-primary"></i>';
            }
            
            notification.classList.remove('translate-y-20', 'opacity-0', 'pointer-events-none');
            setTimeout(hideNotification, 4000);
        }

        function hideNotification() {
            notification.classList.add('translate-y-20', 'opacity-0', 'pointer-events-none');
        }

        // 页面切换 - 确保登录页固定为首页
        function showCheckinSection() {
            loginSection.classList.add('hidden');
            checkinSection.classList.remove('hidden');
            personalRecordsSection.classList.add('hidden');
            adminDashboardSection.classList.add('hidden');
            
            // 加载数据
            if (currentUser) {
                loadCheckinData();
            }
        }

        function showPersonalRecordsSection() {
            checkinSection.classList.add('hidden');
            personalRecordsSection.classList.remove('hidden');
            adminDashboardSection.classList.add('hidden');
            loadPersonalAllRecords();
        }

        function showAdminDashboardSection() {
            if (!currentUser || currentUser.role !== 'admin') {
                showNotification('权限不足', '您没有管理员权限', 'error');
                return;
            }
            
            checkinSection.classList.add('hidden');
            personalRecordsSection.classList.add('hidden');
            adminDashboardSection.classList.remove('hidden');
            loadAdminAllRecords();
            updateAdminStats();
        }

        // 时间格式化
        function updateDateTime() {
            const now = new Date();
            currentDateEl.textContent = formatDate(now);
            currentTimeEl.textContent = formatTime(now);
        }

        function formatDate(date, simple = false) {
            const year = date.getFullYear();
            const month = (date.getMonth() + 1).toString().padStart(2, '0');
            const day = date.getDate().toString().padStart(2, '0');
            if (simple) return `${year}-${month}-${day}`;
            const weekday = getWeekday(date);
            return `${year}年${month}月${day}日 ${weekday}`;
        }

        function formatTime(date) {
            const hours = date.getHours().toString().padStart(2, '0');
            const minutes = date.getMinutes().toString().padStart(2, '0');
            const seconds = date.getSeconds().toString().padStart(2, '0');
            return `${hours}:${minutes}:${seconds}`;
        }

        function getWeekday(date) {
            const weekdays = ['周日', '周一', '周二', '周三', '周四', '周五', '周六'];
            return weekdays[date.getDay()];
        }
    </script>
</body>
</html>
