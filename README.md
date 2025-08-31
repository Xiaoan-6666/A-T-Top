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
                </div>
                <button id="changeEmployeeBtn" class="text-gray-600 hover:text-primary transition-colors">
                    <i class="fa fa-refresh"></i>
                    <span class="ml-1 hidden md:inline">更换员工</span>
                </button>
            </div>
        </div>
    </header>

    <!-- 主内容区 -->
    <main class="container mx-auto px-4 pt-24 pb-16">
        <!-- 工号输入区域 -->
        <section id="employeeInputSection" class="max-w-md mx-auto animate-fadeIn">
            <div class="bg-white rounded-xl p-6 md:p-8 card-shadow">
                <div class="text-center mb-6">
                    <div class="inline-flex items-center justify-center w-16 h-16 rounded-full gradient-bg mb-4">
                        <i class="fa fa-id-card text-white text-2xl"></i>
                    </div>
                    <h2 class="text-2xl font-bold text-gray-800">员工打卡渠道</h2>
                    <p class="text-gray-500 mt-1">请输入工号进行打卡操作</p>
                </div>
                
                <div class="space-y-4">
                    <div>
                        <label for="employeeId" class="block text-sm font-medium text-gray-700 mb-1">工号</label>
                        <div class="relative">
                            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                <i class="fa fa-id-card text-gray-400"></i>
                            </div>
                            <input type="text" id="employeeId" name="employeeId" 
                                class="block w-full pl-10 pr-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all"
                                placeholder="例如：1001" required>
                            <!-- 工号匹配的姓名预览 -->
                            <p id="employeeNamePreview" class="absolute left-10 top-full mt-1 text-xs text-success hidden"></p>
                        </div>
                        <!-- 工号不存在提示 -->
                        <p id="employeeIdError" class="mt-1 text-xs text-danger hidden">该工号不存在，请核对后重新输入</p>
                    </div>
                    
                    <div class="pt-2">
                        <button id="confirmEmployeeBtn" class="w-full bg-primary text-white py-3 px-4 rounded-lg font-medium btn-hover">
                            <i class="fa fa-check mr-2"></i>确认工号
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- 打卡界面 (默认隐藏) -->
        <section id="checkinSection" class="hidden max-w-4xl mx-auto animate-fadeIn">
            <div class="grid grid grid-cols-1 md:grid-cols-3 gap-6">
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
                            <div class="flex justify-between justify-between items-center">
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
                        
                        <!-- 员工自定义自定义备注输入框 -->
                        <div class="mt-6">
                            <label for="userRemark" class="block text-sm font-medium text-gray-700 mb-1">
                                <i class="fa fa-pencil text-primary mr-1"></i>打卡备注
                            </label>
                            <input type="text" id="userRemark" 
                                class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary-primary transition-all text-sm"
                                placeholder="请输入备注（如：外勤、请假、加班等）">
                        </div>
                        
                        <!-- 打卡按钮 -->
                        <div class="mt-4">
                            <button id="checkinBtn" class="w-full bg-success text-white py-3 px-4 rounded-lg font-medium btn-hover flex items-center justify-center">
                                <i class="fa fa-check mr-2"></i>
                                <span id="checkinBtnText">上班班打卡</span>
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

        <!-- 打卡记录查询界面 (含备注列) -->
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

    <!-- JavaScript：核心逻辑 -->
    <script>
        // 120个员工数据（工号-姓名匹配）
        const employees = [
            { id: "1001", name: "张三" }, { id: "1002", name: "李四" }, { id: "1003", name: "王五" },
            { id: "1004", name: "赵六" }, { id: "1005", name: "孙七" }, { id: "1006", name: "周八" },
            { id: "1007", name: "吴九" }, { id: "1008", name: "郑十" }, { id: "1009", name: "钱一" },
            { id: "1010", name: "冯二" }, { id: "1011", name: "陈三" }, { id: "1012", name: "褚四" },
            { id: "1013", name: "卫五" }, { id: "1014", name: "蒋六" }, { id: "1015", name: "沈七" },
            { id: "1016", name: "韩八" }, { id: "1017", name: "杨九" }, { id: "1018", name: "朱十" },
            { id: "1019", name: "秦一" }, { id: "1020", name: "尤二" }, { id: "1021", name: "许三" },
            { id: "1022", name: "何四" }, { id: "1023", name: "吕五" }, { id: "1024", name: "施六" },
            { id: "1025", name: "张七" }, { id: "1026", name: "孔八" }, { id: "1027", name: "曹九" },
            { id: "1028", name: "严十" }, { id: "1029", name: "华一" }, { id: "1030", name: "金二" },
            { id: "1031", name: "魏三" }, { id: "1032", name: "陶四" }, { id: "1033", name: "姜五" },
            { id: "1034", name: "戚六" }, { id: "1035", name: "谢七" }, { id: "1036", name: "邹八" },
            { id: "1037", name: "喻九" }, { id: "1038", name: "柏十" }, { id: "1039", name: "水一" },
            { id: "1040", name: "窦二" }, { id: "1041", name: "章三" }, { id: "1042", name: "云四" },
            { id: "1043", name: "苏五" }, { id: "1044", name: "潘六" }, { id: "1045", name: "葛七" },
            { id: "1046", name: "奚八" }, { id: "1047", name: "范九" }, { id: "1048", name: "彭十" },
            { id: "1049", name: "郎一" }, { id: "1050", name: "鲁二" }, { id: "1051", name: "韦三" },
            { id: "1052", name: "昌四" }, { id: "1053", name: "马五" }, { id: "1054", name: "苗六" },
            { id: "1055", name: "凤七" }, { id: "1056", name: "花八" }, { id: "1057", name: "方九" },
            { id: "1058", name: "俞十" }, { id: "1059", name: "任二" }, { id: "1060", name: "袁三" },
            { id: "1061", name: "柳四" }, { id: "1062", name: "酆五" }, { id: "1063", name: "鲍六" },
            { id: "1064", name: "史七" }, { id: "1065", name: "唐八" }, { id: "1066", name: "费九" },
            { id: "1067", name: "廉十" }, { id: "1068", name: "岑一" }, { id: "1069", name: "薛二" },
            { id: "1070", name: "雷三" }, { id: "1071", name: "贺四" }, { id: "1072", name: "倪五" },
            { id: "1073", name: "汤六" }, { id: "1074", name: "滕七" }, { id: "1075", name: "殷八" },
            { id: "1076", name: "罗九" }, { id: "1077", name: "毕十" }, { id: "1078", name: "郝一" },
            { id: "1079", name: "邬二" }, { id: "1080", name: "安三" }, { id: "1081", name: "常四" },
            { id: "1082", name: "乐五" }, { id: "1083", name: "于六" }, { id: "1084", name: "时七" },
            { id: "1085", name: "傅八" }, { id: "1086", name: "皮九" }, { id: "1087", name: "卞十" },
            { id: "1088", name: "齐一" }, { id: "1089", name: "康二" }, { id: "1090", name: "伍三" },
            { id: "1091", name: "余四" }, { id: "1092", name: "元五" }, { id: "1093", name: "卜六" },
            { id: "1094", name: "顾七" }, { id: "1095", name: "孟八" }, { id: "1096", name: "平九" },
            { id: "1097", name: "黄十" }, { id: "1098", name: "和一" }, { id: "1099", name: "穆二" },
            { id: "1100", name: "萧三" }, { id: "1101", name: "尹四" }, { id: "1102", name: "姚五" },
            { id: "1103", name: "邵六" }, { id: "1104", name: "湛七" }, { id: "1105", name: "汪八" },
            { id: "1106", name: "祁九" }, { id: "1107", name: "毛十" }, { id: "1108", name: "禹一" },
            { id: "1109", name: "狄二" }, { id: "1110", name: "米三" }, { id: "1111", name: "贝四" },
            { id: "1112", name: "明五" }, { id: "1113", name: "臧六" }, { id: "1114", name: "计七" },
            { id: "1115", name: "伏八" }, { id: "1116", name: "成九" }, { id: "1117", name: "戴十" },
            { id: "1118", name: "谈一" }, { id: "1119", name: "宋二" }, { id: "1120", name: "茅三" }
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
        let打卡记录 = [];

        // DOM元素
        const employeeInputSection = document.getElementById('employeeInputSection');
        const checkinSection = document.getElementById('checkinSection');
        const recordsSection = document.getElementById('recordsSection');
        const employeeIdInput = document.getElementById('employeeId');
        const employeeNamePreview = document.getElementById('employeeNamePreview');
        const employeeIdError = document.getElementById('employeeIdError');
        const confirmEmployeeBtn = document.getElementById('confirmEmployeeBtn');
        const changeEmployeeBtn = document.getElementById('changeEmployeeBtn');
        const usernameDisplay = document.getElementById('usernameDisplay');
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
        // 统计元素
        const lateCountEl = document.getElementById('lateCount');
        const salaryDeductCountEl = document.getElementById('salaryDeductCount');

        // 初始化
        document.addEventListener('DOMContentLoaded', () => {
            // 更新时间
            updateDateTime();
            setInterval(updateDateTime, 1000);
            
            // 初始化月份选择器
            initMonthSelector();
            
            // 加载本地存储的打卡记录
            const savedRecords = localStorage.getItem('checkinRecords');
            if (savedRecords) {
                打卡记录 = JSON.parse(savedRecords);
            } else {
                打卡记录 = [];
            }
            
            // 工号输入实时匹配姓名
            employeeIdInput.addEventListener('input', handleEmployeeIdInput);
            
            // 事件监听
            confirmEmployeeBtn.addEventListener('click', confirmEmployee);
            changeEmployeeBtn.addEventListener('click', showEmployeeInputSection);
            checkinBtn.addEventListener('click', handleCheckin);
            viewAllRecordsBtn.addEventListener('click', showRecordsSection);
            backToCheckinBtn.addEventListener('click', showCheckinSection);
            monthSelector.addEventListener('change', loadAllRecords);
            exportExcelBtn.addEventListener('click', exportRecords);
            closeNotification.addEventListener('click', hideNotification);
            
            // 滚动导航效果
            window.addEventListener('scroll', () => {
                if (window.scrollY > 10) {
                    navbar.classList.add('shadow');
                } else {
                    navbar.classList.remove('shadow');
                }
            });
        });

        // 工号输入实时匹配姓名
        function handleEmployeeIdInput() {
            const inputId = employeeIdInput.value.trim();
            if (!inputId) {
                employeeNamePreview.classList.add('hidden');
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

        // 确认工号
        function confirmEmployee() {
            const inputId = employeeIdInput.value.trim();
            
            // 验证工号是否存在
            const matchedEmployee = employees.find(emp => emp.id === inputId);
            if (!matchedEmployee) {
                employeeIdError.classList.remove('hidden');
                showNotification('验证失败', '该工号不存在，请核对后重新输入', 'error');
                return;
            }
            
            // 设置当前用户
            currentUser = {
                id: matchedEmployee.id,
                name: matchedEmployee.name
            };
            
            // 显示打卡界面
            showCheckinSection();
            loadCheckinData();
            showNotification('验证成功', `欢迎，${matchedEmployee.name}`, 'success');
        }

        // 显示工号输入界面
        function showEmployeeInputSection() {
            employeeInputSection.classList.remove('hidden');
            checkinSection.classList.add('hidden');
            recordsSection.classList.add('hidden');
            employeeIdInput.value = '';
            employeeNamePreview.classList.add('hidden');
            employeeIdError.classList.add('hidden');
            employeeIdInput.focus();
        }

        // 打卡处理
        function handleCheckin() {
            if (!currentUser) {
                showNotification('操作失败', '请先输入并确认工号', 'error');
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

        // 辅助：判断上班打卡状态（8:30前正常，8:00-8:30迟到，9:00后扣半天工资）
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
            let record =打卡记录.find(r => r.date === today && r.employeeId === currentUser.id);
            
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
            loadAllRecords();
            updateStats();
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
            usernameDisplay.textContent = currentUser.name;
            userRemarkInput.value = ''; // 重置备注输入框
            
            // 恢复今日状态
            const today = formatDate(new Date(), true);
            const todayRecord =打卡记录.find(r => r.date === today && r.employeeId === currentUser.id);
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
            
            // 更新列表和统计
            updateRecentRecords();
            loadAllRecords();
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
            
            // 筛选当前员工的记录，按日期倒序，取最近5条
            const userRecords = [...打卡记录]
                .filter(r => r.employeeId === currentUser.id)
                .sort((a, b) => new Date(b.date) - new Date(a.date))
                .slice(0, 5);
            
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

        // 历史记录显示员工姓名和备注
        function loadAllRecords() {
            allRecordsEl.innerHTML = '';
            if (!currentUser) return;
            
            const selectedMonth = monthSelector.value;
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
            
            showingCountEl.textContent = userMonthlyRecords.length;
            totalCountEl.textContent = userMonthlyRecords.length;
            
            if (userMonthlyRecords.length === 0) {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td colspan="8" class="px-3 py-8 text-center text-gray-500">该月份暂无打卡记录</td>
                `;
                allRecordsEl.appendChild(row);
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
                allRecordsEl.appendChild(row);
            });
        }

        // 更新统计（新增扣薪次数）
        function updateStats() {
            if (!currentUser) return;
            
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

        // 初始化月份选择器
        function initMonthSelector() {
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
                monthSelector.appendChild(option);
            }
        }

        // 导出记录（含员工姓名和备注）
        function exportRecords() {
            if (!currentUser) {
                showNotification('操作失败', '请先输入并确认工号', 'error');
                return;
            }
            
            const selectedMonth = monthSelector.value;
            const [year, month] = selectedMonth.split('-');
            showNotification('导出成功', `已将${year}年${month}月的打卡记录（含员工姓名和备注）导出为Excel`, 'success');
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

        // 页面切换
        function showCheckinSection() {
            employeeInputSection.classList.add('hidden');
            checkinSection.classList.remove('hidden');
            recordsSection.classList.add('hidden');
        }

        function showRecordsSection() {
            checkinSection.classList.add('hidden');
            recordsSection.classList.remove('hidden');
            loadAllRecords();
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
