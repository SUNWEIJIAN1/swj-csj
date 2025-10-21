<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>计算</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/11.8.0/math.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #1a2a6c, #b21f1f, #fdbb2d);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        
        .calculator-container {
            display: flex;
            flex-direction: column;
            max-width: 1200px;
            width: 100%;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            overflow: hidden;
        }
        
        .calculator-header {
            background: rgba(0, 0, 0, 0.2);
            color: white;
            padding: 20px;
            text-align: center;
        }
        
        .calculator-header h1 {
            font-size: 2.2rem;
            margin-bottom: 10px;
            text-shadow: 0 2px 5px rgba(0, 0, 0, 0.3);
        }
        
        .calculator-body {
            display: flex;
            flex-wrap: wrap;
            padding: 20px;
            gap: 20px;
        }
        
        .calculator-main {
            flex: 1;
            min-width: 300px;
        }
        
        .calculator-sidebar {
            flex: 1;
            min-width: 300px;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }
        
        .display-container {
            background: rgba(0, 0, 0, 0.7);
            border-radius: 10px;
            padding: 15px;
            margin-bottom: 20px;
            box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.5);
        }
        
        .expression-display {
            color: #aaa;
            font-size: 1.2rem;
            min-height: 30px;
            word-wrap: break-word;
        }
        
        .result-display {
            color: white;
            font-size: 2.5rem;
            text-align: right;
            min-height: 60px;
            word-wrap: break-word;
        }
        
        .buttons-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 12px;
        }
        
        .btn {
            padding: 15px 10px;
            border: none;
            border-radius: 10px;
            font-size: 1.2rem;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        
        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 8px rgba(0, 0, 0, 0.2);
        }
        
        .btn:active {
            transform: translateY(1px);
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }
        
        .btn-number {
            background: #3498db;
            color: white;
        }
        
        .btn-operator {
            background: #e74c3c;
            color: white;
        }
        
        .btn-function {
            background: #9b59b6;
            color: white;
        }
        
        .btn-comma {
            background: #16a085;
            color: white;
        }
        
        .btn-equals {
            background: #2ecc71;
            color: white;
            grid-column: span 2;
        }
        
        .btn-clear {
            background: #e67e22;
            color: white;
        }
        
        .btn-memory {
            background: #34495e;
            color: white;
        }
        
        .panel {
            background: rgba(255, 255, 255, 0.9);
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
        }
        
        .panel h2 {
            color: #2c3e50;
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid #eee;
        }
        
        .history-list {
            max-height: 200px;
            overflow-y: auto;
        }
        
        .history-item {
            padding: 8px 0;
            border-bottom: 1px solid #eee;
            font-family: monospace;
        }
        
        .variables-list {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
            gap: 10px;
        }
        
        .variable-item {
            background: #f8f9fa;
            padding: 10px;
            border-radius: 5px;
            text-align: center;
            font-family: monospace;
            border: 1px solid #e9ecef;
            cursor: pointer;
            transition: all 0.2s ease;
        }
        
        .variable-item:hover {
            background: #e9ecef;
            transform: translateY(-2px);
        }
        
        .variable-name {
            font-weight: bold;
            color: #2c3e50;
        }
        
        .variable-value {
            color: #e74c3c;
        }
        
        .instructions {
            background: rgba(255, 255, 255, 0.9);
            border-radius: 10px;
            padding: 20px;
            margin: 20px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
        }
        
        .instructions h2 {
            color: #2c3e50;
            margin-bottom: 15px;
        }
        
        .instructions ul {
            padding-left: 20px;
        }
        
        .instructions li {
            margin-bottom: 8px;
            line-height: 1.5;
        }
        
        /* 新功能样式 */
        .advanced-tabs {
            display: flex;
            margin-bottom: 15px;
            border-bottom: 1px solid #ddd;
        }
        
        .tab {
            padding: 8px 15px;
            cursor: pointer;
            background: #f8f9fa;
            border: 1px solid #ddd;
            border-bottom: none;
            border-radius: 5px 5px 0 0;
            margin-right: 5px;
        }
        
        .tab.active {
            background: #3498db;
            color: white;
            border-color: #3498db;
        }
        
        .tab-content {
            display: none;
        }
        
        .tab-content.active {
            display: block;
        }
        
        .input-group {
            margin-bottom: 10px;
        }
        
        .input-group label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        
        .input-group input, .input-group select {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        
        .matrix-input {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 5px;
            margin-bottom: 10px;
        }
        
        .matrix-input input {
            text-align: center;
        }
        
        .matrix-size-selector {
            margin-bottom: 10px;
        }
        
        .matrix-size-selector select {
            padding: 5px;
        }
        
        .equation-input {
            display: grid;
            grid-template-columns: 1fr auto 1fr auto 1fr auto 1fr;
            gap: 5px;
            align-items: center;
            margin-bottom: 10px;
        }
        
        .equation-input input {
            text-align: center;
        }
        
        .result-box {
            background: #f8f9fa;
            padding: 10px;
            border-radius: 5px;
            margin-top: 10px;
            border: 1px solid #e9ecef;
            font-family: monospace;
        }
        
        .action-button {
            background: #3498db;
            color: white;
            border: none;
            padding: 8px 15px;
            border-radius: 4px;
            cursor: pointer;
            margin-top: 10px;
            width: 100%;
        }
        
        .action-button:hover {
            background: #2980b9;
        }
        
        @media (max-width: 768px) {
            .calculator-body {
                flex-direction: column;
            }
            
            .buttons-grid {
                grid-template-columns: repeat(4, 1fr);
            }
            
            .equation-input {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="calculator-container">
        <div class="calculator-header">
            <h1>计算器</h1>
        </div>
        
        <div class="calculator-body">
            <div class="calculator-main">
                <div class="display-container">
                    <div class="expression-display" id="expression"></div>
                    <div class="result-display" id="result">0</div>
                </div>
                
                <div class="buttons-grid">
                    <!-- 第一行：清除和内存功能 -->
                    <button class="btn btn-clear" onclick="clearAll()">C</button>
                    <button class="btn btn-clear" onclick="clearEntry()">CE</button>
                    <button class="btn btn-memory" onclick="storeVariable()">M+</button>
                    <button class="btn btn-memory" onclick="recallVariable()">MR</button>
                    <button class="btn btn-function" onclick="appendToExpression('!')">x!</button>
                    
                    <!-- 第二行：数字 -->
                    <button class="btn btn-number" onclick="appendToExpression('7')">7</button>
                    <button class="btn btn-number" onclick="appendToExpression('8')">8</button>
                    <button class="btn btn-number" onclick="appendToExpression('9')">9</button>
                    <button class="btn btn-operator" onclick="appendToExpression('/')">/</button>
                    <button class="btn btn-function" onclick="appendToExpression('(')">(</button>
                    
                    <!-- 第三行：数字 -->
                    <button class="btn btn-number" onclick="appendToExpression('4')">4</button>
                    <button class="btn btn-number" onclick="appendToExpression('5')">5</button>
                    <button class="btn btn-number" onclick="appendToExpression('6')">6</button>
                    <button class="btn btn-operator" onclick="appendToExpression('*')">×</button>
                    <button class="btn btn-function" onclick="appendToExpression(')')">)</button>
                    
                    <!-- 第四行：数字 -->
                    <button class="btn btn-number" onclick="appendToExpression('1')">1</button>
                    <button class="btn btn-number" onclick="appendToExpression('2')">2</button>
                    <button class="btn btn-number" onclick="appendToExpression('3')">3</button>
                    <button class="btn btn-operator" onclick="appendToExpression('-')">-</button>
                    <button class="btn btn-function" onclick="appendToExpression('^')">x^y</button>
                    
                    <!-- 第五行：数字和基本操作 -->
                    <button class="btn btn-number" onclick="appendToExpression('0')">0</button>
                    <button class="btn btn-number" onclick="appendToExpression('.')">.</button>
                    <button class="btn btn-comma" onclick="appendToExpression(',')">,</button>
                    <button class="btn btn-operator" onclick="appendToExpression('+')">+</button>
                    <button class="btn btn-equals" onclick="calculate()">=</button>
                    
                    <!-- 第六行：三角函数 -->
                    <button class="btn btn-function" onclick="appendToExpression('sin(')">sin</button>
                    <button class="btn btn-function" onclick="appendToExpression('cos(')">cos</button>
                    <button class="btn btn-function" onclick="appendToExpression('tan(')">tan</button>
                    <button class="btn btn-function" onclick="appendToExpression('asin(')">asin</button>
                    <button class="btn btn-function" onclick="appendToExpression('acos(')">acos</button>
                    
                    <!-- 第七行：对数和常数 -->
                    <button class="btn btn-function" onclick="appendToExpression('atan(')">atan</button>
                    <button class="btn btn-function" onclick="appendToExpression('lg(')">lg</button>
                    <button class="btn btn-function" onclick="appendToExpression('ln(')">ln</button>
                    <button class="btn btn-function" onclick="appendCustomLog()">log(a,b)</button>
                    <button class="btn btn-function" onclick="appendToExpression('π')">π</button>
                </div>
            </div>
            
            <div class="calculator-sidebar">
                <div class="panel">
                    <h2>历史记录</h2>
                    <div class="history-list" id="historyList">
                        <!-- 历史记录将在这里显示 -->
                    </div>
                </div>
                
                <div class="panel">
                    <h2>存储的变量 (点击使用)</h2>
                    <div class="variables-list" id="variablesList">
                        <!-- 变量将在这里显示 -->
                    </div>
                </div>
                
                <!-- 新增高级功能面板 -->
                <div class="panel">
                    <h2>高级功能</h2>
                    <div class="advanced-tabs">
                        <div class="tab active" onclick="switchTab('base-conversion')">进制转换</div>
                        <div class="tab" onclick="switchTab('equation-solver')">方程求解</div>
                        <div class="tab" onclick="switchTab('matrix-calculator')">矩阵计算</div>
                    </div>
                    
                    <!-- 进制转换 -->
                    <div id="base-conversion" class="tab-content active">
                        <div class="input-group">
                            <label for="inputNumber">输入数值</label>
                            <input type="text" id="inputNumber" placeholder="输入数值">
                        </div>
                        <div class="input-group">
                            <label for="fromBase">从进制</label>
                            <select id="fromBase">
                                <option value="2">二进制 (2)</option>
                                <option value="8">八进制 (8)</option>
                                <option value="10" selected>十进制 (10)</option>
                                <option value="16">十六进制 (16)</option>
                            </select>
                        </div>
                        <div class="input-group">
                            <label for="toBase">转换到</label>
                            <select id="toBase">
                                <option value="2">二进制 (2)</option>
                                <option value="8">八进制 (8)</option>
                                <option value="10">十进制 (10)</option>
                                <option value="16" selected>十六进制 (16)</option>
                            </select>
                        </div>
                        <button class="action-button" onclick="convertBase()">转换</button>
                        <div class="result-box" id="baseResult"></div>
                    </div>
                    
                    <!-- 方程求解 -->
                    <div id="equation-solver" class="tab-content">
                        <div class="input-group">
                            <label>方程类型</label>
                            <select id="equationType" onchange="changeEquationType()">
                                <option value="linear">一元一次方程</option>
                                <option value="quadratic">一元二次方程</option>
                                <option value="system">线性方程组</option>
                            </select>
                        </div>
                        
                        <!-- 一元一次方程 -->
                        <div id="linear-equation" class="equation-type">
                            <div class="equation-input">
                                <input type="number" id="linearA" placeholder="a" value="1">
                                <span>x +</span>
                                <input type="number" id="linearB" placeholder="b" value="1">
                                <span>=</span>
                                <input type="number" id="linearC" placeholder="c" value="0">
                            </div>
                        </div>
                        
                        <!-- 一元二次方程 -->
                        <div id="quadratic-equation" class="equation-type" style="display:none">
                            <div class="equation-input">
                                <input type="number" id="quadA" placeholder="a" value="1">
                                <span>x² +</span>
                                <input type="number" id="quadB" placeholder="b" value="0">
                                <span>x +</span>
                                <input type="number" id="quadC" placeholder="c" value="-1">
                                <span>= 0</span>
                            </div>
                        </div>
                        
                        <!-- 线性方程组 -->
                        <div id="system-equation" class="equation-type" style="display:none">
                            <div class="equation-input">
                                <input type="number" id="sysA1" placeholder="a1" value="1">
                                <span>x +</span>
                                <input type="number" id="sysB1" placeholder="b1" value="1">
                                <span>y =</span>
                                <input type="number" id="sysC1" placeholder="c1" value="3">
                            </div>
                            <div class="equation-input">
                                <input type="number" id="sysA2" placeholder="a2" value="1">
                                <span>x +</span>
                                <input type="number" id="sysB2" placeholder="b2" value="-1">
                                <span>y =</span>
                                <input type="number" id="sysC2" placeholder="c2" value="1">
                            </div>
                        </div>
                        
                        <button class="action-button" onclick="solveEquation()">求解</button>
                        <div class="result-box" id="equationResult"></div>
                    </div>
                    
                    <!-- 矩阵计算 -->
                    <div id="matrix-calculator" class="tab-content">
                        <div class="input-group">
                            <label>矩阵操作</label>
                            <select id="matrixOperation" onchange="changeMatrixOperation()">
                                <option value="add">矩阵加法</option>
                                <option value="subtract">矩阵减法</option>
                                <option value="multiply">矩阵乘法</option>
                                <option value="determinant">行列式</option>
                                <option value="inverse">逆矩阵</option>
                            </select>
                        </div>
                        
                        <div class="matrix-size-selector">
                            <label>矩阵大小:</label>
                            <select id="matrixSize" onchange="changeMatrixSize()">
                                <option value="2">2x2</option>
                                <option value="3">3x3</option>
                            </select>
                        </div>
                        
                        <div class="matrix-container">
                            <div class="input-group">
                                <label>矩阵 A</label>
                                <div id="matrixA" class="matrix-input">
                                    <!-- 2x2 矩阵默认 -->
                                    <input type="number" id="a11" value="1">
                                    <input type="number" id="a12" value="0">
                                    <input type="number" id="a21" value="0">
                                    <input type="number" id="a22" value="1">
                                </div>
                            </div>
                            
                            <div class="input-group" id="matrixBContainer">
                                <label>矩阵 B</label>
                                <div id="matrixB" class="matrix-input">
                                    <!-- 2x2 矩阵默认 -->
                                    <input type="number" id="b11" value="1">
                                    <input type="number" id="b12" value="0">
                                    <input type="number" id="b21" value="0">
                                    <input type="number" id="b22" value="1">
                                </div>
                            </div>
                        </div>
                        
                        <button class="action-button" onclick="calculateMatrix()">计算</button>
                        <div class="result-box" id="matrixResult"></div>
                    </div>
                </div>
            </div>
        </div>
        
        <div class="instructions">
            <h2>使用说明</h2>
            <ul>
                <li><strong>基础运算：</strong> 支持加(+)、减(-)、乘(×)、除(/)、括号运算</li>
                <li><strong>三角函数：</strong> sin(角度), cos(角度), tan(角度) - 使用角度制</li>
                <li><strong>反三角函数：</strong> asin(值), acos(值), atan(值)</li>
                <li><strong>对数函数：</strong> lg(值) - 常用对数, ln(值) - 自然对数, log(底数, 真数) - 点击"log(a,b)"按钮自动输入格式</li>
                <li><strong>其他函数：</strong> 阶乘(!), 幂运算(^)</li>
                <li><strong>变量存储：</strong> 使用 M+ 存储当前结果到变量，MR 调用变量，或直接点击变量名使用</li>
                <li><strong>常数：</strong> π 表示圆周率</li>
                <li><strong>逗号输入：</strong> 使用专门的逗号按钮输入自定义对数所需的逗号</li>
                <li><strong>进制转换：</strong> 支持二进制、八进制、十进制和十六进制之间的转换</li>
                <li><strong>方程求解：</strong> 支持一元一次方程、一元二次方程和二元一次方程组的求解</li>
                <li><strong>矩阵计算：</strong> 支持矩阵的加法、减法、乘法、行列式和逆矩阵计算</li>
            </ul>
        </div>
    </div>

    <script>
        // 初始化变量和历史记录
        let variables = {};
        let history = [];
        
        // 页面加载时从本地存储恢复数据
        window.onload = function() {
            const savedVariables = localStorage.getItem('calculatorVariables');
            const savedHistory = localStorage.getItem('calculatorHistory');
            
            if (savedVariables) {
                variables = JSON.parse(savedVariables);
                updateVariablesDisplay();
            }
            
            if (savedHistory) {
                history = JSON.parse(savedHistory);
                updateHistoryDisplay();
            }
        };
        
        // 更新表达式显示
        function appendToExpression(value) {
            const expressionElement = document.getElementById('expression');
            expressionElement.textContent += value;
        }
        
        // 自定义对数格式输入
        function appendCustomLog() {
            const expressionElement = document.getElementById('expression');
            expressionElement.textContent += 'log(,)';
        }
        
        // 修复浮点数精度问题的函数
        function fixFloatPrecision(number) {
            if (!Number.isFinite(number)) return number;
            
            // 处理非常接近整数的浮点数
            if (Math.abs(number - Math.round(number)) < 1e-12) {
                return Math.round(number);
            }
            
            // 处理常见的小数精度问题
            const str = number.toString();
            if (str.includes('.')) {
                // 限制小数位数并去除多余的0
                const fixed = parseFloat(number.toFixed(12));
                // 检查是否是常见的精度问题
                const commonPrecisionIssues = {
                    '0.30000000000000004': 0.3,
                    '0.5599999999999999': 0.56,
                    '0.19999999999999998': 0.2,
                    '0.20999999999999996': 0.21
                };
                
                if (commonPrecisionIssues[str] !== undefined) {
                    return commonPrecisionIssues[str];
                }
                
                return fixed;
            }
            
            return number;
        }
        
        // 预处理表达式，处理变量和自定义对数
        function preprocessExpression(expression) {
            // 替换变量值
            for (const [varName, varValue] of Object.entries(variables)) {
                const regex = new RegExp('\\b' + varName + '\\b', 'g');
                expression = expression.replace(regex, varValue.toString());
            }
            
            // 替换常数
            expression = expression.replace(/π/g, 'pi');
            expression = expression.replace(/lg\(/g, 'log10(');
            expression = expression.replace(/ln\(/g, 'log(');
            
            // 处理自定义对数 log(a,b) -> log(a) / log(b)
            expression = expression.replace(/log\(([^,]+),([^)]+)\)/g, 'log($2)/log($1)');
            
            return expression;
        }
        
        // 计算表达式
        function calculate() {
            const expressionElement = document.getElementById('expression');
            const resultElement = document.getElementById('result');
            
            let expression = expressionElement.textContent;
            
            if (!expression) return;
            
            try {
                // 预处理表达式
                expression = preprocessExpression(expression);
                
                console.log('计算表达式:', expression); // 调试用
                
                // 使用math.js计算
                let result = math.evaluate(expression);
                
                // 修复浮点数精度问题
                result = fixFloatPrecision(result);
                
                // 显示结果
                resultElement.textContent = result;
                
                // 添加到历史记录
                addToHistory(expressionElement.textContent, result);
                
            } catch (error) {
                resultElement.textContent = '错误';
                console.error('计算错误:', error);
            }
        }
        
        // 添加到历史记录
        function addToHistory(expression, result) {
            history.unshift({
                expression: expression,
                result: result,
                timestamp: new Date().toLocaleTimeString()
            });
            
            // 限制历史记录数量
            if (history.length > 10) {
                history.pop();
            }
            
            // 更新显示和本地存储
            updateHistoryDisplay();
            localStorage.setItem('calculatorHistory', JSON.stringify(history));
        }
        
        // 更新历史记录显示
        function updateHistoryDisplay() {
            const historyList = document.getElementById('historyList');
            historyList.innerHTML = '';
            
            history.forEach(item => {
                const historyItem = document.createElement('div');
                historyItem.className = 'history-item';
                historyItem.textContent = `${item.expression} = ${item.result}`;
                historyList.appendChild(historyItem);
            });
        }
        
        // 存储变量
        function storeVariable() {
            const resultElement = document.getElementById('result');
            const result = resultElement.textContent;
            
            if (result === '0' || result === '错误') return;
            
            const varName = prompt('请输入变量名（单个字母）:');
            if (varName && /^[a-zA-Z]$/.test(varName)) {
                variables[varName] = parseFloat(result);
                updateVariablesDisplay();
                localStorage.setItem('calculatorVariables', JSON.stringify(variables));
                alert(`变量 ${varName} 已存储为 ${result}`);
            } else if (varName) {
                alert('变量名必须是单个字母');
            }
        }
        
        // 调用变量
        function recallVariable() {
            const varName = prompt('请输入要调用的变量名:');
            if (varName && variables.hasOwnProperty(varName)) {
                appendToExpression(varName);
            } else if (varName) {
                alert(`变量 ${varName} 不存在`);
            }
        }
        
        // 使用变量（点击变量名直接使用）
        function useVariable(varName) {
            appendToExpression(varName);
        }
        
        // 更新变量显示
        function updateVariablesDisplay() {
            const variablesList = document.getElementById('variablesList');
            variablesList.innerHTML = '';
            
            for (const [name, value] of Object.entries(variables)) {
                const variableItem = document.createElement('div');
                variableItem.className = 'variable-item';
                variableItem.innerHTML = `
                    <div class="variable-name">${name}</div>
                    <div class="variable-value">${value}</div>
                `;
                variableItem.onclick = () => useVariable(name);
                variablesList.appendChild(variableItem);
            }
        }
        
        // 清除所有
        function clearAll() {
            document.getElementById('expression').textContent = '';
            document.getElementById('result').textContent = '0';
        }
        
        // 清除输入
        function clearEntry() {
            const expressionElement = document.getElementById('expression');
            expressionElement.textContent = expressionElement.textContent.slice(0, -1);
        }
        
        // 高级功能部分
        
        // 切换选项卡
        function switchTab(tabName) {
            // 隐藏所有选项卡内容
            document.querySelectorAll('.tab-content').forEach(tab => {
                tab.classList.remove('active');
            });
            
            // 移除所有选项卡的活动状态
            document.querySelectorAll('.tab').forEach(tab => {
                tab.classList.remove('active');
            });
            
            // 显示选定的选项卡内容
            document.getElementById(tabName).classList.add('active');
            
            // 设置选定的选项卡为活动状态
            event.target.classList.add('active');
        }
        
        // 进制转换
        function convertBase() {
            const inputNumber = document.getElementById('inputNumber').value;
            const fromBase = parseInt(document.getElementById('fromBase').value);
            const toBase = parseInt(document.getElementById('toBase').value);
            const resultElement = document.getElementById('baseResult');
            
            if (!inputNumber) {
                resultElement.textContent = '请输入数值';
                return;
            }
            
            try {
                // 将输入值从原始进制转换为十进制
                let decimalValue;
                if (fromBase === 10) {
                    decimalValue = parseFloat(inputNumber);
                } else {
                    decimalValue = parseInt(inputNumber, fromBase);
                }
                
                // 将十进制值转换为目标进制
                let result;
                if (toBase === 10) {
                    result = decimalValue.toString();
                } else {
                    result = decimalValue.toString(toBase).toUpperCase();
                }
                
                resultElement.textContent = `结果: ${result}`;
            } catch (error) {
                resultElement.textContent = '转换错误: 输入值无效';
            }
        }
        
        // 改变方程类型
        function changeEquationType() {
            const equationType = document.getElementById('equationType').value;
            
            // 隐藏所有方程类型
            document.querySelectorAll('.equation-type').forEach(type => {
                type.style.display = 'none';
            });
            
            // 显示选定的方程类型
            document.getElementById(`${equationType}-equation`).style.display = 'block';
        }
        
        // 求解方程
        function solveEquation() {
            const equationType = document.getElementById('equationType').value;
            const resultElement = document.getElementById('equationResult');
            
            try {
                let result;
                
                if (equationType === 'linear') {
                    // 一元一次方程: ax + b = c
                    const a = parseFloat(document.getElementById('linearA').value);
                    const b = parseFloat(document.getElementById('linearB').value);
                    const c = parseFloat(document.getElementById('linearC').value);
                    
                    if (a === 0) {
                        result = '错误: a 不能为 0';
                    } else {
                        const x = (c - b) / a;
                        result = `x = ${fixFloatPrecision(x)}`;
                    }
                } else if (equationType === 'quadratic') {
                    // 一元二次方程: ax² + bx + c = 0
                    const a = parseFloat(document.getElementById('quadA').value);
                    const b = parseFloat(document.getElementById('quadB').value);
                    const c = parseFloat(document.getElementById('quadC').value);
                    
                    if (a === 0) {
                        result = '错误: a 不能为 0';
                    } else {
                        const discriminant = b * b - 4 * a * c;
                        
                        if (discriminant > 0) {
                            const x1 = (-b + Math.sqrt(discriminant)) / (2 * a);
                            const x2 = (-b - Math.sqrt(discriminant)) / (2 * a);
                            result = `x₁ = ${fixFloatPrecision(x1)}, x₂ = ${fixFloatPrecision(x2)}`;
                        } else if (discriminant === 0) {
                            const x = -b / (2 * a);
                            result = `x = ${fixFloatPrecision(x)} (重根)`;
                        } else {
                            const realPart = -b / (2 * a);
                            const imaginaryPart = Math.sqrt(-discriminant) / (2 * a);
                            result = `x₁ = ${fixFloatPrecision(realPart)} + ${fixFloatPrecision(imaginaryPart)}i, x₂ = ${fixFloatPrecision(realPart)} - ${fixFloatPrecision(imaginaryPart)}i`;
                        }
                    }
                } else if (equationType === 'system') {
                    // 线性方程组
                    const a1 = parseFloat(document.getElementById('sysA1').value);
                    const b1 = parseFloat(document.getElementById('sysB1').value);
                    const c1 = parseFloat(document.getElementById('sysC1').value);
                    const a2 = parseFloat(document.getElementById('sysA2').value);
                    const b2 = parseFloat(document.getElementById('sysB2').value);
                    const c2 = parseFloat(document.getElementById('sysC2').value);
                    
                    const determinant = a1 * b2 - a2 * b1;
                    
                    if (determinant === 0) {
                        result = '错误: 方程组无解或有无穷多解';
                    } else {
                        const x = (c1 * b2 - c2 * b1) / determinant;
                        const y = (a1 * c2 - a2 * c1) / determinant;
                        result = `x = ${fixFloatPrecision(x)}, y = ${fixFloatPrecision(y)}`;
                    }
                }
                
                resultElement.textContent = result;
            } catch (error) {
                resultElement.textContent = '计算错误: 请检查输入值';
            }
        }
        
        // 改变矩阵操作
        function changeMatrixOperation() {
            const operation = document.getElementById('matrixOperation').value;
            const matrixBContainer = document.getElementById('matrixBContainer');
            
            // 对于逆矩阵和行列式，只需要一个矩阵
            if (operation === 'determinant' || operation === 'inverse') {
                matrixBContainer.style.display = 'none';
            } else {
                matrixBContainer.style.display = 'block';
            }
        }
        
        // 改变矩阵大小
        function changeMatrixSize() {
            const size = parseInt(document.getElementById('matrixSize').value);
            const matrixA = document.getElementById('matrixA');
            const matrixB = document.getElementById('matrixB');
            
            // 清空矩阵
            matrixA.innerHTML = '';
            matrixB.innerHTML = '';
            
            // 创建新的矩阵输入
            for (let i = 1; i <= size; i++) {
                for (let j = 1; j <= size; j++) {
                    const inputA = document.createElement('input');
                    inputA.type = 'number';
                    inputA.id = `a${i}${j}`;
                    inputA.value = i === j ? '1' : '0';
                    matrixA.appendChild(inputA);
                    
                    const inputB = document.createElement('input');
                    inputB.type = 'number';
                    inputB.id = `b${i}${j}`;
                    inputB.value = i === j ? '1' : '0';
                    matrixB.appendChild(inputB);
                }
            }
            
            // 更新网格布局
            matrixA.style.gridTemplateColumns = `repeat(${size}, 1fr)`;
            matrixB.style.gridTemplateColumns = `repeat(${size}, 1fr)`;
        }
        
        // 矩阵计算
        function calculateMatrix() {
            const operation = document.getElementById('matrixOperation').value;
            const size = parseInt(document.getElementById('matrixSize').value);
            const resultElement = document.getElementById('matrixResult');
            
            try {
                // 获取矩阵A的值
                const matrixA = [];
                for (let i = 1; i <= size; i++) {
                    const row = [];
                    for (let j = 1; j <= size; j++) {
                        const value = parseFloat(document.getElementById(`a${i}${j}`).value) || 0;
                        row.push(value);
                    }
                    matrixA.push(row);
                }
                
                let result;
                
                if (operation === 'determinant') {
                    // 计算行列式
                    const det = math.det(matrixA);
                    result = `行列式 = ${fixFloatPrecision(det)}`;
                } else if (operation === 'inverse') {
                    // 计算逆矩阵
                    const inv = math.inv(matrixA);
                    result = '逆矩阵:\n' + formatMatrix(inv);
                } else {
                    // 获取矩阵B的值
                    const matrixB = [];
                    for (let i = 1; i <= size; i++) {
                        const row = [];
                        for (let j = 1; j <= size; j++) {
                            const value = parseFloat(document.getElementById(`b${i}${j}`).value) || 0;
                            row.push(value);
                        }
                        matrixB.push(row);
                    }
                    
                    // 执行矩阵运算
                    if (operation === 'add') {
                        const sum = math.add(matrixA, matrixB);
                        result = '矩阵和:\n' + formatMatrix(sum);
                    } else if (operation === 'subtract') {
                        const diff = math.subtract(matrixA, matrixB);
                        result = '矩阵差:\n' + formatMatrix(diff);
                    } else if (operation === 'multiply') {
                        const product = math.multiply(matrixA, matrixB);
                        result = '矩阵积:\n' + formatMatrix(product);
                    }
                }
                
                resultElement.textContent = result;
            } catch (error) {
                resultElement.textContent = '计算错误: ' + error.message;
            }
        }
        
        // 格式化矩阵显示
        function formatMatrix(matrix) {
            if (typeof matrix === 'number') {
                return matrix.toString();
            }
            
            let result = '';
            for (let i = 0; i < matrix.length; i++) {
                result += '[';
                for (let j = 0; j < matrix[i].length; j++) {
                    result += fixFloatPrecision(matrix[i][j]);
                    if (j < matrix[i].length - 1) result += ', ';
                }
                result += ']';
                if (i < matrix.length - 1) result += '\n';
            }
            return result;
        }
    </script>
</body>
</html>
