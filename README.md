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
            .title-fixed {
                @apply fixed top-0 left-0 right-0 bg-white z-50 border-b border-gray-200 shadow-sm transition-all duration-300;
            }
            .nav-fixed {
                @apply fixed top-16 left-0 right-0 bg-white/95 backdrop-blur-sm z-40 transition-all duration-300 shadow-sm;
            }
            .input-focus {
                @apply focus:ring-2 focus:ring-primary/70 focus:border-primary transition-all outline-none;
            }
        }
    </style>
    
    <style>
        /* 全局样式 */
        body {
            font-family: 'Inter', system-ui, sans-serif;
            /* 为固定标题和导航预留空间 */
            padding-top: 80px !important;
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
        
        @keyframes scaleIn {
            from { opacity: 0; transform: scale(0.98); }
            to { opacity: 1; transform: scale(1); }
        }
        
        .animate-fadeIn {
            animation: fadeIn 0.5s ease forwards;
        }
        
        .animate-scaleIn {
            animation: scaleIn 0.3s ease forwards;
        }
        
        /* 表格hover效果 */
        .table-row-hover:hover {
            background-color: rgba(22, 93, 255, 0.03);
        }
        
        /* 模态框背景过渡 */
        #editRecordModal {
            transition: opacity 0.3s ease;
        }
        
        #editRecordModal > div {
            animation: fadeIn 0.3s ease forwards;
        }
        
        /* 通知动画优化 */
        #notification {
            transition: all 0.3s cubic-bezier(0.68, -0.55, 0.27, 1.55);
        }
    </style>
