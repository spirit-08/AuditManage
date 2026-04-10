# AuditManage
审核项目跟踪

<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>工作台 · 审核项目 · 智能备忘</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: system-ui, -apple-system, 'Segoe UI', Roboto, 'Helvetica Neue', sans-serif; }
        body { background: linear-gradient(145deg, #f0f4f8 0%, #e6ecf3 100%); min-height: 100vh; padding: 0.8rem; }
        .nav-header, .dash-card, .audit-panel-full, .nav-panel-full, .memo-panel-full, .site-card, .audit-item, .todo-item {
            background: rgba(255, 255, 255, 0.7);
            backdrop-filter: blur(12px);
            background-color: rgba(255, 255, 255, 0.75);
        }
        @supports (backdrop-filter: blur(12px)) {
            .nav-header, .dash-card, .audit-panel-full, .nav-panel-full, .memo-panel-full, .site-card, .audit-item, .todo-item {
                background: rgba(255, 255, 255, 0.6);
                backdrop-filter: blur(12px);
            }
        }
        .nav-header { border-radius: 2rem; padding: 0.6rem 1.2rem; margin-bottom: 1rem; display: flex; align-items: center; justify-content: space-between; flex-wrap: wrap; gap: 0.8rem; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
        .logo { display: flex; align-items: center; gap: 0.5rem; }
        .logo i { font-size: 1.6rem; color: #1e2b3c; }
        .logo h1 { font-size: 1.1rem; font-weight: 600; color: #1e2b3c; }
        .datetime-display { background: linear-gradient(135deg, #1e2b3c, #2c3e50); color: white; padding: 0.3rem 1rem; border-radius: 2rem; font-size: 0.75rem; font-weight: 500; text-align: center; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
        .datetime-display .date { font-size: 0.9rem; font-weight: bold; }
        .datetime-display .time { font-size: 1.1rem; font-weight: bold; font-family: monospace; }
        .nav-tabs { display: flex; gap: 0.4rem; background: rgba(0,0,0,0.05); padding: 0.25rem; border-radius: 3rem; flex-wrap: wrap; justify-content: center; }
        .nav-tab { padding: 0.5rem 1rem; border-radius: 2rem; border: none; background: transparent; cursor: pointer; font-size: 0.85rem; font-weight: 500; color: #2c3e50; transition: all 0.2s; white-space: nowrap; }
        @media (max-width: 640px) { .nav-tab { padding: 0.5rem 0.8rem; font-size: 0.75rem; } }
        .nav-tab.active { background: #1e2b3c; color: white; box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
        .stats-badge { background: #e6b800; color: #1e2b3c; padding: 0.3rem 0.9rem; border-radius: 2rem; font-size: 0.7rem; font-weight: 500; }
        .view-container { min-height: calc(100vh - 100px); }
        .view { display: none; animation: fadeIn 0.25s ease; }
        .view.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(6px); } to { opacity: 1; transform: translateY(0); } }
        .dashboard-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 1.2rem; margin-bottom: 1.5rem; }
        .dash-card { border-radius: 1.5rem; padding: 1.2rem; transition: all 0.2s ease; cursor: pointer; border: 1px solid rgba(255,255,255,0.6); }
        .dash-card:active { transform: scale(0.98); background: rgba(255,255,255,0.85);}
        .dash-card-header { display: flex; align-items: center; gap: 0.8rem; margin-bottom: 1rem; }
        .dash-card-header i { font-size: 2rem; }
        .dash-number { font-size: 2rem; font-weight: 700; color: #1e2b3c; }
        .dash-list li { padding: 0.4rem 0; border-bottom: 1px solid rgba(0,0,0,0.05); font-size: 0.75rem; display: flex; justify-content: space-between; }
        .audit-panel-full, .nav-panel-full, .memo-panel-full { border-radius: 1.8rem; padding: 1.2rem; box-shadow: 0 20px 35px -12px rgba(0,0,0,0.15); overflow-y: auto; max-height: calc(100vh - 130px); -webkit-overflow-scrolling: touch; }
        .section-title { display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 0.6rem; margin-bottom: 1.2rem; }
        .section-title h2 { font-size: 1.25rem; font-weight: 600; background: linear-gradient(135deg, #1e2b3c, #2c3e50); -webkit-background-clip: text; background-clip: text; color: transparent; display: inline-flex; align-items: center; gap: 0.5rem; }
        .category-tabs-container { display: flex; gap: 0.5rem; margin-bottom: 1rem; flex-wrap: wrap; align-items: center; }
        .category-tabs { display: flex; flex-wrap: wrap; gap: 0.4rem; flex: 1; }
        .tab-btn { background: rgba(0,0,0,0.05); border: none; padding: 0.4rem 1rem; border-radius: 2rem; font-size: 0.75rem; cursor: pointer; transition: all 0.2s; min-height: 34px; }
        .tab-btn.active { background: #1e2b3c; color: white; }
        .add-category-btn { width: 34px; height: 34px; border-radius: 50%; background: rgba(255,255,255,0.9); border: 2px dashed #1e2b3c; display: flex; align-items: center; justify-content: center; cursor: pointer; font-weight: bold; }
        .sites-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(105px, 1fr)); gap: 0.8rem; }
        .site-card { background: rgba(255,255,255,0.7); border-radius: 1.2rem; padding: 0.7rem 0.3rem; text-align: center; cursor: pointer; position: relative; transition: 0.1s; }
        .site-card:active { transform: scale(0.97); background: white; }
        .delete-btn { position: absolute; top: 4px; left: 4px; width: 24px; height: 24px; border-radius: 50%; background: rgba(220,38,38,0.9); color: white; font-size: 12px; display: flex; align-items: center; justify-content: center; cursor: pointer; opacity: 0; transition: 0.1s; z-index: 2; }
        .site-card:hover .delete-btn, .site-card:active .delete-btn { opacity: 0.9; }
        @media (max-width: 768px) { .delete-btn { opacity: 0.7; width: 28px; height: 28px; font-size: 14px; } }
        .card-icon { width: 100%; min-height: 48px; display: flex; align-items: center; justify-content: center; margin: 0 auto 0.4rem; font-size: 1.8rem; background: rgba(0,0,0,0.02); border-radius: 1rem; overflow: hidden; }
        .card-icon img { max-width: 48px; max-height: 48px; width: auto; height: auto; object-fit: contain; border-radius: 0.5rem; }
        .card-icon i, .card-icon span { font-size: 1.8rem; }
        .site-title { font-weight: 600; font-size: 0.7rem; word-break: break-word; }
        .add-panel { background: rgba(255,255,255,0.5); border-radius: 1.2rem; padding: 0.8rem; margin-top: 1.2rem; }
        .add-form { display: flex; gap: 0.6rem; flex-wrap: wrap; }
        .add-form input, .add-form select, .add-form button { padding: 0.5rem 0.8rem; border-radius: 2rem; border: 1px solid rgba(0,0,0,0.1); font-size: 0.8rem; background: white; }
        .toolbar { display: flex; flex-wrap: wrap; gap: 0.5rem; align-items: center; }
        .toolbar button { background: rgba(44,62,80,0.1); padding: 0.4rem 0.8rem; border-radius: 2rem; font-size: 0.7rem; min-height: 34px; cursor: pointer; }
        .audit-item { background: rgba(255,255,255,0.85); border-radius: 1rem; padding: 0.9rem; margin-bottom: 0.8rem; border-left: 4px solid #1e2b3c; display: flex; align-items: flex-start; gap: 0.8rem; flex-wrap: wrap; }
        .audit-checkbox { width: 22px; height: 22px; margin-top: 0.2rem; cursor: pointer; }
        .audit-content { flex: 1; }
        .metric { background: rgba(0,0,0,0.04); border-radius: 2rem; padding: 0.2rem 0.7rem; font-size: 0.7rem; display: inline-block; margin: 0.2rem 0.3rem 0 0; }
        .metric.green { background: #c8e6c9; color: #2e7d32; }
        .metric.yellow { background: #fff9c4; color: #b26500; }
        .metric.red { background: #ffcdd2; color: #c62828; }
        .todo-item { background: rgba(255,255,255,0.85); border-radius: 1rem; padding: 0.8rem; margin-bottom: 0.7rem; border-left: 3px solid #4caf50; display: flex; align-items: flex-start; gap: 0.8rem; flex-wrap: wrap; }
        .todo-checkbox { width: 22px; height: 22px; margin-top: 0.2rem; cursor: pointer; }
        .todo-content { flex: 1; }
        .todo-time { font-size: 0.7rem; margin-top: 0.2rem; }
        .todo-time.normal { color: #666; }
        .todo-time.warning { color: #ed6c02; font-weight: bold; }
        .todo-time.urgent { color: #c62828; font-weight: bold; background: #ffcdd2; display: inline-block; padding: 0.1rem 0.4rem; border-radius: 1rem; }
        .todo-time.overdue { color: #c62828; font-weight: bold; background: #ffcdd2; display: inline-block; padding: 0.1rem 0.4rem; border-radius: 1rem; }
        .complete-btn, .delete-todo-btn, .edit-todo-btn, .delay-todo-btn { border: none; padding: 0.3rem 0.9rem; border-radius: 2rem; font-size: 0.7rem; cursor: pointer; min-width: 54px; margin-right: 0.3rem; }
        .complete-btn { background: #4caf50; color: white; }
        .delete-todo-btn { background: #f44336; color: white; }
        .edit-todo-btn { background: #ff9800; color: white; }
        .delay-todo-btn { background: #2196f3; color: white; }
        .batch-bar { display: flex; gap: 0.8rem; margin-bottom: 1rem; flex-wrap: wrap; align-items: center; }
        .select-all-btn, .batch-btn, .setting-btn, .btn-primary { background: #4a5568; color: white; border: none; padding: 0.4rem 1rem; border-radius: 2rem; font-size: 0.75rem; cursor: pointer; min-height: 38px; }
        .btn-primary { background: #1e2b3c; }
        .setting-btn { background: rgba(44,62,80,0.2); color: #1e2b3c; }
        .todo-form { background: rgba(255,255,255,0.5); border-radius: 1.2rem; padding: 1rem; margin-bottom: 1rem; }
        .form-row { margin-bottom: 0.6rem; display: flex; flex-direction: column; gap: 0.3rem; }
        .form-row input, .todo-form input, .todo-form button { padding: 0.6rem; border-radius: 2rem; border: 1px solid #ccc; }
        .datetime-row { display: flex; gap: 0.8rem; flex-wrap: wrap; }
        .modal { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); backdrop-filter: blur(6px); display: flex; align-items: center; justify-content: center; z-index: 2000; visibility: hidden; opacity: 0; transition: 0.2s; }
        .modal.show { visibility: visible; opacity: 1; }
        .modal-content { background: white; border-radius: 1.8rem; padding: 1.2rem; max-width: 550px; width: 90%; max-height: 80vh; overflow-y: auto; -webkit-overflow-scrolling: touch; }
        .batch-textarea { width: 100%; height: 160px; padding: 0.6rem; border-radius: 1rem; border: 1px solid #ddd; }
        .icon-selector-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(70px, 1fr)); gap: 0.5rem; max-height: 260px; overflow-y: auto; padding: 0.5rem; border: 1px solid #eee; border-radius: 1rem; }
        .icon-option { text-align: center; padding: 0.5rem; cursor: pointer; border-radius: 0.8rem; }
        .icon-option i { font-size: 1.5rem; }
        .reminder-list { list-style: none; }
        .reminder-item { padding: 0.6rem; border-bottom: 1px solid rgba(0,0,0,0.05); font-size: 0.85rem; }
        .reminder-item.urgent { color: #c62828; font-weight: 600; background: rgba(198,40,40,0.08); }
        .reminder-item.overdue { background: #ffcdd2; color: #c62828; font-weight: bold; border-left: 3px solid #c62828; }
        .reminder-item.warning { background: #fff9c4; color: #b26500; font-weight: bold; border-left: 3px solid #ed6c02; }
        .footer-note { font-size: 0.6rem; color: #4a6272; text-align: center; margin-top: 1rem; padding-top: 0.6rem; }
        .empty-message { text-align: center; color: #6b8ba0; padding: 1.5rem; }
        .history-badge { background: #e0e7ef; border-radius: 1rem; padding: 0.2rem 0.6rem; font-size: 0.65rem; margin-left: 0.5rem; }
        .remark-badge { background: #e3f2fd; border-radius: 1rem; padding: 0.2rem 0.6rem; font-size: 0.65rem; margin-left: 0.5rem; cursor: pointer; }
        .remark-badge:hover { background: #bbdef5; }
        .remark-text { font-size: 0.7rem; color: #1565c0; margin-top: 0.3rem; padding: 0.2rem 0.5rem; background: rgba(21,101,192,0.05); border-radius: 0.5rem; display: inline-block; }
        .todo-prefix-plan { color: #1976D2; font-weight: 700; }
        .todo-prefix-report { color: #2e7d32; font-weight: 700; }
        .todo-prefix-corrective { color: #ed6c02; font-weight: 700; }
        .todo-prefix-review { color: #00bcd4; font-weight: 700; }
        .todo-prefix-custom { color: #9c27b0; font-weight: 700; }
        .icon-library-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(80px, 1fr)); gap: 0.8rem; max-height: 300px; overflow-y: auto; margin-bottom: 1rem; }
        .icon-library-item { text-align: center; padding: 0.5rem; cursor: pointer; border-radius: 0.8rem; border: 1px solid #eee; transition: all 0.2s; }
        .icon-library-item:hover { background: #e3f2fd; transform: scale(1.05); }
        .icon-library-item i { font-size: 2rem; }
        .icon-library-item span { display: block; font-size: 0.6rem; margin-top: 0.3rem; word-break: break-all; }
        .icon-library-item img { max-width: 40px; max-height: 40px; object-fit: contain; }
        @media (max-width: 640px) {
            body { padding: 0.5rem; }
            .nav-header { padding: 0.6rem; }
            .logo h1 { font-size: 0.95rem; }
            .datetime-display { font-size: 0.65rem; padding: 0.2rem 0.6rem; }
            .datetime-display .time { font-size: 0.9rem; }
            .add-form input, .add-form select { font-size: 0.7rem; flex: 1 1 auto; }
            .dash-number { font-size: 1.6rem; }
            .section-title h2 { font-size: 1.1rem; }
            .audit-item, .todo-item { padding: 0.7rem; }
            .metric { font-size: 0.65rem; }
            button, .tab-btn, .select-all-btn { min-height: 40px; }
            .site-title { font-size: 0.65rem; }
        }
    </style>
</head>
<body>
<body>
<!-- ========== 密码保护开始 ========== -->
<script>
(function(){
    // 设置您的密码（可以修改成您喜欢的）
    const CORRECT_PASSWORD = 'xbdxyy';
    
    // 检查是否已验证通过
    if (sessionStorage.getItem('authenticated') === 'true') {
        // 已通过验证，正常显示页面
        return;
    }
    
    // 提示输入密码
    let userPassword = prompt('🔐 请输入访问密码：', '');
    
    if (userPassword === CORRECT_PASSWORD) {
        // 密码正确，记录验证状态（当前标签页有效）
        sessionStorage.setItem('authenticated', 'true');
        // 刷新页面以加载完整内容
        location.reload();
    } else {
        // 密码错误，显示拒绝访问并阻止页面显示
        document.documentElement.innerHTML = `
            <div style="display:flex;align-items:center;justify-content:center;height:100vh;background:linear-gradient(145deg,#f0f4f8 0%,#e6ecf3 100%);font-family:system-ui,sans-serif;">
                <div style="background:rgba(255,255,255,0.9);border-radius:2rem;padding:2rem;text-align:center;box-shadow:0 20px 35px -12px rgba(0,0,0,0.2);">
                    <div style="font-size:4rem;">🔒</div>
                    <h2 style="color:#c62828;margin:1rem 0;">访问被拒绝</h2>
                    <p style="color:#4a6272;">密码错误，无法访问此页面</p>
                    <button onclick="location.reload()" style="margin-top:1rem;padding:0.5rem 1.5rem;background:#1e2b3c;color:white;border:none;border-radius:2rem;cursor:pointer;">重新输入密码</button>
                </div>
            </div>
        `;
        // 阻止后续脚本执行
        throw new Error('密码错误');
    }
})();
</script>
<!-- ========== 密码保护结束 ========== -->


























<div class="nav-header">
    <div class="logo"><i class="fas fa-rocket"></i><h1>工作台 Portal</h1></div>
    <div class="datetime-display" id="datetimeDisplay">
        <div class="date">加载中...</div>
        <div class="time"></div>
    </div>
    <div class="nav-tabs">
        <button class="nav-tab" data-view="dashboard">🏠 首页</button>
        <button class="nav-tab" data-view="nav">🧭 工作导航</button>
        <button class="nav-tab" data-view="audit">📋 审核项目</button>
        <button class="nav-tab" data-view="memo">📝 备忘录</button>
    </div>
    <div class="stats-badge" id="globalStats">加载中...</div>
</div>

<div class="view-container">
    <div class="view" id="view-dashboard">
        <div class="dashboard-grid">
            <div class="dash-card" data-view="nav"><div class="dash-card-header"><i class="fas fa-th-large"></i><h3>工作导航</h3></div><div class="dash-number" id="dashSiteCount">0</div><div>个收藏站点</div><div class="dash-list" id="dashRecentSites"></div></div>
            <div class="dash-card" data-view="audit"><div class="dash-card-header"><i class="fas fa-clipboard-list"></i><h3>审核项目</h3></div><div class="dash-number" id="dashAuditCount">0</div><div>个进行中项目</div><div class="dash-list" id="dashOverdueAudits"></div></div>
            <div class="dash-card" data-view="memo"><div class="dash-card-header"><i class="fas fa-tasks"></i><h3>待办事项</h3></div><div class="dash-number" id="dashTodoCount">0</div><div>个待办任务</div><div class="dash-list" id="dashUpcomingTodos"></div></div>
        </div>
        <div class="audit-panel-full" style="background: rgba(255,255,255,0.5);">
            <div class="section-title"><h2><i class="fas fa-bell"></i> 近期提醒<button id="reminderThresholdBtn" class="setting-btn" title="设置紧急提醒阈值">⚙️ 阈值</button></h2></div>
            <div id="dashboardReminders"></div>
        </div>
    </div>

    <div class="view" id="view-nav">
        <div class="nav-panel-full">
            <div class="section-title"><h2><i class="fas fa-th-large"></i> 工作导航</h2><div class="search-box"><input type="text" placeholder="搜索站点..." id="navSearchInput"></div></div>
            <div class="category-tabs-container"><div class="category-tabs" id="navTabContainer"></div><div class="add-category-btn" id="navAddCategoryBtn" title="添加新栏目">+</div></div>
            <div class="sites-grid" id="navSitesGrid"></div>
            <div class="add-panel">
                <div class="add-form">
                    <input type="text" placeholder="名称" id="navSiteName">
                    <input type="text" placeholder="网址" id="navSiteUrl" value="https://">
                    <input type="text" placeholder="图标" id="navSiteIcon" value="🌐">
                    <select id="navSiteCategory"></select>
                    <button id="navAddSiteBtn" style="background:#1e2b3c;color:white;border:none;">➕ 添加</button>
                </div>
                <div class="toolbar">
                    <button id="navBatchImportBtn"><i class="fas fa-file-import"></i> 导入备份</button>
                    <button id="navBatchExportBtn"><i class="fas fa-file-export"></i> 导出备份</button>
                    <button id="navResetBtn"><i class="fas fa-undo-alt"></i> 重置</button>
                    <button id="openIconLibraryBtn" style="background:#4caf50; color:white;"><i class="fas fa-icons"></i> 图标库</button>
                </div>
            </div>
            <div class="footer-note">💡 双击分类重命名 | 右键/长按图标可更换 | 图标库支持批量上传Emoji/FA/图片</div>
        </div>
    </div>

    <div class="view" id="view-audit">
        <div class="audit-panel-full">
            <div class="section-title"><h2><i class="fas fa-clipboard-list"></i> 审核项目管理</h2><div id="auditStats"></div></div>
            <div class="batch-bar">
                <button id="auditSelectAllBtn" class="select-all-btn">全选</button>
                <button id="auditBatchDeleteBtn" class="batch-btn">🗑️ 批量删除</button>
                <button id="smartImportAuditBtn" class="setting-btn" style="background:#1e2b3c; color:white;"><i class="fas fa-magic"></i> 智能导入</button>
                <button id="exportAuditBtn" class="setting-btn" style="background:#4a5568; color:white;"><i class="fas fa-download"></i> 导出项目</button>
                <button id="exportCsvBtn" class="setting-btn" style="background:#4caf50; color:white;"><i class="fas fa-file-excel"></i> 导出CSV</button>
                <button id="backupNowBtn" class="setting-btn" title="手动备份"><i class="fas fa-database"></i> 备份</button>
                <button id="restoreBackupBtn" class="setting-btn" title="恢复备份"><i class="fas fa-undo-alt"></i> 恢复</button>
            </div>
            <div id="auditListContainer" class="audit-list"></div>
            <div class="footer-note">📅 计划提交 = 审核开始-15天 | 📄 报告提交 = 审核结束+7天 | 🔧 纠正措施 = 审核结束+30天 | ✏️ 点击备注图标可添加备注</div>
        </div>
    </div>

    <div class="view" id="view-memo">
        <div class="audit-panel-full">
            <div class="section-title"><h2><i class="fas fa-tasks"></i> 备忘录</h2><button id="showHistoryBtn" class="setting-btn">📜 历史事项</button></div>
            <div class="todo-form">
                <div class="form-row"><label>待办内容</label><input type="text" id="todoTitle" placeholder="例如：周报提交"></div>
                <div class="datetime-row"><div class="form-row" style="flex:1;"><label>开始时间</label><input type="datetime-local" id="todoStart"></div><div class="form-row" style="flex:1;"><label>截止时间</label><input type="datetime-local" id="todoEnd"></div></div>
                <button class="btn-primary" id="addTodoBtn">➕ 添加待办</button>
            </div>
            <div class="batch-bar">
                <button id="todoSelectAllBtn" class="select-all-btn">全选</button>
                <button id="todoBatchDeleteBtn" class="batch-btn">🗑️ 批量删除</button>
                <button id="importMemoBtn" class="setting-btn" style="background:#4a5568; color:white;"><i class="fas fa-file-import"></i> 导入备忘录</button>
                <button id="exportMemoBtn" class="setting-btn" style="background:#2d3748; color:white;"><i class="fas fa-file-export"></i> 导出备忘录</button>
            </div>
            <div id="todoList"></div>
            <div class="footer-note">⚡ 截止前7天每次打开提醒 | 🟡 ≤3天黄字提醒 | 🔴 ≤1天红字逾期 | ⏰ 点击“推迟1天”可延后截止日期</div>
        </div>
    </div>
</div>

<div class="modal" id="auditImportModal"><div class="modal-content"><h3>📄 智能导入审核项目</h3><button id="uploadAuditJsonBtn" style="background:#e6b800; border:none; padding:0.4rem 1rem; border-radius:2rem; margin-bottom:0.8rem;">📁 上传 JSON</button><textarea id="auditRawText" class="batch-textarea" placeholder="粘贴文本..."></textarea><div class="modal-buttons" style="display:flex; gap:0.5rem; margin-top:1rem;"><button class="cancel" id="cancelAuditImport">取消</button><button class="confirm" id="confirmAuditImport">解析并导入</button></div></div></div>
<div class="modal" id="historyModal"><div class="modal-content"><h3>📜 历史事项</h3><div id="historyModalList"></div><button id="closeHistoryModal" class="cancel" style="margin-top:1rem;">关闭</button></div></div>
<div class="modal" id="iconSelectorModal"><div class="modal-content"><h3>🎨 选择图标</h3><div class="icon-selector-grid" id="iconGrid"></div><div class="icon-selector-actions" style="display:flex; flex-wrap:wrap; gap:0.5rem; margin-top:0.8rem;"><input type="text" id="customEmoji" placeholder="Emoji 或 FontAwesome 类名"><button id="applyCustomIcon">应用</button><button id="uploadImageBtn">📁 上传图片</button><input type="file" id="imageUpload" accept="image/*" style="display:none;"></div><button id="closeIconSelector" class="cancel" style="margin-top:1rem;">取消</button></div></div>
<div class="modal" id="todoEditModal"><div class="modal-content"><h3>✏️ 编辑待办</h3><div class="form-row"><label>待办内容</label><input type="text" id="editTodoTitle" placeholder="待办内容"></div><div class="datetime-row"><div class="form-row" style="flex:1;"><label>开始时间</label><input type="datetime-local" id="editTodoStart"></div><div class="form-row" style="flex:1;"><label>截止时间</label><input type="datetime-local" id="editTodoEnd"></div></div><div class="modal-buttons" style="display:flex; gap:0.5rem; margin-top:1rem;"><button class="cancel" id="cancelTodoEdit">取消</button><button class="confirm" id="confirmTodoEdit">保存修改</button></div></div></div>
<div class="modal" id="remarkModal"><div class="modal-content"><h3>✏️ 添加/编辑备注</h3><textarea id="remarkText" rows="4" style="width:100%; padding:0.5rem; border-radius:0.8rem; border:1px solid #ccc;" placeholder="输入备注信息..."></textarea><div class="modal-buttons" style="display:flex; gap:0.5rem; margin-top:1rem;"><button class="cancel" id="cancelRemark">取消</button><button class="confirm" id="saveRemark">保存</button></div></div></div>
<div class="modal" id="iconLibraryModal"><div class="modal-content"><h3>📚 图标库 <button id="batchUploadIconsBtn" style="background:#4caf50; border:none; padding:0.2rem 0.8rem; border-radius:1rem; font-size:0.7rem; margin-left:0.5rem; cursor:pointer;"><i class="fas fa-upload"></i> 批量上传</button></h3><div id="iconLibraryGrid" class="icon-library-grid"></div><div class="footer-note">💡 点击图标可直接应用到当前编辑的站点 | 支持Emoji、FontAwesome、图片</div><button id="closeIconLibrary" class="cancel" style="margin-top:1rem;">关闭</button></div></div>

<script>
// 农历数据（简化版）
const lunarData = {
    '2026-4-10': '二月廿三', '2026-4-11': '二月廿四', '2026-4-12': '二月廿五', '2026-4-13': '二月廿六',
    '2026-4-14': '二月廿七', '2026-4-15': '二月廿八', '2026-4-16': '二月廿九', '2026-4-17': '三月初一',
    '2026-4-18': '三月初二', '2026-4-19': '三月初三', '2026-4-20': '三月初四', '2026-4-21': '三月初五',
    '2026-4-22': '三月初六', '2026-4-23': '三月初七', '2026-4-24': '三月初八', '2026-4-25': '三月初九',
    '2026-4-26': '三月初十', '2026-4-27': '三月十一', '2026-4-28': '三月十二', '2026-4-29': '三月十三',
    '2026-4-30': '三月十四', '2026-5-1': '三月十五', '2026-5-2': '三月十六', '2026-5-3': '三月十七'
};
function getLunarDate(date) {
    const key = date.toISOString().slice(0,10);
    return lunarData[key] || '农历日期';
}
function getWeekday(date) {
    const weekdays = ['周日', '周一', '周二', '周三', '周四', '周五', '周六'];
    return weekdays[date.getDay()];
}
function updateDateTimeDisplay() {
    const now = new Date();
    const year = now.getFullYear();
    const month = now.getMonth() + 1;
    const day = now.getDate();
    const weekday = getWeekday(now);
    const lunar = getLunarDate(now);
    const hours = String(now.getHours()).padStart(2,'0');
    const minutes = String(now.getMinutes()).padStart(2,'0');
    const seconds = String(now.getSeconds()).padStart(2,'0');
    document.getElementById('datetimeDisplay').innerHTML = `
        <div class="date">📅 ${year}年${month}月${day}日 ${weekday} 农历${lunar}</div>
        <div class="time">🕐 ${hours}:${minutes}:${seconds}</div>
    `;
}
setInterval(updateDateTimeDisplay, 1000);
updateDateTimeDisplay();

const STORAGE_KEY = 'portal_v5';
const BACKUP_KEY = 'portal_backup_v5';
const ICON_LIBRARY_KEY = 'portal_icon_library';
let store = {
    categories: ['全部', '工作', '工具', '开发', '本地应用', '查询'],
    sites: [
        { name: '微信', url: 'weixin://', icon: 'fab fa-weixin', category: '本地应用', desc: '微信' },
        { name: '腾讯云', url: 'https://console.cloud.tencent.com/', icon: 'fas fa-cloud', category: '工作', desc: '云控制台' },
        { name: '阿里云', url: 'https://homenew.console.aliyun.com/', icon: 'fas fa-server', category: '工作', desc: '资源管理' },
        { name: '飞书', url: 'https://www.feishu.cn/', icon: 'fab fa-feishu', category: '工作', desc: '团队协作' },
        { name: 'Gmail', url: 'https://mail.google.com/', icon: 'fab fa-google', category: '工作', desc: '邮箱' },
        { name: 'GitHub', url: 'https://github.com/', icon: 'fab fa-github', category: '开发', desc: '代码' }
    ],
    currentCategory: '全部',
    searchTerm: '',
    todos: [],
    history: [],
    auditProjects: []
};
let reminderThreshold = 2;
let currentEditingIconSiteIdx = null;
let currentEditingTodoIdx = null;
let currentRemarkProjectId = null;
let userIconLibrary = [];
let shownReminders = new Set(); // 记录已提醒过的待办，避免重复提醒

function loadIconLibrary() {
    try {
        const saved = localStorage.getItem(ICON_LIBRARY_KEY);
        if (saved) {
            userIconLibrary = JSON.parse(saved);
        } else {
            userIconLibrary = ['🌐', '📁', '📊', '🔧', '⚙️', '💡', '🎯', '🔗', '✅', '⚠️', '🔔', '📞', 'fas fa-home', 'fas fa-cog', 'fab fa-weixin', 'fas fa-cloud', 'fab fa-github'];
        }
    } catch(e) { userIconLibrary = []; }
}
function saveIconLibrary() {
    localStorage.setItem(ICON_LIBRARY_KEY, JSON.stringify(userIconLibrary));
}
function addIconToLibrary(icon) {
    if (icon && !userIconLibrary.includes(icon)) {
        userIconLibrary.push(icon);
        saveIconLibrary();
        renderIconLibrary();
    }
}

function batchUploadIcons() {
    const overlay = document.createElement('div');
    overlay.style.position = 'fixed';
    overlay.style.top = '0';
    overlay.style.left = '0';
    overlay.style.width = '100%';
    overlay.style.height = '100%';
    overlay.style.background = 'rgba(0,0,0,0.5)';
    overlay.style.display = 'flex';
    overlay.style.alignItems = 'center';
    overlay.style.justifyContent = 'center';
    overlay.style.zIndex = '3000';
    
    const content = document.createElement('div');
    content.style.background = 'white';
    content.style.borderRadius = '1.5rem';
    content.style.padding = '1.2rem';
    content.style.width = '90%';
    content.style.maxWidth = '500px';
    content.innerHTML = `
        <h3 style="margin-bottom:0.8rem;">📚 批量上传图标</h3>
        <div style="display:flex; gap:0.8rem; margin-bottom:1rem;">
            <button id="uploadTextIconsBtn" style="flex:1; background:#2196f3; color:white; border:none; padding:0.5rem; border-radius:2rem; cursor:pointer;">📝 上传文字图标</button>
            <button id="uploadImageIconsBtn" style="flex:1; background:#4caf50; color:white; border:none; padding:0.5rem; border-radius:2rem; cursor:pointer;">🖼️ 批量上传图片</button>
        </div>
        <button id="cancelUploadMenu" style="width:100%; background:#f44336; color:white; border:none; padding:0.5rem; border-radius:2rem; cursor:pointer;">取消</button>
    `;
    overlay.appendChild(content);
    document.body.appendChild(overlay);
    
    const textBtn = content.querySelector('#uploadTextIconsBtn');
    textBtn.onclick = () => {
        document.body.removeChild(overlay);
        
        const textOverlay = document.createElement('div');
        textOverlay.style.position = 'fixed';
        textOverlay.style.top = '0';
        textOverlay.style.left = '0';
        textOverlay.style.width = '100%';
        textOverlay.style.height = '100%';
        textOverlay.style.background = 'rgba(0,0,0,0.5)';
        textOverlay.style.display = 'flex';
        textOverlay.style.alignItems = 'center';
        textOverlay.style.justifyContent = 'center';
        textOverlay.style.zIndex = '3000';
        
        const textContent = document.createElement('div');
        textContent.style.background = 'white';
        textContent.style.borderRadius = '1.5rem';
        textContent.style.padding = '1.2rem';
        textContent.style.width = '90%';
        textContent.style.maxWidth = '500px';
        textContent.innerHTML = `
            <h3 style="margin-bottom:0.8rem;">📝 批量上传文字图标</h3>
            <p style="font-size:0.7rem; color:#666; margin-bottom:0.5rem;">每行一个图标（支持Emoji或FontAwesome类名）<br>例如：<br>😀<br>🎉<br>fas fa-home</p>
            <textarea id="batchIconsInput" style="width:100%; height:150px; padding:0.5rem; border-radius:0.8rem; border:1px solid #ccc; font-family:monospace;"></textarea>
            <div style="display:flex; gap:0.8rem; margin-top:1rem;">
                <button id="confirmTextUploadBtn" style="flex:1; background:#4caf50; color:white; border:none; padding:0.5rem; border-radius:2rem; cursor:pointer;">✅ 确认添加</button>
                <button id="cancelTextUploadBtn" style="flex:1; background:#f44336; color:white; border:none; padding:0.5rem; border-radius:2rem; cursor:pointer;">❌ 取消</button>
            </div>
        `;
        textOverlay.appendChild(textContent);
        document.body.appendChild(textOverlay);
        
        const confirmBtn = textContent.querySelector('#confirmTextUploadBtn');
        const cancelBtn = textContent.querySelector('#cancelTextUploadBtn');
        const textarea = textContent.querySelector('#batchIconsInput');
        
        confirmBtn.onclick = () => {
            const lines = textarea.value.split(/\r?\n/);
            let added = 0;
            lines.forEach(line => {
                const icon = line.trim();
                if (icon && !userIconLibrary.includes(icon)) {
                    userIconLibrary.push(icon);
                    added++;
                }
            });
            if (added > 0) {
                saveIconLibrary();
                renderIconLibrary();
                alert(`✅ 成功添加 ${added} 个图标到图标库！`);
            } else {
                alert('没有新增图标（可能重复或为空）');
            }
            document.body.removeChild(textOverlay);
        };
        
        cancelBtn.onclick = () => document.body.removeChild(textOverlay);
    };
    
    const imageBtn = content.querySelector('#uploadImageIconsBtn');
    imageBtn.onclick = () => {
        document.body.removeChild(overlay);
        
        const fileInput = document.createElement('input');
        fileInput.type = 'file';
        fileInput.accept = 'image/*';
        fileInput.multiple = true;
        fileInput.onchange = (e) => {
            const files = Array.from(e.target.files);
            if (files.length === 0) return;
            
            let processed = 0;
            files.forEach(file => {
                const reader = new FileReader();
                reader.onload = (ev) => {
                    const imgData = ev.target.result;
                    if (!userIconLibrary.includes(imgData)) {
                        userIconLibrary.push(imgData);
                    }
                    processed++;
                    if (processed === files.length) {
                        saveIconLibrary();
                        renderIconLibrary();
                        alert(`✅ 成功添加 ${files.length} 张图片到图标库！`);
                    }
                };
                reader.readAsDataURL(file);
            });
        };
        fileInput.click();
    };
    
    const cancelBtn = content.querySelector('#cancelUploadMenu');
    cancelBtn.onclick = () => document.body.removeChild(overlay);
}

function renderIconLibrary() {
    const grid = document.getElementById('iconLibraryGrid');
    if (!grid) return;
    if (userIconLibrary.length === 0) {
        grid.innerHTML = '<div class="empty-message">暂无自定义图标，点击“批量上传”添加</div>';
        return;
    }
    let html = '';
    userIconLibrary.forEach((icon, idx) => {
        let displayHtml;
        if (icon.startsWith('data:image')) {
            displayHtml = `<img src="${icon}" style="max-width:40px; max-height:40px; object-fit:contain; border-radius:8px;">`;
        } else if (icon.startsWith('fa-') || icon.includes(' fa-')) {
            displayHtml = `<i class="${icon}" style="font-size:2rem;"></i>`;
        } else {
            displayHtml = `<span style="font-size:2rem;">${icon}</span>`;
        }
        const displayText = icon.startsWith('data:image') ? '图片' : (icon.length > 15 ? icon.substring(0,12)+'...' : icon);
        html += `<div class="icon-library-item" data-icon="${escapeHtml(icon)}" data-idx="${idx}">
            ${displayHtml}
            <span style="font-size:0.6rem; word-break:break-all;">${displayText}</span>
            <div><button class="delete-icon-btn" data-idx="${idx}" style="background:#f44336; color:white; border:none; border-radius:1rem; padding:0.1rem 0.4rem; font-size:0.6rem; margin-top:0.3rem; cursor:pointer;">删除</button></div>
        </div>`;
    });
    grid.innerHTML = html;
    
    grid.querySelectorAll('.icon-library-item').forEach(item => {
        const icon = item.dataset.icon;
        item.onclick = (e) => {
            if (e.target.classList.contains('delete-icon-btn')) return;
            if (currentEditingIconSiteIdx !== null) {
                store.sites[currentEditingIconSiteIdx].icon = icon;
                saveAll();
                renderNavSites();
                document.getElementById('iconLibraryModal').classList.remove('show');
                alert('图标已应用！');
            } else {
                alert('请先在站点上右键/长按图标打开选择器，再打开图标库选择图标');
            }
        };
    });
    
    grid.querySelectorAll('.delete-icon-btn').forEach(btn => {
        btn.onclick = (e) => {
            e.stopPropagation();
            const idx = parseInt(btn.dataset.idx);
            userIconLibrary.splice(idx, 1);
            saveIconLibrary();
            renderIconLibrary();
        };
    });
}

function escapeHtml(str) { return String(str).replace(/[&<>]/g, m => ({'&':'&amp;','<':'&lt;','>':'&gt;'}[m])); }
function getDaysRemaining(target) { let t = new Date(target); t.setHours(0,0,0,0); let today = new Date(); today.setHours(0,0,0,0); return Math.ceil((t - today) / 86400000); }
function saveAll() {
    localStorage.setItem(STORAGE_KEY, JSON.stringify({ categories: store.categories, sites: store.sites, auditProjects: store.auditProjects, todos: store.todos, history: store.history }));
}
function loadAll() {
    try { let d = JSON.parse(localStorage.getItem(STORAGE_KEY)); if (d) { store.categories = d.categories || store.categories; store.sites = d.sites || store.sites; store.auditProjects = d.auditProjects || []; store.todos = d.todos || []; store.history = d.history || []; } } catch(e) {}
    store.auditProjects.forEach(p => { if (!p.completedFlags) p.completedFlags = {plan:false, report:false, corrective:false, reportReview:false}; if (!p.remark) p.remark = ''; });
}
function createBackup() {
    const backup = {
        categories: store.categories,
        sites: store.sites,
        auditProjects: store.auditProjects,
        todos: store.todos,
        history: store.history,
        backupTime: new Date().toISOString()
    };
    localStorage.setItem(BACKUP_KEY, JSON.stringify(backup));
    alert(`✅ 备份完成！时间：${new Date().toLocaleString()}`);
}
function restoreBackup() {
    const backupData = localStorage.getItem(BACKUP_KEY);
    if (!backupData) { alert('没有找到备份文件'); return; }
    try {
        const backup = JSON.parse(backupData);
        if (backup.categories && backup.sites && backup.auditProjects) {
            store.categories = backup.categories;
            store.sites = backup.sites;
            store.auditProjects = backup.auditProjects;
            store.todos = backup.todos || [];
            store.history = backup.history || [];
            store.auditProjects.forEach(p => { if (!p.completedFlags) p.completedFlags = {plan:false, report:false, corrective:false, reportReview:false}; if (!p.remark) p.remark = ''; });
            saveAll();
            renderNavTabs(); renderNavSites(); updateCategorySelect();
            renderAudit(); renderTodos(); updateDashboard();
            alert(`✅ 恢复成功！备份时间：${new Date(backup.backupTime).toLocaleString()}`);
        } else { alert('备份文件格式错误'); }
    } catch(e) { alert('恢复失败：' + e.message); }
}
function autoBackup() { createBackup(); }
setInterval(() => autoBackup(), 24 * 60 * 60 * 1000);

function exportToCSV() {
    let csvRows = [];
    csvRows.push(['项目名称', '审核开始日期', '审核结束日期', '计划提交状态', '报告提交状态', '纠正措施状态', '报告评审状态', '备注']);
    store.auditProjects.forEach(p => {
        const flags = p.completedFlags || {};
        csvRows.push([
            p.displayName,
            new Date(p.auditStartDate).toLocaleDateString(),
            new Date(p.auditEndDate).toLocaleDateString(),
            flags.plan ? '已完成' : '未完成',
            flags.report ? '已完成' : '未完成',
            flags.corrective ? '已完成' : '未完成',
            flags.reportReview ? '已完成' : '未完成',
            p.remark || ''
        ]);
    });
    const csvContent = csvRows.map(row => row.map(cell => `"${String(cell).replace(/"/g, '""')}"`).join(',')).join('\n');
    const blob = new Blob(["\uFEFF" + csvContent], { type: 'text/csv;charset=utf-8;' });
    const link = document.createElement('a');
    const url = URL.createObjectURL(blob);
    link.href = url;
    link.setAttribute('download', `审核项目_${new Date().toISOString().slice(0,19)}.csv`);
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
    URL.revokeObjectURL(url);
}

function archiveAuditProject(project) {
    const index = store.auditProjects.findIndex(p => p.id === project.id);
    if (index !== -1) store.auditProjects.splice(index, 1);
    const historyItem = { ...project, archivedAt: new Date().toISOString(), isArchivedAudit: true, title: `📋 审核完成：${project.displayName}`, start: project.auditStartDate, end: project.auditEndDate };
    store.history.unshift(historyItem);
    store.todos = store.todos.filter(t => !(t.isFromAudit && t.auditProjectId === project.id));
    saveAll();
}

function generateOrUpdateReportReview(proj, correctiveCompleteDate) {
    store.todos = store.todos.filter(t => !(t.isFromAudit && t.auditProjectId === proj.id && t.deadlineType === 'reportReview'));
    let deadline = new Date(correctiveCompleteDate);
    deadline.setDate(deadline.getDate() + 30);
    let title = `✅【报告评审】${proj.displayName}`;
    let newTodo = { id: Date.now() + '-' + Math.random() + 'review', title, start: deadline.toISOString(), end: deadline.toISOString(), created: new Date().toISOString(), lastRemindedAt: null, isFromAudit: true, auditProjectId: proj.id, deadlineType: 'reportReview' };
    store.todos.push(newTodo);
    sortTodos();
    saveAll();
}

function generateTodoForAudit(proj, type) {
    let title = '', deadline = null;
    if (type === 'plan') { title = `📋【计划提交】${proj.displayName}`; deadline = new Date(proj.auditStartDate); deadline.setDate(deadline.getDate() - 15); }
    else if (type === 'report') { title = `📄【报告提交】${proj.displayName}`; deadline = new Date(proj.auditEndDate); deadline.setDate(deadline.getDate() + 7); }
    else if (type === 'corrective') { title = `🔧【纠正措施】${proj.displayName}`; deadline = new Date(proj.auditEndDate); deadline.setDate(deadline.getDate() + 30); }
    else if (type === 'reportReview') { return null; }
    if (!deadline || isNaN(deadline) || (proj.completedFlags && proj.completedFlags[type])) return null;
    if (store.todos.some(t => t.isFromAudit && t.auditProjectId === proj.id && t.deadlineType === type)) return null;
    return { id: Date.now() + '-' + Math.random() + type, title, start: deadline.toISOString(), end: deadline.toISOString(), created: new Date().toISOString(), lastRemindedAt: null, isFromAudit: true, auditProjectId: proj.id, deadlineType: type };
}

function syncTodosForAudit(proj) { 
    const types = ['plan', 'report', 'corrective'];
    types.forEach(t => { let todo = generateTodoForAudit(proj, t); if (todo) store.todos.push(todo); });
    sortTodos(); saveAll(); renderTodos();
}

function addAuditProject(p) {
    if (store.auditProjects.some(a => a.displayName === p.displayName && a.auditEndDate === p.auditEndDate)) return false;
    let newProj = { id: Date.now() + '-' + Math.random(), ...p, completedFlags: {plan:false, report:false, corrective:false, reportReview:false}, remark: '' };
    store.auditProjects.push(newProj);
    syncTodosForAudit(newProj);
    saveAll(); renderAudit(); updateDashboard();
    return true;
}

function deleteAuditProject(id, silent = false) {
    if (!silent && !confirm('删除项目将删除关联待办')) return;
    store.auditProjects = store.auditProjects.filter(p => p.id !== id);
    store.todos = store.todos.filter(t => !(t.isFromAudit && t.auditProjectId === id));
    store.history = store.history.filter(t => !(t.isFromAudit && t.auditProjectId === id));
    saveAll(); renderAudit(); renderTodos(); updateDashboard();
}

function sortTodos() { store.todos.sort((a,b) => new Date(a.end) - new Date(b.end)); }

function delayTodo(idx) {
    const todo = store.todos[idx];
    if (!todo) return;
    if (todo.isFromAudit) {
        alert('审核关联待办不可手动推迟，请通过完成里程碑调整');
        return;
    }
    const currentEnd = new Date(todo.end);
    const newEnd = new Date(currentEnd);
    newEnd.setDate(newEnd.getDate() + 1);
    todo.end = newEnd.toISOString();
    todo.start = newEnd.toISOString();
    sortTodos();
    saveAll();
    renderTodos();
    updateDashboard();
    alert(`✅ 已将“${todo.title}”推迟至 ${newEnd.toLocaleDateString()}`);
}

function parseAuditText(text) {
    let blocks = text.split(/\n\s*\n/), res = [];
    for (let b of blocks) {
        let lines = b.split(/\r?\n/).filter(l => l.trim());
        let start = null, end = null, cust = '', type = '';
        for (let l of lines) {
            if (l.includes('审核日期')) {
                let m = l.match(/审核日期：\s*([0-9]{1,2}-[A-Za-z]{3}-[0-9]{4})\s*-\s*([0-9]{1,2}-[A-Za-z]{3}-[0-9]{4})/);
                if (m) { start = new Date(m[1]); end = new Date(m[2]); }
                else { let s = l.match(/审核日期：\s*([0-9]{1,2}-[A-Za-z]{3}-[0-9]{4})/); if (s) end = new Date(s[1]); }
            }
        }
        let tl = lines.find(l => l.match(/[A-Z0-9]{2,}[-][A-Z0-9]{2,}/));
        if (tl) type = tl.match(/[A-Z0-9]{2,}[-][A-Z0-9]{2,}/)?.[0] || '';
        cust = lines[0] || '项目';
        if (end && cust) {
            if (!start) start = new Date(end);
            res.push({ customerName: cust, auditType: type, displayName: `${cust} (${type||'审核'})`, issueDate: start.toISOString(), auditStartDate: start.toISOString(), auditEndDate: end.toISOString() });
        }
    }
    return res;
}

function openRemarkModal(projectId) {
    currentRemarkProjectId = projectId;
    const project = store.auditProjects.find(p => p.id === projectId);
    if (project) {
        document.getElementById('remarkText').value = project.remark || '';
    } else {
        document.getElementById('remarkText').value = '';
    }
    document.getElementById('remarkModal').classList.add('show');
}

function saveRemark() {
    if (currentRemarkProjectId) {
        const project = store.auditProjects.find(p => p.id === currentRemarkProjectId);
        if (project) {
            project.remark = document.getElementById('remarkText').value;
            saveAll();
            renderAudit();
        }
    }
    document.getElementById('remarkModal').classList.remove('show');
    currentRemarkProjectId = null;
}

function getProjectUrgencyScore(project) {
    const flags = project.completedFlags || {};
    let minDays = Infinity;
    if (!flags.plan) {
        const planDate = new Date(project.auditStartDate);
        planDate.setDate(planDate.getDate() - 15);
        minDays = Math.min(minDays, getDaysRemaining(planDate));
    }
    if (!flags.report) {
        const reportDate = new Date(project.auditEndDate);
        reportDate.setDate(reportDate.getDate() + 7);
        minDays = Math.min(minDays, getDaysRemaining(reportDate));
    }
    if (!flags.corrective) {
        const corrDate = new Date(project.auditEndDate);
        corrDate.setDate(corrDate.getDate() + 30);
        minDays = Math.min(minDays, getDaysRemaining(corrDate));
    }
    if (!flags.reportReview) {
        minDays = Math.min(minDays, 999);
    }
    return minDays === Infinity ? 999999 : minDays;
}

function renderAudit() {
    let cont = document.getElementById('auditListContainer'); if (!cont) return;
    if (store.auditProjects.length === 0) { 
        cont.innerHTML = '<div class="empty-message">✨ 暂无进行中的审核项目<br>完成“报告评审”后项目将自动归档</div>'; 
        document.getElementById('auditStats').innerHTML = '0个项目';
        return; 
    }
    const sortedProjects = [...store.auditProjects].sort((a, b) => {
        const scoreA = getProjectUrgencyScore(a);
        const scoreB = getProjectUrgencyScore(b);
        return scoreA - scoreB;
    });
    
    let html = '';
    sortedProjects.forEach(p => {
        let flags = p.completedFlags || {};
        let planD = new Date(p.auditStartDate); planD.setDate(planD.getDate() - 15);
        let repD = new Date(p.auditEndDate); repD.setDate(repD.getDate() + 7);
        let corD = new Date(p.auditEndDate); corD.setDate(corD.getDate() + 30);
        let metrics = '';
        if (!flags.plan) { let d = getDaysRemaining(planD); let cls = d > 0 ? 'green' : (d === 0 ? 'yellow' : 'red'); let txt = d > 0 ? `剩余${d}天` : (d === 0 ? '今日截止' : `逾期${-d}天`); metrics += `<span class="metric ${cls}">📋 计划提交: ${txt}</span>`; }
        if (!flags.report) { let d = getDaysRemaining(repD); let cls = d > 0 ? 'green' : (d === 0 ? 'yellow' : 'red'); let txt = d > 0 ? `剩余${d}天` : (d === 0 ? '今日截止' : `逾期${-d}天`); metrics += `<span class="metric ${cls}">📄 报告提交: ${txt}</span>`; }
        if (!flags.corrective) { let d = getDaysRemaining(corD); let cls = d > 0 ? 'green' : (d === 0 ? 'yellow' : 'red'); let txt = d > 0 ? `剩余${d}天` : (d === 0 ? '今日截止' : `逾期${-d}天`); metrics += `<span class="metric ${cls}">🔧 纠正措施: ${txt}</span>`; }
        if (!flags.reportReview) { metrics += `<span class="metric">✅ 报告评审: 未完成</span>`; } 
        else { metrics += `<span class="metric green">✅ 报告评审: 已完成</span>`; }
        const remarkHtml = p.remark ? `<div class="remark-text" onclick="openRemarkModal('${p.id}')">📝 ${escapeHtml(p.remark.substring(0, 50))}${p.remark.length > 50 ? '...' : ''}</div>` : `<span class="remark-badge" onclick="openRemarkModal('${p.id}')">➕ 添加备注</span>`;
        html += `<div class="audit-item"><input type="checkbox" class="audit-checkbox" data-id="${p.id}"><div class="audit-content"><div class="audit-header"><span class="audit-name">${escapeHtml(p.displayName)}</span><button class="del-audit" data-id="${p.id}" style="background:none;border:none;color:#f44336;cursor:pointer;">✕</button>${remarkHtml}</div><div class="audit-dates">📅 ${new Date(p.auditStartDate).toLocaleDateString()} → ${new Date(p.auditEndDate).toLocaleDateString()}</div><div class="audit-metrics">${metrics}</div></div></div>`;
    });
    cont.innerHTML = html;
    document.getElementById('auditStats').innerHTML = `${sortedProjects.length}个进行中项目`;
    cont.querySelectorAll('.del-audit').forEach(btn => btn.onclick = () => deleteAuditProject(btn.dataset.id));
}

// 检查并提醒自定义待办（每次打开页面时调用）
function checkAndRemindCustomTodos() {
    const now = new Date();
    const today = new Date(now.getFullYear(), now.getMonth(), now.getDate());
    const reminderList = [];
    
    store.todos.forEach(todo => {
        if (todo.isFromAudit) return; // 审核待办不弹窗提醒
        
        const endDate = new Date(todo.end);
        const endDay = new Date(endDate.getFullYear(), endDate.getMonth(), endDate.getDate());
        const daysRemaining = Math.ceil((endDay - today) / (1000 * 60 * 60 * 24));
        
        // 剩余天数 ≤ 7天，且未提醒过（或提醒记录已过期）
        if (daysRemaining <= 7 && daysRemaining >= 0) {
            const reminderKey = `${todo.id}_${endDay.toISOString().slice(0,10)}`;
            if (!shownReminders.has(reminderKey)) {
                reminderList.push({ todo, daysRemaining });
                shownReminders.add(reminderKey);
            }
        }
    });
    
    if (reminderList.length > 0) {
        let message = '⏰ 待办提醒：\n';
        reminderList.forEach(item => {
            let urgencyText = '';
            if (item.daysRemaining <= 1) urgencyText = '【紧急】';
            else if (item.daysRemaining <= 3) urgencyText = '【警告】';
            message += `${urgencyText} ${item.todo.title}\n   截止：${new Date(item.todo.end).toLocaleDateString()}，剩余${item.daysRemaining}天\n`;
        });
        alert(message);
    }
}

function renderTodos() {
    let cont = document.getElementById('todoList'); if (!cont) return;
    sortTodos();
    if (store.todos.length === 0) { cont.innerHTML = '<div class="empty-message">✨ 暂无待办</div>'; return; }
    let html = '';
    store.todos.forEach((t, idx) => {
        let titleHtml = escapeHtml(t.title);
        let prefixClass = '';
        if (t.isFromAudit) {
            if (t.deadlineType === 'plan') { prefixClass = 'todo-prefix-plan'; titleHtml = `<span class="${prefixClass}">📋【计划提交】</span>${escapeHtml(t.title.replace('📋【计划提交】', ''))}`; }
            else if (t.deadlineType === 'report') { prefixClass = 'todo-prefix-report'; titleHtml = `<span class="${prefixClass}">📄【报告提交】</span>${escapeHtml(t.title.replace('📄【报告提交】', ''))}`; }
            else if (t.deadlineType === 'corrective') { prefixClass = 'todo-prefix-corrective'; titleHtml = `<span class="${prefixClass}">🔧【纠正措施】</span>${escapeHtml(t.title.replace('🔧【纠正措施】', ''))}`; }
            else if (t.deadlineType === 'reportReview') { prefixClass = 'todo-prefix-review'; titleHtml = `<span class="${prefixClass}">✅【报告评审】</span>${escapeHtml(t.title.replace('✅【报告评审】', ''))}`; }
        } else {
            prefixClass = 'todo-prefix-custom';
            titleHtml = `<span class="${prefixClass}">⏰【自定义】</span>${escapeHtml(t.title)}`;
        }
        
        const startDate = new Date(t.start);
        const endDate = new Date(t.end);
        const remainingDays = getDaysRemaining(endDate);
        
        let timeClass = 'normal';
        let timeText = `⏱️ 开始：${startDate.toLocaleString()} → 截止：${endDate.toLocaleString()}`;
        
        if (remainingDays < 0) {
            timeClass = 'overdue';
            timeText = `⚠️ 已逾期 ${-remainingDays} 天 · 开始：${startDate.toLocaleString()} → 截止：${endDate.toLocaleString()}`;
        } else if (remainingDays === 0) {
            timeClass = 'urgent';
            timeText = `🔴 今日截止！开始：${startDate.toLocaleString()} → 截止：${endDate.toLocaleString()}`;
        } else if (remainingDays <= 3) {
            timeClass = 'urgent';
            timeText = `🔴 剩余 ${remainingDays} 天 · 开始：${startDate.toLocaleString()} → 截止：${endDate.toLocaleString()}`;
        } else if (remainingDays <= 7) {
            timeClass = 'warning';
            timeText = `🟡 剩余 ${remainingDays} 天 · 开始：${startDate.toLocaleString()} → 截止：${endDate.toLocaleString()}`;
        }
        
        // 审核待办不显示编辑按钮
        let editBtnHtml = '';
        if (!t.isFromAudit) {
            editBtnHtml = `<button class="edit-todo-btn" data-idx="${idx}">✏️ 编辑</button>`;
        }
        
        html += `<div class="todo-item"><input type="checkbox" class="todo-checkbox" data-idx="${idx}"><div class="todo-content"><div class="todo-title">${titleHtml}${t.isFromAudit ? '<span class="history-badge">审核</span>' : ''}</div><div class="todo-time ${timeClass}">${timeText}</div><div class="todo-actions"><button class="complete-btn" data-idx="${idx}">✓ 完成</button><button class="delete-todo-btn" data-idx="${idx}">✕ 删除</button>${editBtnHtml}<button class="delay-todo-btn" data-idx="${idx}" title="推迟1天">⏰ 推迟1天</button></div></div></div>`;
    });
    cont.innerHTML = html;
    
    document.querySelectorAll('.complete-btn').forEach(btn => btn.onclick = () => {
        let idx = btn.dataset.idx;
        let todo = store.todos[idx];
        if (todo.isFromAudit) {
            let proj = store.auditProjects.find(p => p.id === todo.auditProjectId);
            if (proj) {
                if (todo.deadlineType === 'corrective') {
                    if (!proj.completedFlags.corrective) {
                        proj.completedFlags.corrective = true;
                        let completeDate = new Date();
                        generateOrUpdateReportReview(proj, completeDate);
                        saveAll();
                        renderAudit();
                        renderTodos();
                        updateDashboard();
                        alert(`✅ 纠正措施已完成！已自动生成报告评审待办（完成日期+30天）。`);
                    }
                    store.history.unshift({ ...todo, completedAt: new Date().toISOString() });
                    store.todos.splice(idx, 1);
                    saveAll();
                    renderTodos();
                    updateDashboard();
                    return;
                }
                else if (todo.deadlineType === 'reportReview') {
                    if (!proj.completedFlags.reportReview) {
                        proj.completedFlags.reportReview = true;
                        saveAll();
                        renderAudit();
                        archiveAuditProject(proj);
                        renderAudit();
                        renderTodos();
                        updateDashboard();
                        alert(`✅ 审核项目“${proj.displayName}”的报告评审已完成！项目已归档至历史事项。`);
                    }
                    store.history.unshift({ ...todo, completedAt: new Date().toISOString() });
                    store.todos.splice(idx, 1);
                    saveAll();
                    renderTodos();
                    updateDashboard();
                    return;
                }
                else {
                    if (!proj.completedFlags[todo.deadlineType]) {
                        proj.completedFlags[todo.deadlineType] = true;
                        saveAll();
                        renderAudit();
                        const allCompleted = proj.completedFlags.plan && proj.completedFlags.report && proj.completedFlags.corrective && proj.completedFlags.reportReview;
                        if (allCompleted) {
                            archiveAuditProject(proj);
                            renderAudit();
                            renderTodos();
                            updateDashboard();
                            alert(`✅ 审核项目“${proj.displayName}”的所有里程碑已完成！已自动归档。`);
                        }
                    }
                }
            }
        }
        store.history.unshift({ ...todo, completedAt: new Date().toISOString() });
        store.todos.splice(idx, 1);
        saveAll();
        renderTodos();
        updateDashboard();
    });
    
    document.querySelectorAll('.delete-todo-btn').forEach(btn => btn.onclick = () => {
        let idx = btn.dataset.idx;
        let todo = store.todos[idx];
        if (todo.isFromAudit) {
            let proj = store.auditProjects.find(p => p.id === todo.auditProjectId);
            if (proj && proj.completedFlags) {
                proj.completedFlags[todo.deadlineType] = false;
                saveAll();
                renderAudit();
            }
        }
        store.todos.splice(idx, 1);
        saveAll();
        renderTodos();
        updateDashboard();
    });
    
    document.querySelectorAll('.delay-todo-btn').forEach(btn => btn.onclick = () => {
        let idx = parseInt(btn.dataset.idx);
        delayTodo(idx);
    });
    
    document.querySelectorAll('.edit-todo-btn').forEach(btn => btn.onclick = () => {
        let idx = parseInt(btn.dataset.idx);
        let todo = store.todos[idx];
        if (!todo || todo.isFromAudit) { alert('审核关联待办不可编辑'); return; }
        currentEditingTodoIdx = idx;
        document.getElementById('editTodoTitle').value = todo.title;
        const startDate = new Date(todo.start);
        const endDate = new Date(todo.end);
        const formatLocal = (d) => { let y=d.getFullYear(), m=String(d.getMonth()+1).padStart(2,'0'), day=String(d.getDate()).padStart(2,'0'), h=String(d.getHours()).padStart(2,'0'), min=String(d.getMinutes()).padStart(2,'0'); return `${y}-${m}-${day}T${h}:${min}`; };
        document.getElementById('editTodoStart').value = formatLocal(startDate);
        document.getElementById('editTodoEnd').value = formatLocal(endDate);
        document.getElementById('todoEditModal').classList.add('show');
    });
}

function updateDashboard() {
    document.getElementById('dashSiteCount').innerText = store.sites.length;
    document.getElementById('dashAuditCount').innerText = store.auditProjects.length;
    document.getElementById('dashTodoCount').innerText = store.todos.length;
    let recent = store.sites.slice(0,3);
    document.getElementById('dashRecentSites').innerHTML = recent.map(s => `<li>${escapeHtml(s.name)}</li>`).join('');
    let overdue = [];
    store.auditProjects.forEach(p => { let cd = new Date(p.auditEndDate); cd.setDate(cd.getDate() + 30); if (getDaysRemaining(cd) <= 0) overdue.push(p.displayName); });
    document.getElementById('dashOverdueAudits').innerHTML = overdue.slice(0,3).map(n => `<li>⚠️ ${escapeHtml(n)}</li>`).join('') || '<li>暂无逾期项目</li>';
    let upcoming = store.todos.slice(0,3);
    document.getElementById('dashUpcomingTodos').innerHTML = upcoming.map(t => `<li>📌 ${escapeHtml(t.title.substring(0,25))} <span style="font-size:0.6rem;">${new Date(t.end).toLocaleDateString()}</span></li>`).join('') || '<li>暂无待办</li>';
    
    let reminders = [];
    store.auditProjects.forEach(p => {
        let f = p.completedFlags || {};
        if (!f.plan) { let d = getDaysRemaining(new Date(new Date(p.auditStartDate).setDate(new Date(p.auditStartDate).getDate() - 15))); if (d <= 3) reminders.push({ text: `📋 ${p.displayName} 计划提交${d>0?`剩${d}天`:d===0?'今日截止':`逾期${-d}天`}`, days: d, isOverdue: d <= 0, isWarning: d <= 3 && d > 0 }); }
        if (!f.report) { let d = getDaysRemaining(new Date(new Date(p.auditEndDate).setDate(new Date(p.auditEndDate).getDate() + 7))); if (d <= 3) reminders.push({ text: `📄 ${p.displayName} 报告提交${d>0?`剩${d}天`:d===0?'今日截止':`逾期${-d}天`}`, days: d, isOverdue: d <= 0, isWarning: d <= 3 && d > 0 }); }
        if (!f.corrective) { let d = getDaysRemaining(new Date(new Date(p.auditEndDate).setDate(new Date(p.auditEndDate).getDate() + 30))); if (d <= 3) reminders.push({ text: `🔧 ${p.displayName} 纠正措施${d>0?`剩${d}天`:d===0?'今日截止':`逾期${-d}天`}`, days: d, isOverdue: d <= 0, isWarning: d <= 3 && d > 0 }); }
    });
    store.todos.forEach(t => { 
        if (!t.isFromAudit) { 
            let d = getDaysRemaining(t.end); 
            if (d <= 7) reminders.push({ text: `⏰ ${t.title}${d>0?`剩${d}天`:d===0?'今日截止':`逾期${-d}天`}`, days: d, isOverdue: d <= 0, isWarning: d <= 3 && d > 0 }); 
        } 
    });
    
    reminders.sort((a, b) => {
        if (a.isOverdue !== b.isOverdue) return a.isOverdue ? -1 : 1;
        if (a.isWarning !== b.isWarning) return a.isWarning ? -1 : 1;
        return a.days - b.days;
    });
    
    document.getElementById('dashboardReminders').innerHTML = reminders.length ? `<ul class="reminder-list">${reminders.map(r => `<li class="reminder-item ${r.isOverdue ? 'overdue' : (r.isWarning ? 'warning' : '')}">${escapeHtml(r.text)}</li>`).join('')}</ul>` : '<div class="empty-message">暂无近期提醒 🎉</div>';
    document.getElementById('globalStats').innerHTML = `📋 ${store.auditProjects.length} 审核 | 📝 ${store.todos.length} 待办`;
}

function renderNavSites() {
    let filtered = store.sites.filter(s => (store.currentCategory === '全部' || s.category === store.currentCategory) && (!store.searchTerm || s.name.toLowerCase().includes(store.searchTerm.toLowerCase()) || (s.desc || '').toLowerCase().includes(store.searchTerm.toLowerCase())));
    let grid = document.getElementById('navSitesGrid');
    if (!grid) return;
    if (filtered.length === 0) { grid.innerHTML = '<div class="empty-message">✨ 无匹配站点</div>'; return; }
    let html = '';
    filtered.forEach((s, idx) => {
        let oidx = store.sites.findIndex(s2 => s2.name === s.name && s2.url === s.url);
        let iconHtml = (s.icon && (s.icon.startsWith('fa-') || s.icon.includes(' fa-'))) ? `<i class="${s.icon}"></i>` : (s.icon && (s.icon.startsWith('data:') || s.icon.startsWith('http'))) ? `<img src="${s.icon}" style="max-width:48px; max-height:48px; width:auto; height:auto; object-fit:contain;">` : `<span>${s.icon || '🔗'}</span>`;
        html += `<div class="site-card" data-idx="${oidx}"><div class="delete-btn" data-del="${oidx}">✕</div><div class="card-icon" data-icon="${oidx}">${iconHtml}</div><div class="site-title">${escapeHtml(s.name)}</div></div>`;
    });
    grid.innerHTML = html;
    grid.querySelectorAll('.delete-btn').forEach(btn => btn.onclick = (e) => { e.stopPropagation(); let idx = btn.dataset.del; if (confirm('删除站点？')) { store.sites.splice(idx, 1); saveAll(); renderNavSites(); updateDashboard(); } });
    let attachIconEvents = () => {
        grid.querySelectorAll('.card-icon').forEach(ic => {
            ic.oncontextmenu = (e) => { e.preventDefault(); let siteIdx = ic.dataset.icon; if (siteIdx) openIconSelector(siteIdx); };
            ic.addEventListener('touchstart', (e) => { let timer = setTimeout(() => { let siteIdx = ic.dataset.icon; if (siteIdx) openIconSelector(siteIdx); e.preventDefault(); }, 500); ic.addEventListener('touchend', () => clearTimeout(timer), { once: true }); ic.addEventListener('touchmove', () => clearTimeout(timer), { once: true }); });
        });
    };
    attachIconEvents();
    grid.querySelectorAll('.site-card').forEach(card => {
        card.onclick = (e) => { if (e.target.classList.contains('delete-btn') || e.target.closest('.card-icon')) return; let s = store.sites[card.dataset.idx]; if (s && s.url) { if (s.url.startsWith('http')) window.open(s.url, '_blank'); else alert('本地应用：' + s.url); } };
    });
}

function renderNavTabs() {
    let cats = store.categories;
    let html = cats.map(c => `<button class="tab-btn ${c === store.currentCategory ? 'active' : ''}" data-cat="${escapeHtml(c)}">${escapeHtml(c)}</button>`).join('');
    document.getElementById('navTabContainer').innerHTML = html;
    document.querySelectorAll('#navTabContainer .tab-btn').forEach(btn => {
        btn.onclick = () => { store.currentCategory = btn.dataset.cat; renderNavTabs(); renderNavSites(); };
        btn.ondblclick = () => {
            let old = btn.dataset.cat;
            if (old === '全部') return;
            let n = prompt('新名称', old);
            if (n && n.trim() && !store.categories.includes(n.trim())) {
                let idx = store.categories.indexOf(old);
                if (idx !== -1) {
                    store.categories[idx] = n.trim();
                    store.sites.forEach(s => { if (s.category === old) s.category = n.trim(); });
                    if (store.currentCategory === old) store.currentCategory = n.trim();
                    saveAll(); renderNavTabs(); renderNavSites(); updateCategorySelect();
                }
            }
        };
    });
}

function updateCategorySelect() {
    let sel = document.getElementById('navSiteCategory');
    if (sel) sel.innerHTML = store.categories.filter(c => c !== '全部').map(c => `<option value="${escapeHtml(c)}">${escapeHtml(c)}</option>`).join('');
}

function openIconSelector(siteIdx) {
    currentEditingIconSiteIdx = parseInt(siteIdx);
    let modal = document.getElementById('iconSelectorModal');
    let grid = document.getElementById('iconGrid');
    
    const defaultIcons = ['fas fa-home','fas fa-cog','fab fa-weixin','fas fa-cloud','fab fa-github','🌐','📁','📊','🔧','⚙️','💡','🎯','🔗','✅','⚠️','🔔','📞'];
    let allIcons = [...defaultIcons];
    if (userIconLibrary && userIconLibrary.length > 0) {
        userIconLibrary.forEach(icon => {
            if (!allIcons.includes(icon)) {
                allIcons.push(icon);
            }
        });
    }
    
    let html = '';
    allIcons.forEach(icon => {
        let displayHtml;
        if (icon.startsWith('data:image')) {
            displayHtml = `<img src="${icon}" style="max-width:32px; max-height:32px; object-fit:contain; border-radius:6px;">`;
        } else if (icon.startsWith('fa-') || icon.includes(' fa-')) {
            displayHtml = `<i class="${icon}" style="font-size:1.8rem;"></i>`;
        } else {
            displayHtml = `<span style="font-size:1.8rem;">${icon}</span>`;
        }
        const displayText = icon.startsWith('data:image') ? '图片' : (icon.length > 15 ? icon.substring(0,12)+'...' : icon);
        html += `<div class="icon-option" data-icon="${escapeHtml(icon)}" title="${escapeHtml(icon)}">
            ${displayHtml}
            <span style="font-size:0.6rem; display:block; word-break:break-all;">${displayText}</span>
        </div>`;
    });
    
    grid.innerHTML = html;
    
    grid.querySelectorAll('.icon-option').forEach(opt => {
        opt.onclick = () => {
            const selectedIcon = opt.dataset.icon;
            store.sites[currentEditingIconSiteIdx].icon = selectedIcon;
            if (selectedIcon && !userIconLibrary.includes(selectedIcon)) {
                userIconLibrary.push(selectedIcon);
                saveIconLibrary();
            }
            saveAll();
            renderNavSites();
            modal.classList.remove('show');
        };
    });
    
    modal.classList.add('show');
}

function openIconLibrary() {
    if (currentEditingIconSiteIdx === null) {
        alert('请先在站点上右键/长按图标打开选择器，然后再打开图标库');
        return;
    }
    renderIconLibrary();
    document.getElementById('iconLibraryModal').classList.add('show');
}

function switchToView(viewName) {
    document.querySelectorAll('.view').forEach(v => v.classList.remove('active'));
    let target = document.getElementById('view-' + viewName);
    if (target) target.classList.add('active');
    document.querySelectorAll('.nav-tab').forEach(tab => { if (tab.dataset.view === viewName) tab.classList.add('active'); else tab.classList.remove('active'); });
    if (viewName === 'nav') { renderNavTabs(); renderNavSites(); updateCategorySelect(); }
    if (viewName === 'audit') { renderAudit(); setTimeout(initAuditBatchDelete, 50); }
    if (viewName === 'memo') { renderTodos(); setTimeout(initTodoBatchDelete, 50); }
    if (viewName === 'dashboard') { updateDashboard(); }
}

function initAuditBatchDelete() {
    let selectAll = document.getElementById('auditSelectAllBtn');
    let batchDelete = document.getElementById('auditBatchDeleteBtn');
    if (!selectAll || !batchDelete) return;
    selectAll.onclick = () => { let cbs = document.querySelectorAll('.audit-checkbox'); let allChecked = Array.from(cbs).every(cb => cb.checked); cbs.forEach(cb => cb.checked = !allChecked); };
    batchDelete.onclick = () => { let selected = Array.from(document.querySelectorAll('.audit-checkbox:checked')).map(cb => cb.dataset.id); if (selected.length === 0) return alert('请至少选择一个项目'); if (confirm(`确定删除 ${selected.length} 个审核项目及其关联待办吗？`)) { selected.forEach(id => deleteAuditProject(id, true)); renderAudit(); renderTodos(); updateDashboard(); } };
}

function initTodoBatchDelete() {
    let selectAll = document.getElementById('todoSelectAllBtn');
    let batchDelete = document.getElementById('todoBatchDeleteBtn');
    if (!selectAll || !batchDelete) return;
    selectAll.onclick = () => { let cbs = document.querySelectorAll('.todo-checkbox'); let allChecked = Array.from(cbs).every(cb => cb.checked); cbs.forEach(cb => cb.checked = !allChecked); };
    batchDelete.onclick = () => { let indices = Array.from(document.querySelectorAll('.todo-checkbox:checked')).map(cb => parseInt(cb.dataset.idx)).sort((a,b)=>b-a); if (indices.length === 0) return alert('请至少选择一个待办'); if (confirm(`确定删除 ${indices.length} 个待办吗？`)) { indices.forEach(idx => { let todo = store.todos[idx]; if (todo.isFromAudit) { let proj = store.auditProjects.find(p => p.id === todo.auditProjectId); if (proj && proj.completedFlags) proj.completedFlags[todo.deadlineType] = false; } store.todos.splice(idx, 1); }); sortTodos(); saveAll(); renderTodos(); renderAudit(); updateDashboard(); } };
}

document.addEventListener('DOMContentLoaded', () => {
    loadAll();
    loadIconLibrary();
    let savedTh = localStorage.getItem('reminder_threshold');
    if (savedTh) { let v = parseInt(savedTh, 10); if (!isNaN(v) && v >= 0) reminderThreshold = v; }
    document.getElementById('reminderThresholdBtn').onclick = () => { let v = prompt(`紧急提醒阈值（剩余天数 ≤ 阈值标红）\n当前：${reminderThreshold}`, reminderThreshold); if (v !== null) { let n = parseInt(v, 10); if (!isNaN(n) && n >= 0) { reminderThreshold = n; localStorage.setItem('reminder_threshold', n); updateDashboard(); alert(`阈值已设为 ${reminderThreshold} 天`); } else alert('请输入有效非负整数'); } };
    document.querySelectorAll('.nav-tab').forEach(btn => btn.addEventListener('click', () => switchToView(btn.dataset.view)));
    document.querySelectorAll('.dash-card').forEach(card => card.addEventListener('click', () => switchToView(card.dataset.view)));
    
    document.getElementById('navAddSiteBtn').onclick = () => {
        let n = document.getElementById('navSiteName').value.trim(), u = document.getElementById('navSiteUrl').value.trim(), i = document.getElementById('navSiteIcon').value.trim() || '🌐', c = document.getElementById('navSiteCategory').value;
        if (!n || !u) return alert('名称和网址不能为空');
        if (!u.includes('://')) u = 'https://' + u;
        store.sites.push({ name: n, url: u, icon: i, category: c, desc: n });
        saveAll(); renderNavSites(); renderNavTabs(); updateDashboard();
        document.getElementById('navSiteName').value = ''; document.getElementById('navSiteUrl').value = 'https://';
    };
    document.getElementById('navAddCategoryBtn').onclick = () => { let newCat = prompt('新栏目名称'); if (newCat && newCat.trim() && !store.categories.includes(newCat.trim())) { store.categories.push(newCat.trim()); saveAll(); renderNavTabs(); renderNavSites(); updateCategorySelect(); } };
    document.getElementById('navBatchExportBtn').onclick = () => { let blob = new Blob([JSON.stringify({ categories: store.categories, sites: store.sites })], { type: 'application/json' }); let a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'nav_backup.json'; a.click(); };
    document.getElementById('navBatchImportBtn').onclick = () => { let inp = document.createElement('input'); inp.type = 'file'; inp.accept = 'application/json'; inp.onchange = e => { let f = e.target.files[0]; if (!f) return; let r = new FileReader(); r.onload = ev => { try { let d = JSON.parse(ev.target.result); if (d.categories && d.sites) { store.categories = d.categories; store.sites = d.sites; saveAll(); renderNavTabs(); renderNavSites(); updateCategorySelect(); updateDashboard(); alert('导入成功'); } else alert('无效备份文件'); } catch(err) { alert('解析失败'); } }; r.readAsText(f); }; inp.click(); };
    document.getElementById('navResetBtn').onclick = () => { if (confirm('重置导航将丢失自定义数据？')) { store.categories = ['全部','工作','工具','开发','本地应用','查询']; store.sites = [{ name:'微信', url:'weixin://', icon:'fab fa-weixin', category:'本地应用', desc:'微信' },{ name:'腾讯云', url:'https://console.cloud.tencent.com/', icon:'fas fa-cloud', category:'工作' },{ name:'阿里云', url:'https://homenew.console.aliyun.com/', icon:'fas fa-server', category:'工作' },{ name:'飞书', url:'https://www.feishu.cn/', icon:'fab fa-feishu', category:'工作' }]; saveAll(); renderNavTabs(); renderNavSites(); updateCategorySelect(); updateDashboard(); } };
    document.getElementById('navSearchInput').oninput = (e) => { store.searchTerm = e.target.value; renderNavSites(); };
    document.getElementById('openIconLibraryBtn').onclick = () => openIconLibrary();
    
    document.getElementById('smartImportAuditBtn').onclick = () => document.getElementById('auditImportModal').classList.add('show');
    document.getElementById('cancelAuditImport').onclick = () => document.getElementById('auditImportModal').classList.remove('show');
    document.getElementById('confirmAuditImport').onclick = () => { let raw = document.getElementById('auditRawText').value; if (!raw.trim()) return alert('请输入文本'); let projs = parseAuditText(raw); let added = 0; projs.forEach(p => { if (addAuditProject(p)) added++; }); document.getElementById('auditImportModal').classList.remove('show'); alert(`成功导入 ${added} 个审核项目`); };
    document.getElementById('uploadAuditJsonBtn').onclick = () => { let inp = document.createElement('input'); inp.type = 'file'; inp.accept = 'application/json'; inp.onchange = e => { let f = e.target.files[0]; if (!f) return; let r = new FileReader(); r.onload = ev => { try { let data = JSON.parse(ev.target.result); if (Array.isArray(data)) { let added = 0; data.forEach(p => { if (p.auditStartDate && p.auditEndDate && p.displayName && addAuditProject(p)) added++; }); alert(`成功导入 ${added} 个审核项目`); document.getElementById('auditImportModal').classList.remove('show'); } else alert('需上传审核项目数组JSON'); } catch(err) { alert('解析失败'); } }; r.readAsText(f); }; inp.click(); };
    document.getElementById('exportAuditBtn').onclick = () => { let blob = new Blob([JSON.stringify(store.auditProjects, null, 2)], { type: 'application/json' }); let a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'audit_projects.json'; a.click(); };
    document.getElementById('exportCsvBtn').onclick = () => exportToCSV();
    document.getElementById('backupNowBtn').onclick = () => createBackup();
    document.getElementById('restoreBackupBtn').onclick = () => restoreBackup();
    
    document.getElementById('addTodoBtn').onclick = () => { 
        let title = document.getElementById('todoTitle').value.trim(); 
        let start = document.getElementById('todoStart').value; 
        let end = document.getElementById('todoEnd').value; 
        if (!title) return alert('请输入待办内容'); 
        if (!start || !end) return alert('请选择时间'); 
        if (new Date(end) <= new Date(start)) return alert('结束时间必须晚于开始'); 
        store.todos.push({ 
            id: Date.now()+'-'+Math.random(), 
            title, 
            start: new Date(start).toISOString(), 
            end: new Date(end).toISOString(), 
            created: new Date().toISOString(), 
            lastRemindedAt: null, 
            isFromAudit: false 
        }); 
        sortTodos(); 
        saveAll(); 
        renderTodos(); 
        updateDashboard(); 
        document.getElementById('todoTitle').value = ''; 
        let now = new Date(); 
        document.getElementById('todoStart').value = new Date(now.getTime() + 3600000).toISOString().slice(0,16); 
        document.getElementById('todoEnd').value = new Date(now.getTime() + 7200000).toISOString().slice(0,16); 
    };
    document.getElementById('importMemoBtn').onclick = () => { let inp = document.createElement('input'); inp.type = 'file'; inp.accept = 'application/json'; inp.onchange = e => { let f = e.target.files[0]; if (!f) return; let r = new FileReader(); r.onload = ev => { try { let d = JSON.parse(ev.target.result); if (d.todos && d.history) { store.todos = d.todos; store.history = d.history; sortTodos(); saveAll(); renderTodos(); updateDashboard(); alert('导入成功'); } else alert('无效备忘录文件'); } catch(err) { alert('解析失败'); } }; r.readAsText(f); }; inp.click(); };
    document.getElementById('exportMemoBtn').onclick = () => { let blob = new Blob([JSON.stringify({ todos: store.todos, history: store.history })], { type: 'application/json' }); let a = document.createElement('a'); a.href = URL.createObjectURL(blob); a.download = 'memo_backup.json'; a.click(); };
    
    document.getElementById('showHistoryBtn').onclick = () => { 
        let modal = document.getElementById('historyModal'); 
        let list = document.getElementById('historyModalList'); 
        if (store.history.length === 0) list.innerHTML = '<div class="empty-message">暂无历史事项</div>'; 
        else { 
            let html = ''; 
            store.history.forEach((item, idx) => { 
                let startStr = new Date(item.start).toLocaleString(), endStr = new Date(item.end).toLocaleString(), sourceTag = item.isFromAudit ? ' (待办)' : (item.isArchivedAudit ? ' 📋 已完成审核' : '');
                html += `<div class="history-item"><strong>${escapeHtml(item.title)}${sourceTag}</strong><br><small>${startStr} → ${endStr}</small><div><button class="del-history" data-idx="${idx}" style="background:#f44336;color:white;border:none;border-radius:1rem;padding:2px 8px;margin-top:4px;">删除</button></div></div>`; 
            }); 
            list.innerHTML = html; 
            list.querySelectorAll('.del-history').forEach(btn => btn.onclick = () => { let idx = btn.dataset.idx; store.history.splice(idx, 1); saveAll(); document.getElementById('showHistoryBtn').click(); }); 
        } 
        modal.classList.add('show'); 
    };
    document.getElementById('closeHistoryModal').onclick = () => document.getElementById('historyModal').classList.remove('show');
    
    document.getElementById('closeIconSelector').onclick = () => document.getElementById('iconSelectorModal').classList.remove('show');
    document.getElementById('applyCustomIcon').onclick = () => { let val = document.getElementById('customEmoji').value.trim(); if (val && currentEditingIconSiteIdx !== null) { store.sites[currentEditingIconSiteIdx].icon = val; addIconToLibrary(val); saveAll(); renderNavSites(); document.getElementById('iconSelectorModal').classList.remove('show'); } };
    document.getElementById('uploadImageBtn').onclick = () => document.getElementById('imageUpload').click();
    document.getElementById('imageUpload').onchange = (e) => { let file = e.target.files[0]; if (file && currentEditingIconSiteIdx !== null) { let reader = new FileReader(); reader.onload = ev => { store.sites[currentEditingIconSiteIdx].icon = ev.target.result; addIconToLibrary(ev.target.result); saveAll(); renderNavSites(); document.getElementById('iconSelectorModal').classList.remove('show'); }; reader.readAsDataURL(file); } };
    
    document.getElementById('cancelTodoEdit').onclick = () => document.getElementById('todoEditModal').classList.remove('show');
    document.getElementById('confirmTodoEdit').onclick = () => {
        if (currentEditingTodoIdx === null) return;
        let newTitle = document.getElementById('editTodoTitle').value.trim();
        let newStart = document.getElementById('editTodoStart').value;
        let newEnd = document.getElementById('editTodoEnd').value;
        if (!newTitle) return alert('待办内容不能为空');
        if (!newStart || !newEnd) return alert('请选择时间');
        if (new Date(newEnd) <= new Date(newStart)) return alert('结束时间必须晚于开始时间');
        let todo = store.todos[currentEditingTodoIdx];
        if (!todo || todo.isFromAudit) return alert('不能编辑审核待办');
        todo.title = newTitle;
        todo.start = new Date(newStart).toISOString();
        todo.end = new Date(newEnd).toISOString();
        sortTodos();
        saveAll();
        renderTodos();
        updateDashboard();
        document.getElementById('todoEditModal').classList.remove('show');
        currentEditingTodoIdx = null;
    };
    
    document.getElementById('cancelRemark').onclick = () => document.getElementById('remarkModal').classList.remove('show');
    document.getElementById('saveRemark').onclick = () => saveRemark();
    window.openRemarkModal = openRemarkModal;
    
    document.getElementById('closeIconLibrary').onclick = () => document.getElementById('iconLibraryModal').classList.remove('show');
    document.getElementById('batchUploadIconsBtn').onclick = () => batchUploadIcons();
    
    renderNavTabs(); renderNavSites(); updateCategorySelect();
    renderAudit(); renderTodos(); updateDashboard();
    switchToView('dashboard');
    
    // 每次打开页面时检查并提醒自定义待办
    checkAndRemindCustomTodos();
    
    // 定时提醒（每30分钟检查一次，避免频繁弹窗）
    setInterval(() => { 
        let now = Date.now(); 
        store.todos.forEach(todo => { 
            if (todo.isFromAudit) return; // 审核待办不弹窗
            let end = new Date(todo.end).getTime(); 
            if (end - now < 3600000 && end > now && (!todo.lastRemindedAt || (now - todo.lastRemindedAt) > 3600000)) { 
                todo.lastRemindedAt = now; 
                saveAll(); 
                alert(`⏰ 待办即将到期：${todo.title}`); 
            } 
        }); 
    }, 30 * 60 * 1000);
    
    let now = new Date();
    document.getElementById('todoStart').value = new Date(now.getTime() + 3600000).toISOString().slice(0,16);
    document.getElementById('todoEnd').value = new Date(now.getTime() + 7200000).toISOString().slice(0,16);
});
</script>
</body>
</html>
