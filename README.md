<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>三角洲行动 - 枪械综合评分</title>
    <style>
        :root {
            --bg-color: #080c10; --panel-bg: #0e141b; --panel-bg-sub: #131b24;
            --border-color: #1e2936; --text-color: #f1f5f9; --text-muted: #94a3b8;
            --accent-orange: #ff5a00; --accent-blue: #3b82f6; --accent-green: #10b981;
        }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: sans-serif; }
        body { background-color: var(--bg-color); color: var(--text-color); padding: 20px 10px; }
        .container { max-width: 1200px; margin: 0 auto; }
        header { border-bottom: 2px solid rgba(255, 90, 0, 0.4); padding-bottom: 15px; margin-bottom: 25px; display: flex; align-items: center; gap: 12px; }
        .title-indicator { width: 6px; height: 24px; background-color: var(--accent-orange); }
        header h1 { font-size: 1.4rem; font-weight: 900; }
        .main-layout { display: grid; grid-template-columns: 1fr; gap: 25px; }
        @media (min-width: 1024px) { .main-layout { grid-template-columns: 7fr 5fr; } }
        
        /* 全局状态控制栏 */
        .global-control { background: var(--panel-bg); border: 1px solid var(--border-color); padding: 15px; border-radius: 12px; margin-bottom: 15px; display: flex; align-items: center; gap: 15px; }
        .main-toggle { background: rgba(16, 185, 129, 0.2); border: 1px solid var(--accent-green); color: #10b981; padding: 10px 20px; font-size: 1rem; font-weight: bold; border-radius: 6px; cursor: pointer; transition: all 0.2s; }
        .main-toggle.minus-mode { background: rgba(239, 68, 68, 0.2); border-color: #ef4444; color: #f87171; }
        
        .input-panel { background-color: var(--panel-bg); border: 1px solid var(--border-color); border-radius: 12px; padding: 20px; }
        .attribute-section { background-color: var(--panel-bg-sub); border: 1px solid rgba(30, 41, 54, 0.5); border-radius: 8px; padding: 15px; margin-bottom: 15px; }
        .section-title { font-size: 0.85rem; font-weight: bold; color: var(--accent-orange); margin-bottom: 15px; border-left: 3px solid var(--accent-orange); padding-left: 6px; }
        .grid-inputs { display: grid; grid-template-columns: 1fr; gap: 12px; }
        .input-group { display: flex; flex-direction: column; gap: 8px; background: rgba(0,0,0,0.15); padding: 10px; border-radius: 6px; }
        .input-header { display: flex; justify-content: space-between; font-size: 0.85rem; color: #cbd5e1; }
        .input-val-display { color: var(--accent-orange); font-weight: bold; font-family: monospace; }
        .input-control-row { display: flex; align-items: center; gap: 10px; flex-wrap: wrap; }
        .btn-group { display: flex; gap: 4px; flex-grow: 1; }
        .calc-btn { background: #1e2936; color: #fff; border: 1px solid #2d3d52; padding: 6px 0; font-size: 0.8rem; border-radius: 4px; cursor: pointer; flex: 1; font-weight: bold; }
        .calc-btn:hover { background: #273549; border-color: #3b82f6; }
        
        /* 优化后的可直接输入数值的文本框样式 */
        .number-box { 
            background-color: var(--bg-color); 
            border: 1px solid var(--border-color); 
            color: #fff; 
            width: 80px; 
            text-align: center; 
            font-size: 0.9rem; 
            padding: 5px 0; 
            border-radius: 4px; 
            outline: none; 
            font-weight: bold;
            transition: all 0.2s ease-in-out;
        }
        .number-box:focus {
            border-color: var(--accent-orange);
            box-shadow: 0 0 0 2px rgba(255, 90, 0, 0.3);
            background-color: #131b24;
        }
        
        .results-panel { display: flex; flex-direction: column; gap: 20px; }
        .score-card { background-color: var(--panel-bg); border: 1px solid var(--border-color); border-radius: 12px; padding: 20px; position: relative; overflow: hidden; }
        .score-card::before { content: ''; position: absolute; left: 0; top: 0; height: 100%; width: 4px; }
        .card-battle::before { background-color: var(--accent-blue); }
        .card-hazard::before { background-color: var(--accent-green); }
        .card-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px; }
        .score-number { font-size: 2.2rem; font-weight: 900; font-family: monospace; }
        .card-battle .score-number { color: var(--accent-blue); }
        .card-hazard .score-number { color: var(--accent-green); }
        .progress-container { background-color: #1e293b; height: 8px; border-radius: 4px; overflow: hidden; margin-bottom: 15px; }
        .progress-bar { height: 100%; transition: width 0.3s; }
        .card-battle .progress-bar { background-color: var(--accent-blue); }
        .card-hazard .progress-bar { background-color: var(--accent-green); }
        .formula-box { background-color: rgba(0, 0, 0, 0.4); padding: 8px 12px; border-radius: 6px; font-family: monospace; font-size: 0.65rem; color: var(--text-muted); margin-bottom: 15px; }
        .verdict-box { background-color: rgba(255,255,255,0.02); border: 1px solid rgba(255,255,255,0.05); padding: 16px 12px; border-radius: 8px; text-align: center; font-weight: bold; }
        footer { margin-top: 40px; text-align: center; font-size: 0.75rem; color: var(--text-muted); }
    </style>
</head>
<body>

<div class="container">
    <header>
        <span class="title-indicator"></span>
        <h1>三角洲行动枪械数据综合评分面板</h1>
    </header>

    <div class="global-control">
        <span>全局加减模式切换：</span>
        <button class="main-toggle" id="globalToggle" onclick="toggleGlobalMode()">当前状态：【 增加 + 】</button>
    </div>

    <div class="main-layout">
        <section class="input-panel" id="inputContainer"></section>

        <section class="results-panel">
            <div class="score-card card-battle">
                <div class="card-header"><h3>全面战场模式评分</h3><div class="score-number" id="score_battle">0.0</div></div>
                <div class="progress-container"><div id="bar_battle" class="progress-bar" style="width: 0%"></div></div>
                <div class="formula-box">((肉伤×射程×后坐)/1000) × ((据枪+操速)/100) + ((射速+初速)/100)×容量 + (腰射/10)</div>
                <div class="verdict-box">杀人效率 (20分钟)：预计淘汰 <span id="kill_efficiency" style="color: var(--accent-orange); font-size: 1.5rem; font-family: monospace;">0</span> 人</div>
            </div>

            <div class="score-card card-hazard">
                <div class="card-header"><h3>烽火地带 (撤离摸金) 评分</h3><div class="score-number" id="score_hazard">0.0</div></div>
                <div class="progress-container"><div id="bar_hazard" class="progress-bar" style="width: 0%"></div></div>
                <div class="formula-box">((肉伤+甲伤)/10) × ((据枪+腰射+操速)/100) × 后坐 × (1+射程/100) + (射速/10)×(容量/(容量+30))×(初速/100)</div>
                <div class="verdict-box">适合进入：<span id="hazard_suitability" style="font-size: 1.3rem;">分析中...</span></div>
            </div>
        </section>
    </div>
    <footer><p>© 2026 三角洲行动数据分析面板</p></footer>
</div>

<script>
    // 全局正负状态：true 为加，false 为减
    let isPlusMode = true;

    // 所有枪械属性配置数据集
    const attributes = [
        { section: "杀伤与弹道", items: [
            { id: "meat", name: "基础伤害", val: 26, min: 0, max: 100 },
            { id: "armor", name: "护甲伤害", val: 28, min: 0, max: 100 },
            { id: "rpm", name: "射速", val: 80, min: 0, max: 1500 },
            { id: "velocity", name: "枪口初速", val: 85, min: 0, max: 1500 }
        ]},
        { section: "操控与有效范围", items: [
            { id: "range", name: "优势射程", val: 70, min: 0, max: 350 },
            { id: "recoil", name: "后座力控制", val: 75, min: 0, max: 100 },
            { id: "ads", name: "举枪稳定性", val: 65, min: 0, max: 100 },
            { id: "mobility", name: "操控速度", val: 70, min: 0, max: 100 }
        ]},
        { section: "容错与散布控制", items: [
            { id: "hip", name: "腰际射击精度", val: 55, min: 0, max: 100 },
            { id: "cap", name: "容量", val: 50, min: 0, max: 150 }
        ]}
    ];

    // 初始化：自动渲染网页输入框与控制项
    window.onload = function() {
        const container = document.getElementById('inputContainer');
        let html = '';
        attributes.forEach(sec => {
            html += `<div class="attribute-section"><div class="section-title">${sec.section}</div><div class="grid-inputs">`;
            sec.items.forEach(item => {
                html += `
                    <div class="input-group">
                        <div class="input-header"><span>${item.name}</span><span class="input-val-display" id="val_${item.id}">${item.val}</span></div>
                        <div class="input-control-row">
                            <div class="btn-group">
                                <button class="calc-btn" onclick="adjustValue('${item.id}', 1)">1</button>
                                <button class="calc-btn" onclick="adjustValue('${item.id}', 5)">5</button>
                                <button class="calc-btn" onclick="adjustValue('${item.id}', 10)">10</button>
                                <button class="calc-btn" onclick="adjustValue('${item.id}', 20)">20</button>
                            </div>
                            <!-- 优化后的直接数值输入框：oninput实时计算但允许任意临时输入，onblur执行合规校对 -->
                            <input type="number" 
                                   id="num_${item.id}" 
                                   class="number-box" 
                                   value="${item.val}" 
                                   data-min="${item.min}" 
                                   data-max="${item.max}" 
                                   placeholder="输入"
                                   oninput="syncNum('${item.id}')"
                                   onblur="clampNumValue('${item.id}')">
                        </div>
                    </div>`;
            });
            html += `</div></div>`;
        });
        container.innerHTML = html;
        calculateScores();
    };

    // 切换全局主开关状态
    function toggleGlobalMode() {
        isPlusMode = !isPlusMode;
        const btn = document.getElementById('globalToggle');
        if (isPlusMode) {
            btn.innerText = "当前状态：【 增加 + 】";
            btn.className = "main-toggle";
        } else {
            btn.innerText = "当前状态：【 减少 - 】";
            btn.className = "main-toggle minus-mode";
        }
    }

    // 所有属性共用这一个加减逻辑（针对快速加减按钮）
    function adjustValue(key, delta) {
        const inputNum = document.getElementById(`num_${key}`);
        let currentVal = parseInt(inputNum.value) || 0;
        const min = parseInt(inputNum.getAttribute('data-min'));
        const max = parseInt(inputNum.getAttribute('data-max'));

        currentVal = isPlusMode ? (currentVal + delta) : (currentVal - delta);

        if (currentVal < min) currentVal = min;
        if (currentVal > max) currentVal = max;

        inputNum.value = currentVal;
        document.getElementById(`val_${key}`).innerText = currentVal;
        calculateScores();
    }

    // 当用户直接在输入框中打字时触发（实时更新，且不会打断用户输入）
    function syncNum(key) {
        const inputNum = document.getElementById(`num_${key}`);
        let val = parseInt(inputNum.value);
        
        // 如果输入为空，暂不强制重置，允许用户删光重新打字
        if (isNaN(val)) {
            document.getElementById(`val_${key}`).innerText = "0";
            calculateScores();
            return;
        }

        const max = parseInt(inputNum.getAttribute('data-max'));

        // 如果单次打字直接超过了最大值上限，将其限制在最大值
        if (val > max) {
            val = max;
            inputNum.value = val;
        }

        document.getElementById(`val_${key}`).innerText = val;
        calculateScores();
    }

    // 当输入框失去焦点（点击空白处）时，自动纠正不合规的超限数值
    function clampNumValue(key) {
        const inputNum = document.getElementById(`num_${key}`);
        let val = parseInt(inputNum.value);
        const min = parseInt(inputNum.getAttribute('data-min'));
        const max = parseInt(inputNum.getAttribute('data-max'));

        if (isNaN(val) || val < min) {
            val = min;
        } else if (val > max) {
            val = max;
        }

        inputNum.value = val;
        document.getElementById(`val_${key}`).innerText = val;
        calculateScores();
    }

    // 计算综合评分
    function calculateScores() {
        const getValue = (id) => {
            const val = parseFloat(document.getElementById(`num_${id}`).value);
            return isNaN(val) ? 0 : val;
        };
        
        const meat = getValue('meat'), armor = getValue('armor'), range = getValue('range'),
              recoil = getValue('recoil'), ads = getValue('ads'), mobility = getValue('mobility'),
              hip = getValue('hip'), rpm = getValue('rpm'), velocity = getValue('velocity'), r_cap = getValue('cap');

        // 全面战场计算
        const score_battle = (((meat * range * recoil) / 1000) * ((ads + mobility) / 100)) + (((rpm + velocity) / 100) * r_cap) + (hip / 10);
        document.getElementById('score_battle').innerText = score_battle.toFixed(1);
        document.getElementById('bar_battle').style.width = `${Math.min(100, (score_battle / 350) * 100)}%`;
        document.getElementById('kill_efficiency').innerText = Math.round(score_battle / 8);

        // 烽火地带计算
        const score_hazard = (((meat + armor) / 10) * ((ads + hip + mobility) / 100) * recoil * (1 + (range / 100))) + ((rpm / 10) * (r_cap / (r_cap + 30)) * (velocity / 100));
        document.getElementById('score_hazard').innerText = score_hazard.toFixed(1);
        document.getElementById('bar_hazard').style.width = `${Math.min(100, (score_hazard / 1600) * 100)}%`;

        let suit_text = "普通模式", suit_color = "var(--accent-green)";
        if (score_hazard >= 1400) { suit_text = "绝密模式"; suit_color = "var(--accent-orange)"; }
        else if (score_hazard >= 900) { suit_text = "机密模式"; suit_color = "var(--accent-blue)"; }
        
        const suit_element = document.getElementById('hazard_suitability');
        suit_element.innerText = suit_text; suit_element.style.color = suit_color;
    }
</script>

</body>
</html>