</head>
<body class="bg-gray-50 text-neutral-dark min-h-screen">
    <!-- 1. 固定顶部标题栏 - 始终显示在网页最上方 -->
    <div class="title-fixed">
        <div class="container mx-auto px-4 py-3 flex items-center justify-center">
            <div class="flex items-center space-x-3">
                <i class="fa fa-clock-o text-primary text-2xl md:text-3xl transition-transform duration-300 hover:rotate-12"></i>
                <h1 class="text-2xl md:text-3xl font-bold text-primary tracking-tight">企业在线打卡系统</h1>
            </div>
        </div>
    </div>

    <!-- 2. 顶部导航栏 - 固定在标题下方 -->
    <header id="navbar" class="nav-fixed">
        <div class="container mx-auto px-4 py-2 flex flex-wrap justify-between items-center gap-2">
            <!-- 系统说明 - 适配移动端 -->
            <div class="hidden md:flex items-center">
                <p class="text-gray-500 text-sm flex items-center">
                    <i class="fa fa-bullseye text-primary/70 mr-1"></i>
                    高效管理 · 便捷打卡
                </p>
            </div>
            
            <div class="flex items-center space-x-3 md:space-x-4">
                <!-- 用户信息 - 优化显示逻辑 -->
                <div id="userInfo" class="hidden md:flex items-center space-x-2">
                    <img src="https://picsum.photos/id/1005/40/40" alt="用户头像" 
                         class="w-8 h-8 rounded-full object-cover border-2 border-primary transition-all duration-300 hover:ring-2 hover:ring-primary/30">
                    <span id="usernameDisplay" class="font-medium"></span>
                    <span id="userRole" class="text-xs px-2 py-0.5 rounded-full bg-primary/10 text-primary hidden flex items-center">
                        <i class="fa fa-shield mr-0.5"></i>管理员
                    </span>
                </div>
                
                <!-- 移动端用户信息折叠 -->
                <div id="mobileUserInfo" class="md:hidden">
                    <button id="mobileUserBtn" class="text-gray-600 hover:text-primary transition-colors">
                        <i class="fa fa-user-circle text-xl"></i>
                    </button>
                    <div id="mobileUserDropdown" class="hidden absolute top-20 right-4 bg-white rounded-lg shadow-lg p-3 w-48 animate-scaleIn z-50">
                        <div class="flex items-center space-x-2 mb-2 pb-2 border-b border-gray-100">
                            <img src="https://picsum.photos/id/1005/32/32" alt="用户头像" class="w-7 h-7 rounded-full border border-primary">
                            <div>
                                <p id="mobileUsername" class="text-sm font-medium"></p>
                                <p id="mobileUserRole" class="text-xs text-primary hidden">管理员</p>
                            </div>
                        </div>
                        <button id="mobileLogoutBtn" class="w-full text-left text-sm text-gray-600 hover:text-danger transition-colors py-1.5 flex items-center">
                            <i class="fa fa-sign-out mr-2"></i>退出登录
                        </button>
                    </div>
                </div>
                
                <!-- 管理员特有按钮 -->
                <button id="adminDashboardBtn" class="hidden items-center text-gray-600 hover:text-primary transition-colors px-2 py-1 rounded-md hover:bg-primary/5">
                    <i class="fa fa-tachometer"></i>
                    <span class="ml-1 hidden md:inline">管理面板</span>
                </button>
                
                <!-- 退出按钮 - 区分移动端和桌面端 -->
                <button id="desktopLogoutBtn" class="hidden md:flex items-center text-gray-600 hover:text-danger transition-colors px-2 py-1 rounded-md hover:bg-danger/5">
                    <i class="fa fa-sign-out"></i>
                    <span class="ml-1 hidden md:inline">退出</span>
                </button>
            </div>
        </div>
    </header>

    <!-- 主内容区 - 优化间距和响应式 -->
    <main class="container mx-auto px-4 py-8 md:py-12">
        <!-- 登录界面 - 固定显示在首页，优化居中效果 -->
        <section id="loginSection" class="max-w-md mx-auto animate-fadeIn">
            <div class="bg-white rounded-xl p-6 md:p-8 card-shadow transition-all duration-300 hover:shadow-md">
                <div class="text-center mb-6">
                    <div class="inline-flex items-center justify-center w-16 h-16 rounded-full gradient-bg mb-4 shadow-lg">
                        <i class="fa fa-key text-white text-2xl"></i>
                    </div>
                    <h2 class="text-2xl font-bold text-gray-800">员工登录</h2>
                    <p class="text-gray-500 mt-1">请输入工号和密码登录系统</p>
                </div>
                
                <form id="loginForm" class="space-y-4">
                    <div>
                        <label for="employeeId" class="block text-sm font-medium text-gray-700 mb-1.5">工号</label>
                        <div class="relative">
                            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none text-gray-400">
                                <i class="fa fa-id-card"></i>
                            </div>
                            <input type="text" id="employeeId" name="employeeId" 
                                class="block w-full pl-10 pr-3 py-2.5 border border-gray-300 rounded-lg input-focus"
                                placeholder="例如：250001 或 admin" required>
                            <!-- 工号匹配的姓名预览 - 优化显示位置 -->
                            <p id="employeeNamePreview" class="absolute left-10 top-full mt-1 text-xs text-success hidden animate-fadeIn"></p>
                        </div>
                        <!-- 工号不存在提示 - 优化样式 -->
                        <p id="employeeIdError" class="mt-1 text-xs text-danger hidden animate-fadeIn">
                            <i class="fa fa-exclamation-circle mr-1"></i>该工号不存在，请核对后重新输入
                        </p>
                    </div>
                    
                    <div>
                        <label for="password" class="block text-sm font-medium text-gray-700 mb-1.5">密码</label>
                        <div class="relative">
                            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none text-gray-400">
                                <i class="fa fa-lock"></i>
                            </div>
                            <input type="password" id="password" name="password" 
                                class="block w-full pl-10 pr-10 py-2.5 border border-gray-300 rounded-lg input-focus"
                                placeholder="默认密码：123456" required>
                            <button type="button" id="togglePassword" class="absolute inset-y-0 right-0 pr-3 flex items-center text-gray-400 hover:text-gray-600 transition-colors">
                                <i class="fa fa-eye-slash"></i>
                            </button>
                        </div>
                        <!-- 密码提示 - 新增 -->
                        <p class="mt-1 text-xs text-gray-500">
                            <i class="fa fa-lightbulb-o mr-1 text-warning"></i>初始密码为123456，建议登录后修改
                        </p>
                    </div>
                    
                    <div class="pt-2">
                        <button type="submit" class="w-full bg-primary text-white py-3 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                            <i class="fa fa-sign-in mr-2"></i>登录系统
                        </button>
                    </div>
                </form>
                
                <div class="mt-4 text-center text-gray-500 text-sm">
                    <p>忘记密码？请联系管理员重置</p>
                </div>
            </div>
        </section>

        <!-- 打卡界面 (默认隐藏) - 优化布局响应式 -->
        <section id="checkinSection" class="hidden max-w-5xl mx-auto animate-fadeIn">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6 md:gap-8">
                <!-- 左侧：打卡卡片 - 优化高度和交互 -->
                <div class="md:col-span-1">
                    <div class="bg-white rounded-xl p-6 card-shadow h-full transition-all duration-300 hover:shadow-md">
                        <h2 class="text-xl font-bold text-gray-800 mb-4 flex items-center">
                            <i class="fa fa-calendar-check-o text-primary mr-2"></i>今日打卡
                        </h2>
                        
                        <!-- 时间规则提示 - 优化样式 -->
                        <div class="bg-blue-50 p-3 rounded-lg mb-4 text-sm text-gray-700 border border-blue-100">
                            <p class="font-medium flex items-center"><i class="fa fa-info-circle text-primary mr-1.5"></i>打卡规则</p>
                            <p class="mt-1.5"><i class="fa fa-clock-o text-gray-500 mr-1.5"></i>上班：8:30前正常，8:30-9:00迟到，9:00后扣半天工资</p>
                            <p class="mt-1"><i class="fa fa-clock-o text-gray-500 mr-1.5"></i>下班：17:00后正常，17:00前早退</p>
                        </div>
                        
                        <!-- 时间显示 - 优化视觉效果 -->
                        <div class="text-center py-4 border-y border-gray-100 mb-4">
                            <p id="currentDate" class="text-gray-500 mb-1.5 text-sm"></p>
                            <p id="currentTime" class="text-2xl md:text-3xl font-mono font-bold text-gray-800 tracking-wide"></p>
                        </div>
                        
                        <!-- 打卡状态 - 优化间距 -->
                        <div class="mt-2 space-y-4">
                            <div class="flex justify-between items-center pb-2 border-b border-gray-100">
                                <span class="text-gray-600 flex items-center"><i class="fa fa-sign-in text-gray-400 mr-1.5"></i>上班打卡</span>
                                <div class="text-right">
                                    <span id="morningCheckin" class="text-gray-400">未打卡</span>
                                    <!-- 上班备注显示 - 优化样式 -->
                                    <p id="morningRemark" class="text-xs mt-1 text-danger hidden flex items-center">
                                        <i class="fa fa-exclamation-circle mr-1"></i>
                                        <span></span>
                                    </p>
                                </div>
                            </div>
                            <div class="flex justify-between items-center pb-2 border-b border-gray-100">
                                <span class="text-gray-600 flex items-center"><i class="fa fa-sign-out text-gray-400 mr-1.5"></i>下班打卡</span>
                                <div class="text-right">
                                    <span id="eveningCheckout" class="text-gray-400">未打卡</span>
                                    <!-- 下班备注显示 - 优化样式 -->
                                    <p id="eveningRemark" class="text-xs mt-1 text-danger hidden flex items-center">
                                        <i class="fa fa-exclamation-circle mr-1"></i>
                                        <span></span>
                                    </p>
                                </div>
                            </div>
                        </div>
                        
                        <!-- 员工自定义备注输入框 - 优化交互 -->
                        <div class="mt-6">
                            <label for="userRemark" class="block text-sm font-medium text-gray-700 mb-1.5 flex items-center">
                                <i class="fa fa-pencil text-primary mr-1.5"></i>打卡备注
                            </label>
                            <input type="text" id="userRemark" 
                                class="w-full px-3 py-2.5 border border-gray-300 rounded-lg input-focus text-sm"
                                placeholder="请输入备注（如：外勤、请假、加班等）">
                            <p class="mt-1 text-xs text-gray-500">备注将随打卡记录一同保存</p>
                        </div>
                        
                        <!-- 打卡按钮 - 优化尺寸和状态 -->
                        <div class="mt-6">
                            <button id="checkinBtn" class="w-full bg-success text-white py-3 px-4 rounded-lg font-medium btn-hover flex items-center justify-center text-base">
                                <i class="fa fa-check mr-2"></i>
                                <span id="checkinBtnText">上班打卡</span>
                            </button>
                        </div>
                        
                        <!-- 提示文本 - 优化样式 -->
                        <div class="mt-4 text-center text-sm text-gray-500">
                            <p id="checkinTip" class="flex items-center justify-center"><i class="fa fa-lightbulb-o mr-1.5 text-warning"></i>点击按钮完成上班签到</p>
                        </div>
                    </div>
                </div>
                
                <!-- 右侧：打卡状态和统计 - 优化布局 -->
                <div class="md:col-span-2 space-y-6 md:space-y-8">
                    <!-- 本月打卡统计 - 优化卡片样式 -->
                    <div class="bg-white rounded-xl p-6 card-shadow transition-all duration-300 hover:shadow-md">
                        <h2 class="text-xl font-bold text-gray-800 mb-5 flex items-center">
                            <i class="fa fa-bar-chart text-primary mr-2"></i>本月打卡统计
                        </h2>
                        
                        <div class="grid grid-cols-2 md:grid-cols-4 gap-4 md:gap-5 text-center">
                            <div class="p-4 bg-blue-50 rounded-lg border border-blue-100 transition-all duration-300 hover:bg-blue-100/50">
                                <p class="text-gray-500 text-sm">应打卡天数</p>
                                <p id="totalDays" class="text-2xl font-bold text-primary mt-1.5">22</p>
                            </div>
                            <div class="p-4 bg-green-50 rounded-lg border border-green-100 transition-all duration-300 hover:bg-green-100/50">
                                <p class="text-gray-500 text-sm">已打卡天数</p>
                                <p id="checkedDays" class="text-2xl font-bold text-success mt-1.5">8</p>
                            </div>
                            <div class="p-4 bg-yellow-50 rounded-lg border border-yellow-100 transition-all duration-300 hover:bg-yellow-100/50">
                                <p class="text-gray-500 text-sm">迟到次数</p>
                                <p id="lateCount" class="text-2xl font-bold text-warning mt-1.5">1</p>
                            </div>
                            <div class="p-4 bg-red-50 rounded-lg border border-red-100 transition-all duration-300 hover:bg-red-100/50">
                                <p class="text-gray-500 text-sm">扣薪次数</p>
                                <p id="salaryDeductCount" class="text-2xl font-bold text-danger mt-1.5">0</p>
                            </div>
                        </div>
                    </div>
                    
                    <!-- 最近打卡记录（含备注列） - 优化表格样式 -->
                    <div class="bg-white rounded-xl p-6 card-shadow transition-all duration-300 hover:shadow-md">
                        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-5 gap-3">
                            <h2 class="text-xl font-bold text-gray-800 flex items-center">
                                <i class="fa fa-history text-primary mr-2"></i>最近打卡记录
                            </h2>
                            <button id="viewAllRecordsBtn" class="text-primary hover:text-primary/80 text-sm font-medium flex items-center transition-colors px-3 py-1.5 rounded-md hover:bg-primary/5">
                                查看全部 <i class="fa fa-angle-right ml-1.5"></i>
                            </button>
                        </div>
                        
                        <!-- 表格容器 - 优化滚动体验 -->
                        <div class="overflow-x-auto rounded-lg border border-gray-100">
                            <table class="min-w-full divide-y divide-gray-200">
                                <thead class="bg-gray-50">
                                    <tr>
                                        <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">员工姓名</th>
                                        <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日期</th>
                                        <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">上班时间</th>
                                        <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">下班时间</th>
                                        <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                                        <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">备注</th>
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

        <!-- 个人打卡记录查询界面 (含备注列) - 优化响应式 -->
        <section id="personalRecordsSection" class="hidden max-w-5xl mx-auto animate-fadeIn">
            <div class="bg-white rounded-xl p-6 md:p-8 card-shadow mb-6 transition-all duration-300 hover:shadow-md">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6 gap-4">
                    <h2 class="text-xl font-bold text-gray-800 mb-2 md:mb-0 flex items-center">
                        <i class="fa fa-list-alt text-primary mr-2"></i>个人打卡记录查询
                    </h2>
                    
                    <div class="flex flex-col sm:flex-row w-full md:w-auto gap-3">
                        <div class="relative w-full sm:w-auto">
                            <select id="personalMonthSelector" class="block w-full sm:w-48 pl-3 pr-10 py-2.5 border border-gray-300 rounded-lg input-focus appearance-none bg-white">
                                <!-- 月份选项将通过JS动态生成 -->
                            </select>
                            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                                <i class="fa fa-chevron-down text-xs"></i>
                            </div>
                        </div>
                        
                        <button id="backToCheckinBtn" class="bg-gray-100 text-gray-700 py-2.5 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                            <i class="fa fa-arrow-left mr-2"></i>返回打卡
                        </button>
                    </div>
                </div>
                
                <!-- 表格容器 - 优化滚动和边框 -->
                <div class="overflow-x-auto rounded-lg border border-gray-100 mb-6">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">员工姓名</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日期</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">星期</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">上班时间</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">下班时间</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">工作时长</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">备注</th>
                            </tr>
                        </thead>
                        <tbody id="personalAllRecords" class="bg-white divide-y divide-gray-200">
                            <!-- 记录将通过JS动态生成 -->
                        </tbody>
                    </table>
                </div>
                
                <!-- 统计和导出 - 优化布局 -->
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                    <div class="text-gray-600 text-sm flex items-center">
                        <i class="fa fa-database text-gray-400 mr-1.5"></i>
                        显示 <span id="personalShowingCount" class="font-medium text-gray-800">0</span> 条记录，共 <span id="personalTotalCount" class="font-medium text-gray-800">0</span> 条
                    </div>
                    
                    <div class="flex space-x-3">
                        <button id="personalExportExcelBtn" class="bg-green-600 text-white py-2.5 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                            <i class="fa fa-download mr-2"></i>导出Excel
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- 管理员面板 - 优化布局和功能 -->
        <section id="adminDashboardSection" class="hidden max-w-6xl mx-auto animate-fadeIn">
            <!-- 管理员统计概览 - 单独卡片 -->
            <div class="bg-white rounded-xl p-6 md:p-8 card-shadow mb-6 transition-all duration-300 hover:shadow-md">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6 gap-4">
                    <h2 class="text-xl font-bold text-gray-800 mb-2 md:mb-0 flex items-center">
                        <i class="fa fa-tachometer text-primary mr-2"></i>管理员控制面板
                    </h2>
                    
                    <div class="flex flex-col sm:flex-row w-full md:w-auto gap-3">
                        <button id="adminBackToCheckinBtn" class="bg-gray-100 text-gray-700 py-2.5 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                            <i class="fa fa-arrow-left mr-2"></i>返回首页
                        </button>
                    </div>
                </div>
                
                <!-- 统计卡片 - 优化布局 -->
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 md:gap-6">
                    <div class="p-4 bg-blue-50 rounded-lg border border-blue-100 transition-all duration-300 hover:bg-blue-100/50 flex items-center gap-3">
                        <div class="w-12 h-12 rounded-full bg-primary/10 flex items-center justify-center text-primary">
                            <i class="fa fa-users text-xl"></i>
                        </div>
                        <div>
                            <p class="text-gray-500 text-sm">员工总数</p>
                            <p id="adminTotalEmployees" class="text-2xl font-bold text-primary mt-1">0</p>
                        </div>
                    </div>
                    <div class="p-4 bg-green-50 rounded-lg border border-green-100 transition-all duration-300 hover:bg-green-100/50 flex items-center gap-3">
                        <div class="w-12 h-12 rounded-full bg-success/10 flex items-center justify-center text-success">
                            <i class="fa fa-calendar-check-o text-xl"></i>
                        </div>
                        <div>
                            <p class="text-gray-500 text-sm">本月总打卡次数</p>
                            <p id="adminTotalCheckins" class="text-2xl font-bold text-success mt-1">0</p>
                        </div>
                    </div>
                    <div class="p-4 bg-yellow-50 rounded-lg border border-yellow-100 transition-all duration-300 hover:bg-yellow-100/50 flex items-center gap-3">
                        <div class="w-12 h-12 rounded-full bg-warning/10 flex items-center justify-center text-warning">
                            <i class="fa fa-exclamation-triangle text-xl"></i>
                        </div>
                        <div>
                            <p class="text-gray-500 text-sm">本月异常打卡</p>
                            <p id="adminAbnormalCheckins" class="text-2xl font-bold text-warning mt-1">0</p>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- 管理员记录查询 - 所有员工 - 优化筛选和表格 -->
            <div class="bg-white rounded-xl p-6 md:p-8 card-shadow mb-6 transition-all duration-300 hover:shadow-md">
                <div class="flex flex-col md:flex-row justify-between items-start md:items-center mb-6 gap-4">
                    <h2 class="text-xl font-bold text-gray-800 mb-2 md:mb-0 flex items-center">
                        <i class="fa fa-users text-primary mr-2"></i>所有员工打卡记录
                    </h2>
                    
                    <div class="flex flex-col sm:flex-row w-full md:w-auto gap-3">
                        <div class="relative w-full sm:w-auto">
                            <select id="adminMonthSelector" class="block w-full sm:w-48 pl-3 pr-10 py-2.5 border border-gray-300 rounded-lg input-focus appearance-none bg-white">
                                <!-- 月份选项将通过JS动态生成 -->
                            </select>
                            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                                <i class="fa fa-chevron-down text-xs"></i>
                            </div>
                        </div>
                        
                        <div class="relative w-full sm:w-auto">
                            <select id="adminEmployeeFilter" class="block w-full sm:w-64 pl-3 pr-10 py-2.5 border border-gray-300 rounded-lg input-focus appearance-none bg-white">
                                <option value="all">所有员工</option>
                                <!-- 员工选项将通过JS动态生成 -->
                            </select>
                            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                                <i class="fa fa-chevron-down text-xs"></i>
                            </div>
                        </div>
                        
                        <button id="adminExportAllBtn" class="bg-green-600 text-white py-2.5 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                            <i class="fa fa-download mr-2"></i>导出全部
                        </button>
                    </div>
                </div>
                
                <!-- 表格容器 - 优化滚动和样式 -->
                <div class="overflow-x-auto rounded-lg border border-gray-100 mb-6">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">工号</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">员工姓名</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">日期</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">星期</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">上班时间</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">下班时间</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">工作时长</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">状态</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">备注</th>
                                <th class="px-4 py-3 bg-gray-50 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">操作</th>
                            </tr>
                        </thead>
                        <tbody id="adminAllRecords" class="bg-white divide-y divide-gray-200">
                            <!-- 记录将通过JS动态生成 -->
                        </tbody>
                    </table>
                </div>
                
                <!-- 统计信息 - 优化样式 -->
                <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                    <div class="text-gray-600 text-sm flex items-center">
                        <i class="fa fa-database text-gray-400 mr-1.5"></i>
                        显示 <span id="adminShowingCount" class="font-medium text-gray-800">0</span> 条记录，共 <span id="adminTotalCount" class="font-medium text-gray-800">0</span> 条
                    </div>
                </div>
            </div>
        </section>
    </main>

    <!-- 编辑打卡记录的模态框 - 优化样式和交互 -->
    <div id="editRecordModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 hidden">
        <div class="bg-white rounded-xl p-6 md:p-7 w-full max-w-md mx-4 shadow-xl">
            <div class="flex justify-between items-center mb-5 pb-3 border-b border-gray-100">
                <h3 class="text-lg font-bold text-gray-800 flex items-center">
                    <i class="fa fa-pencil-square-o text-primary mr-2"></i>编辑打卡记录
                </h3>
                <button id="closeEditModal" class="text-gray-400 hover:text-gray-600 transition-colors">
                    <i class="fa fa-times text-lg"></i>
                </button>
            </div>
            
            <form id="editRecordForm">
                <input type="hidden" id="editRecordIndex">
                
                <!-- 员工信息 - 优化显示 -->
                <div class="mb-4">
                    <label class="block text-sm font-medium text-gray-700 mb-1.5">员工信息</label>
                    <div class="flex items-center gap-2 p-2.5 bg-gray-50 rounded-lg border border-gray-100">
                        <i class="fa fa-user text-primary"></i>
                        <p id="editEmployeeName" class="text-gray-800 font-medium"></p>
                    </div>
                </div>
                
                <!-- 日期信息 - 优化显示 -->
                <div class="mb-5">
                    <label class="block text-sm font-medium text-gray-700 mb-1.5">打卡日期</label>
                    <div class="flex items-center gap-2 p-2.5 bg-gray-50 rounded-lg border border-gray-100">
                        <i class="fa fa-calendar text-primary"></i>
                        <p id="editRecordDate" class="text-gray-800"></p>
                    </div>
                </div>
                
                <!-- 时间编辑 - 优化布局 -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-5">
                    <div>
                        <label for="editMorningTime" class="block text-sm font-medium text-gray-700 mb-1.5">上班时间</label>
                        <input type="time" id="editMorningTime" class="w-full px-3 py-2.5 border border-gray-300 rounded-lg input-focus">
                    </div>
                    <div>
                        <label for="editEveningTime" class="block text-sm font-medium text-gray-700 mb-1.5">下班时间</label>
                        <input type="time" id="editEveningTime" class="w-full px-3 py-2.5 border border-gray-300 rounded-lg input-focus">
                    </div>
                </div>
                
                <!-- 备注编辑 - 优化高度 -->
                <div class="mb-6">
                    <label for="editRemark" class="block text-sm font-medium text-gray-700 mb-1.5">备注信息</label>
                    <textarea id="editRemark" rows="3" class="w-full px-3 py-2.5 border border-gray-300 rounded-lg input-focus" placeholder="请输入备注信息（可说明异常原因）"></textarea>
                </div>
                
                <!-- 按钮区域 - 优化间距 -->
                <div class="flex space-x-3">
                    <button type="button" id="cancelEditBtn" class="flex-1 bg-gray-100 text-gray-700 py-2.5 px-4 rounded-lg font-medium transition-all duration-300 hover:bg-gray-200">
                        取消
                    </button>
                    <button type="submit" class="flex-1 bg-primary text-white py-2.5 px-4 rounded-lg font-medium btn-hover">
                        保存修改
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- 页脚 - 优化样式 -->
    <footer class="bg-white border-t border-gray-200 py-6 mt-auto">
        <div class="container mx-auto px-4 text-center text-gray-500 text-sm">
            <p class="flex items-center justify-center mb-1.5">
                <i class="fa fa-copyright mr-1.5"></i>
                2024 企业在线打卡系统 - 版权所有
            </p>
            <p class="flex items-center justify-center">
                <i class="fa fa-headphones mr-1.5"></i>
                技术支持：IT部门 | 客服热线：400-123-4567
            </p>
        </div>
    </footer>

    <!-- 通知弹窗 - 优化样式和动画 -->
    <div id="notification" class="fixed bottom-6 right-6 bg-white rounded-lg shadow-lg p-4 max-w-sm transform translate-y-20 opacity-0 transition-all duration-300 pointer-events-none z-50 border border-gray-100">
        <div class="flex items-start">
            <div id="notificationIcon" class="mr-3 text-xl mt-0.5">
                <i class="fa fa-check-circle text-success"></i>
            </div>
            <div class="flex-1">
                <h3 id="notificationTitle" class="font-medium text-gray-800"></h3>
                <p id="notificationMessage" class="text-gray-600 text-sm mt-1"></p>
            </div>
            <button id="closeNotification" class="text-gray-400 hover:text-gray-600 ml-2 transition-colors">
                <i class="fa fa-times"></i>
            </button>
        </div>
    </div>

    <!-- JavaScript：核心逻辑 - 优化交互和性能 -->
    <script>
        // 管理员账号信息
        const adminAccount = {
            id: "admin",
            name: "管理员",
            password: "admin123",
            role: "admin"
        };

        // 用户提供的员工数据
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
            morningRemark: "", 
            eveningRemark: ""  
        };
        let 打卡记录 = [];

        // DOM元素 - 优化获取逻辑
        const DOM = {
            // 页面容器
            loginSection: document.getElementById('loginSection'),
            checkinSection: document.getElementById('checkinSection'),
            personalRecordsSection: document.getElementById('personalRecordsSection'),
            adminDashboardSection: document.getElementById('adminDashboardSection'),
            
            // 登录相关
            loginForm: document.getElementById('loginForm'),
            employeeIdInput: document.getElementById('employeeId'),
            passwordInput: document.getElementById('password'),
            togglePassword: document.getElementById('togglePassword'),
            employeeNamePreview: document.getElementById('employeeNamePreview'),
            employeeIdError: document.getElementById('employeeIdError'),
            
            // 用户信息
            usernameDisplay: document.getElementById('usernameDisplay'),
            userRole: document.getElementById('userRole'),
            userInfo: document.getElementById('userInfo'),
            mobileUserBtn: document.getElementById('mobileUserBtn'),
            mobileUserDropdown: document.getElementById('mobileUserDropdown'),
            mobileUsername: document.getElementById('mobileUsername'),
            mobileUserRole: document.getElementById('mobileUserRole'),
            
            // 按钮
            logoutBtns: {
                desktop: document.getElementById('desktopLogoutBtn'),
                mobile: document.getElementById('mobileLogoutBtn')
            },
            adminDashboardBtn: document.getElementById('adminDashboardBtn'),
            checkinBtn: document.getElementById('checkinBtn'),
            checkinBtnText: document.getElementById('checkinBtnText'),
            viewAllRecordsBtn: document.getElementById('viewAllRecordsBtn'),
            backToCheckinBtn: document.getElementById('backToCheckinBtn'),
            adminBackToCheckinBtn: document.getElementById('adminBackToCheckinBtn'),
            
            // 打卡状态
            morningCheckin: document.getElementById('morningCheckin'),
            eveningCheckout: document.getElementById('eveningCheckout'),
            morningRemark: document.getElementById('morningRemark'),
            eveningRemark: document.getElementById('eveningRemark'),
            userRemarkInput: document.getElementById('userRemark'),
            currentDateEl: document.getElementById('currentDate'),
            currentTimeEl: document.getElementById('currentTime'),
            checkinTip: document.getElementById('checkinTip'),
            
            // 统计元素
            totalDays: document.getElementById('totalDays'),
            checkedDays: document.getElementById('checkedDays'),
            lateCountEl: document.getElementById('lateCount'),
            salaryDeductCountEl: document.getElementById('salaryDeductCount'),
            
            // 记录表格
            recentRecordsEl: document.getElementById('recentRecords'),
            personalAllRecordsEl: document.getElementById('personalAllRecords'),
            adminAllRecordsEl: document.getElementById('adminAllRecords'),
            
            // 筛选器
            personalMonthSelector: document.getElementById('personalMonthSelector'),
            adminMonthSelector: document.getElementById('adminMonthSelector'),
            adminEmployeeFilter: document.getElementById('adminEmployeeFilter'),
            
            // 统计文本
            personalShowingCountEl: document.getElementById('personalShowingCount'),
            personalTotalCountEl: document.getElementById('personalTotalCount'),
            adminShowingCountEl: document.getElementById('adminShowingCount'),
            adminTotalCountEl: document.getElementById('adminTotalCount'),
            adminTotalEmployeesEl: document.getElementById('adminTotalEmployees'),
            adminTotalCheckinsEl: document.getElementById('adminTotalCheckins'),
            adminAbnormalCheckinsEl: document.getElementById('adminAbnormalCheckins'),
            
            // 导出按钮
            personalExportExcelBtn: document.getElementById('personalExportExcelBtn'),
            adminExportAllBtn: document.getElementById('adminExportAllBtn'),
            
            // 编辑模态框
            editRecordModal: document.getElementById('editRecordModal'),
            closeEditModal: document.getElementById('closeEditModal'),
            cancelEditBtn: document.getElementById('cancelEditBtn'),
            editRecordForm: document.getElementById('editRecordForm'),
            editRecordIndex: document.getElementById('editRecordIndex'),
            editEmployeeName: document.getElementById('editEmployeeName'),
            editRecordDate: document.getElementById('editRecordDate'),
            editMorningTime: document.getElementById('editMorningTime'),
            editEveningTime: document.getElementById('editEveningTime'),
            editRemark: document.getElementById('editRemark'),
            
            // 通知
            notification: document.getElementById('notification'),
            notificationTitle: document.getElementById('notificationTitle'),
            notificationMessage: document.getElementById('notificationMessage'),
            notificationIcon: document.getElementById('notificationIcon'),
            closeNotification: document.getElementById('closeNotification'),
            
            // 导航栏
            navbar: document.getElementById('navbar')
        };

        // 初始化
        document.addEventListener('DOMContentLoaded', () => {
            // 更新时间
            updateDateTime();
            setInterval(updateDateTime, 1000);
            
            // 初始化月份选择器
            initMonthSelector(DOM.personalMonthSelector);
            initMonthSelector(DOM.adminMonthSelector);
            
            // 初始化管理员员工筛选器
            initEmployeeFilter();
            
            // 加载本地存储数据
            loadLocalStorageData();
            
            // 事件监听 - 优化绑定逻辑
            bindEvents();
            
            // 初始化页面状态
            initPageState();
        });

        // 初始化月份选择器 - 优化逻辑
        function initMonthSelector(selectorElement) {
            const today = new Date();
            const currentYear = today.getFullYear();
            const currentMonth = today.getMonth() + 1;
            
            // 生成近6个月选项
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

        // 初始化员工筛选器（管理员用）- 优化排序
        function initEmployeeFilter() {
            // 清空现有选项（保留"所有员工"）
            while (DOM.adminEmployeeFilter.options.length > 1) {
                DOM.adminEmployeeFilter.remove(1);
            }
            
            // 按工号排序添加员工选项
            employees.sort((a, b) => a.id.localeCompare(b.id)).forEach(emp => {
                const option = document.createElement('option');
                option.value = emp.id;
                option.textContent = `${emp.id} - ${emp.name}`;
                DOM.adminEmployeeFilter.appendChild(option);
            });
            
            // 更新员工总数统计
            DOM.adminTotalEmployeesEl.textContent = employees.length;
        }

        // 加载本地存储数据 - 优化逻辑
        function loadLocalStorageData() {
            // 加载打卡记录
            const savedRecords = localStorage.getItem('checkinRecords');
            打卡记录 = savedRecords ? JSON.parse(savedRecords) : [];
            
            // 加载用户信息
            const savedUser = localStorage.getItem('currentUser');
            if (savedUser) {
                currentUser = JSON.parse(savedUser);
                // 补全员工姓名
                if (currentUser.role !== 'admin') {
                    const employee = employees.find(emp => emp.id === currentUser.id);
                    if (employee) currentUser.name = employee.name;
                }
            }
        }

        // 绑定事件 - 优化结构
        function bindEvents() {
            // 工号输入实时匹配姓名
            DOM.employeeIdInput.addEventListener('input', handleEmployeeIdInput);
            
            // 登录表单提交
            DOM.loginForm.addEventListener('submit', handleLogin);
            
            // 密码可见性切换
            DOM.togglePassword.addEventListener('click', togglePasswordVisibility);
            
            // 退出登录（桌面端和移动端）
            DOM.logoutBtns.desktop.addEventListener('click', handleLogout);
            DOM.logoutBtns.mobile.addEventListener('click', handleLogout);
            
            // 打卡按钮
            DOM.checkinBtn.addEventListener('click', handleCheckin);
            
            // 页面切换
            DOM.viewAllRecordsBtn.addEventListener('click', () => showSection('personal'));
            DOM.backToCheckinBtn.addEventListener('click', () => showSection('checkin'));
            DOM.adminBackToCheckinBtn.addEventListener('click', () => showSection('checkin'));
            DOM.adminDashboardBtn.addEventListener('click', () => showSection('admin'));
            
            // 筛选器变化
            DOM.personalMonthSelector.addEventListener('change', loadPersonalAllRecords);
            DOM.adminMonthSelector.addEventListener('change', loadAdminAllRecords);
            DOM.adminEmployeeFilter.addEventListener('change', loadAdminAllRecords);
            
            // 导出按钮
            DOM.personalExportExcelBtn.addEventListener('click', exportPersonalRecords);
            DOM.adminExportAllBtn.addEventListener('click', exportAdminRecords);
            
            // 通知关闭
            DOM.closeNotification.addEventListener('click', hideNotification);
            
            // 编辑模态框
            DOM.closeEditModal.addEventListener('click', () => DOM.editRecordModal.classList.add('hidden'));
            DOM.cancelEditBtn.addEventListener('click', () => DOM.editRecordModal.classList.add('hidden'));
            DOM.editRecordForm.addEventListener('submit', handleSaveEdit);
            
            // 移动端用户菜单
            DOM.mobileUserBtn.addEventListener('click', () => {
                DOM.mobileUserDropdown.classList.toggle('hidden');
            });
            
            // 点击页面其他区域关闭移动端菜单
            document.addEventListener('click', (e) => {
                if (!DOM.mobileUserBtn.contains(e.target) && !DOM.mobileUserDropdown.contains(e.target)) {
                    DOM.mobileUserDropdown.classList.add('hidden');
                }
            });
            
            // 滚动导航效果
            window.addEventListener('scroll', () => {
                if (window.scrollY > 20) {
                    DOM.navbar.classList.add('shadow');
                } else {
                    DOM.navbar.classList.remove('shadow');
                }
            });
        }

        // 初始化页面状态 - 优化逻辑
        function initPageState() {
            if (currentUser) {
                // 显示用户信息
                updateUserInfoDisplay();
                // 显示打卡页面
                showSection('checkin');
                // 加载打卡数据
                loadCheckinData();
                // 如果是管理员，更新统计
                if (currentUser.role === 'admin') {
                    updateAdminStats();
                }
            } else {
                // 未登录显示登录页
                showSection('login');
            }
        }

        // 工号输入实时匹配姓名 - 优化提示
        function handleEmployeeIdInput() {
            const inputId = DOM.employeeIdInput.value.trim();
            
            // 清空提示
            DOM.employeeNamePreview.classList.add('hidden');
            DOM.employeeIdError.classList.add('hidden');
            
            if (!inputId) return;
            
            // 管理员账号匹配
            if (inputId === adminAccount.id) {
                DOM.employeeNamePreview.textContent = `管理员账号（工号：${adminAccount.id}）`;
                DOM.employeeNamePreview.classList.remove('hidden');
                return;
            }
            
            // 员工匹配
            const matchedEmployee = employees.find(emp => emp.id === inputId);
            if (matchedEmployee) {
                DOM.employeeNamePreview.textContent = `匹配员工：${matchedEmployee.name}（工号：${matchedEmployee.id}）`;
                DOM.employeeNamePreview.classList.remove('hidden');
            } else {
                DOM.employeeIdError.classList.remove('hidden');
            }
        }

        // 登录处理 - 优化验证逻辑
        function handleLogin(e) {
            e.preventDefault();
            
            const inputId = DOM.employeeIdInput.value.trim();
            const password = DOM.passwordInput.value;
            
            // 空值验证
            if (!inputId || !password) {
                showNotification('登录失败', '请输入工号和密码', 'error');
                return;
            }
            
            // 管理员登录
            if (inputId === adminAccount.id) {
                if (password === adminAccount.password) {
                    currentUser = { ...adminAccount };
                    saveUserToLocalStorage();
                    updateUserInfoDisplay();
                    showSection('checkin');
                    loadCheckinData();
                    updateAdminStats();
                    showNotification('登录成功', `欢迎回来，${adminAccount.name}`, 'success');
                    DOM.loginForm.reset();
                } else {
                    showNotification('登录失败', '管理员密码错误（默认：admin123）', 'error');
                }
                return;
            }
            
            // 员工登录
            const matchedEmployee = employees.find(emp => emp.id === inputId);
            if (!matchedEmployee) {
                DOM.employeeIdError.classList.remove('hidden');
                showNotification('登录失败', '该工号不存在，请核对后重新输入', 'error');
                return;
            }
            
            // 密码验证（默认123456）
            if (password === '123456') {
                currentUser = {
                    id: matchedEmployee.id,
                    name: matchedEmployee.name,
                    role: 'employee'
                };
                saveUserToLocalStorage();
                updateUserInfoDisplay();
                showSection('checkin');
                loadCheckinData();
                showNotification('登录成功', `欢迎回来，${matchedEmployee.name}`, 'success');
                DOM.loginForm.reset();
            } else {
                showNotification('登录失败', '密码错误（员工默认密码：123456）', 'error');
            }
        }

        // 保存用户信息到本地存储 - 优化方法
        function saveUserToLocalStorage() {
            if (currentUser) {
                localStorage.setItem('currentUser', JSON.stringify(currentUser));
            }
        }

        // 更新用户信息显示 - 优化界面
        function updateUserInfoDisplay() {
            if (!currentUser) return;
            
            // 桌面端显示
            DOM.usernameDisplay.textContent = currentUser.name;
            DOM.userInfo.classList.remove('hidden');
            DOM.logoutBtns.desktop.classList.remove('hidden');
            
            // 移动端显示
            DOM.mobileUsername.textContent = currentUser.name;
            
            // 管理员权限显示
            if (currentUser.role === 'admin') {
                DOM.userRole.classList.remove('hidden');
                DOM.mobileUserRole.classList.remove('hidden');
                DOM.adminDashboardBtn.classList.remove('hidden');
                DOM.adminDashboardBtn.classList.add('flex');
            } else {
                DOM.userRole.classList.add('hidden');
                DOM.mobileUserRole.classList.add('hidden');
                DOM.adminDashboardBtn.classList.add('hidden');
                DOM.adminDashboardBtn.classList.remove('flex');
            }
        }

        // 切换密码可见性 - 优化交互
        function togglePasswordVisibility() {
            const isPassword = DOM.passwordInput.getAttribute('type') === 'password';
            const newType = isPassword ? 'text' : 'password';
            const iconClass = isPassword ? 'fa-eye' : 'fa-eye-slash';
            
            // 更新输入框类型
            DOM.passwordInput.setAttribute('type', newType);
            // 更新图标
            const icon = DOM.togglePassword.querySelector('i');
            icon.className = `fa ${iconClass}`;
        }

        // 登出处理 - 优化清理
        function handleLogout() {
            // 清除本地存储
            localStorage.removeItem('currentUser');
            // 重置状态
            currentUser = null;
            checkinStatus = {
                hasCheckedIn: false,
                hasCheckedOut: false,
                morningTime: null,
                eveningTime: null,
                morningRemark: "",
                eveningRemark: ""
            };
            // 显示登录页
            showSection('login');
            // 提示
            showNotification('已退出登录', '您已成功退出系统，欢迎下次使用', 'info');
        }

        // 打卡处理 - 优化逻辑
        function handleCheckin() {
            // 管理员无需打卡
            if (!currentUser || currentUser.role === 'admin') {
                showNotification('操作提示', currentUser ? '管理员无需打卡' : '请先登录', 'info');
                return;
            }
            
            const now = new Date();
            const hours = now.getHours();
            const minutes = now.getMinutes();
            const currentTimeStr = formatTime(now);
            const userRemark = DOM.userRemarkInput.value.trim();

            // 上班打卡
            if (!checkinStatus.hasCheckedIn) {
                const { status, remark } = checkMorningCheckinStatus(hours, minutes);
                
                // 更新状态
                checkinStatus.hasCheckedIn = true;
                checkinStatus.morningTime = currentTimeStr;
                checkinStatus.morningRemark = remark;
                
                // 更新UI
                updateCheckinStatusUI();
                
                // 保存记录
                saveCheckinRecord(userRemark);
                
                // 提示信息
                const fullRemark = userRemark ? `${remark}（员工备注：${userRemark}）` : remark;
                showNotification(
                    '上班打卡成功', 
                    `签到时间：${currentTimeStr} | ${fullRemark}`, 
                    status === 'normal' ? 'success' : 'warning'
                );
            } 
            // 下班打卡
            else if (!checkinStatus.hasCheckedOut) {
                const { status, remark } = checkEveningCheckoutStatus(hours, minutes);
                
                // 更新状态
                checkinStatus.hasCheckedOut = true;
                checkinStatus.eveningTime = currentTimeStr;
                checkinStatus.eveningRemark = remark;
                
                // 更新UI
                updateCheckinStatusUI();
                
                // 保存记录
                saveCheckinRecord(userRemark);
                
                // 计算工作时长
                const workHours = calculateWorkHours(checkinStatus.morningTime, currentTimeStr);
                
                // 提示信息
                const fullRemark = userRemark ? `${remark}（员工备注：${userRemark}）` : remark;
                showNotification(
                    '下班打卡成功', 
                    `签退时间：${currentTimeStr} | 工作时长：${workHours} | ${fullRemark}`, 
                    status === 'normal' ? 'success' : 'warning'
                );
            }
        }

        // 计算工作时长 - 新增方法
        function calculateWorkHours(morningTime, eveningTime) {
            if (!morningTime || !eveningTime) return '0小时';
            
            const [mHour, mMin] = morningTime.split(':').map(Number);
            const [eHour, eMin] = eveningTime.split(':').map(Number);
            
            const totalMinutes = (eHour * 60 + eMin) - (mHour * 60 + mMin);
            const hours = Math.floor(totalMinutes / 60);
            const minutes = totalMinutes % 60;
            
            if (minutes === 0) return `${hours}小时`;
            return `${hours}小时${minutes}分钟`;
        }

        // 更新打卡状态UI - 优化显示
        function updateCheckinStatusUI() {
            // 上班状态
            if (checkinStatus.hasCheckedIn) {
                DOM.morningCheckin.textContent = checkinStatus.morningTime;
                DOM.morningCheckin.classList.remove('text-gray-400');
                
                // 正常/迟到样式
                if (checkinStatus.morningRemark.includes('正常')) {
                    DOM.morningCheckin.classList.add('text-success');
                    DOM.morningRemark.classList.add('hidden');
                } else {
                    DOM.morningCheckin.classList.add('text-warning');
                    DOM.morningRemark.classList.remove('hidden');
                    DOM.morningRemark.querySelector('span').textContent = checkinStatus.morningRemark;
                }
                
                // 切换按钮为下班打卡
                DOM.checkinBtnText.textContent = '下班打卡';
                DOM.checkinBtn.classList.remove('bg-success');
                DOM.checkinBtn.classList.add('bg-primary');
                DOM.checkinTip.textContent = '17:00后点击完成下班签退';
            }
            
            // 下班状态
            if (checkinStatus.hasCheckedOut) {
                DOM.eveningCheckout.textContent = checkinStatus.eveningTime;
                DOM.eveningCheckout.classList.remove('text-gray-400');
                
                // 正常/早退样式
                if (checkinStatus.eveningRemark.includes('正常')) {
                    DOM.eveningCheckout.classList.add('text-success');
                    DOM.eveningRemark.classList.add('hidden');
                } else {
                    DOM.eveningCheckout.classList.add('text-danger');
                    DOM.eveningRemark.classList.remove('hidden');
                    DOM.eveningRemark.querySelector('span').textContent = checkinStatus.eveningRemark;
                }
                
                // 禁用按钮
                DOM.checkinBtnText.textContent = '今日已完成打卡';
                DOM.checkinBtn.disabled = true;
                DOM.checkinBtn.classList.remove('bg-primary', 'btn-hover');
                DOM.checkinBtn.classList.add('bg-gray-400', 'cursor-not-allowed');
                DOM.checkinTip.textContent = '今日打卡已完成，明天见！';
            }
        }

        // 检查上班打卡状态 - 优化逻辑
        function checkMorningCheckinStatus(hours, minutes) {
            const totalMinutes = hours * 60 + minutes;
            const normalEnd = 8 * 60 + 30; // 8:30
            const lateEnd = 9 * 60;        // 9:00
            
            if (totalMinutes <= normalEnd) {
                return { status: 'normal', remark: '正常打卡' };
            } else if (totalMinutes <= lateEnd) {
                const lateMinutes = totalMinutes - normalEnd;
                return { status: 'late', remark: `迟到${lateMinutes}分钟` };
            } else {
                return { status: 'seriousLate', remark: '严重迟到，扣半天工资' };
            }
        }

        // 检查下班打卡状态 - 优化逻辑
        function checkEveningCheckoutStatus(hours, minutes) {
            const totalMinutes = hours * 60 + minutes;
            const normalStart = 17 * 60; // 17:00
            
            if (totalMinutes >= normalStart) {
                return { status: 'normal', remark: '正常打卡' };
            } else {
                const earlyMinutes = normalStart - totalMinutes;
                return { status: 'earlyLeave', remark: `早退${earlyMinutes}分钟` };
            }
        }
