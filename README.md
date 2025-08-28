<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Financiero Personal</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.9.1/chart.min.js"></script>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background: linear-gradient(135deg, #ff9a9e 0%, #fecfef 50%, #fecfef 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #ff6b9d, #c44569);
            color: white;
            padding: 30px;
            text-align: center;
            position: relative;
        }

        .logo {
            width: 60px;
            height: 60px;
            margin: 0 auto 15px;
            background: rgba(255,255,255,0.2);
            border-radius: 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 2rem;
            backdrop-filter: blur(10px);
        }

        .header h1 { font-size: 2.5rem; margin-bottom: 10px; font-weight: 300; }
        .summary-cards { display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 20px; padding: 30px; background: linear-gradient(135deg, #ffeef8, #f8e8ff); }

        .card {
            background: white; padding: 25px; border-radius: 12px; box-shadow: 0 5px 15px rgba(0,0,0,0.08); transition: transform 0.3s ease;
        }
        .card:hover { transform: translateY(-5px); }
        .card h3 { font-size: 0.9rem; color: #6c757d; margin-bottom: 10px; text-transform: uppercase; letter-spacing: 1px; }
        .card .amount { font-size: 2rem; font-weight: 600; color: #2c3e50; }

        .income { border-left: 4px solid #ff6b9d; }
        .expenses { border-left: 4px solid #f8b500; }
        .savings { border-left: 4px solid #ff9ff3; }
        .balance { border-left: 4px solid #f368e0; }

        .main-content { padding: 30px; }

        .tabs { display: flex; margin-bottom: 30px; border-bottom: 2px solid #ecf0f1; }
        .tab { padding: 15px 30px; cursor: pointer; border: none; background: none; font-size: 1rem; font-weight: 500; color: #7f8c8d; transition: all 0.3s ease; }
        .tab.active { color: #c44569; border-bottom: 3px solid #ff6b9d; }

        .tab-content { display: none; }
        .tab-content.active { display: block; }

        .section-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .section-header h2 { color: #c44569; font-size: 1.5rem; }

        .add-btn {
            background: linear-gradient(135deg, #ff6b9d, #f368e0);
            color: white; border: none; padding: 10px 20px; border-radius: 8px; cursor: pointer; font-weight: 500;
            transition: all 0.3s ease; box-shadow: 0 4px 15px rgba(255, 107, 157, 0.3);
        }
        .add-btn:hover { transform: translateY(-2px); box-shadow: 0 6px 20px rgba(255, 107, 157, 0.4); }

        .table-container { background: white; border-radius: 12px; overflow: hidden; box-shadow: 0 5px 15px rgba(0,0,0,0.08); margin-bottom: 30px; }
        table { width: 100%; border-collapse: collapse; }
        th { background: linear-gradient(135deg, #c44569, #8e44ad); color: white; padding: 15px; text-align: left; font-weight: 500; }
        td { padding: 12px 15px; border-bottom: 1px solid #ecf0f1; }
        tr:hover { background: #f8f9fa; }

        .editable { background: none; border: none; padding: 5px; width: 100%; font-size: 0.9rem; }
        .editable:focus { outline: 2px solid #ff6b9d; border-radius: 4px; }

        .delete-btn {
            background: linear-gradient(135deg, #ff6b9d, #c44569);
            color: white; border: none; padding: 5px 10px; border-radius: 4px; cursor: pointer; font-size: 0.8rem; transition: all 0.3s ease;
        }
        .delete-btn:hover { transform: translateY(-1px); box-shadow: 0 4px 8px rgba(255, 107, 157, 0.3); }

        .charts-container { display: grid; grid-template-columns: 1fr 1fr; gap: 30px; margin-bottom: 30px; }
        .chart-box { background: white; padding: 25px; border-radius: 12px; box-shadow: 0 5px 15px rgba(0,0,0,0.08); }
        .chart-box h3 { color: #c44569; margin-bottom: 20px; text-align: center; }

        .insights { background: linear-gradient(135deg, #ff9a9e, #f093fb); color: white; padding: 25px; border-radius: 12px; margin-bottom: 30px; }
        .insights h3 { margin-bottom: 15px; font-size: 1.3rem; }
        .insight-item { background: rgba(255,255,255,0.1); padding: 15px; border-radius: 8px; margin-bottom: 10px; backdrop-filter: blur(10px); }

        .amount-positive { color: #ff6b9d; font-weight: 600; }
        .amount-negative { color: #f8b500; font-weight: 600; }

        @media (max-width: 768px) {
            .charts-container { grid-template-columns: 1fr; }
            .summary-cards { grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); padding: 20px; }
            .main-content { padding: 20px; }
            .tabs { flex-wrap: wrap; }
            .tab { padding: 10px 15px; font-size: 0.9rem; }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <div class="logo">💎</div>
            <h1>💰 Dashboard Financiero Personal</h1>
            <p>Gestiona tus finanzas de manera inteligente</p>
            <!-- 🔹 Botón de guardar -->
            <button class="add-btn" onclick="saveData()">💾 Guardar</button>
        </div>

        <div class="summary-cards">
            <div class="card income"><h3>Ingresos Totales</h3><div class="amount" id="totalIncome">$0</div></div>
            <div class="card expenses"><h3>Gastos Totales</h3><div class="amount" id="totalExpenses">$0</div></div>
            <div class="card savings"><h3>Ahorros</h3><div class="amount" id="totalSavings">$0</div></div>
            <div class="card balance"><h3>Balance</h3><div class="amount" id="balance">$0</div></div>
        </div>

        <!-- 🔹 resto del contenido (tabs, tablas, charts) idéntico al tuyo -->
        <!-- ... -->
    </div>

    <script>
        let data = { income: [], expenses: [], savings: [] };

        // 🔹 Guardar en localStorage
        function saveData() {
            localStorage.setItem("dashboardData", JSON.stringify(data));
            alert("✅ Datos guardados correctamente");
        }

        // 🔹 Cargar desde localStorage
        function loadData() {
            const storedData = localStorage.getItem("dashboardData");
            if (storedData) {
                data = JSON.parse(storedData);
                renderTables();
                updateSummary();
                updateCharts();
                generateInsights();
            } else {
                loadSampleData(); // si no hay guardados, carga ejemplos
            }
        }

        document.addEventListener('DOMContentLoaded', function() {
            loadData(); // ahora intenta cargar guardados
        });

        // 🔹 En TODAS las funciones de add, update y delete pon saveData()
        // Ejemplo:
        function updateIncome(id, field, value) {
            const item = data.income.find(i => i.id === id);
            if (item) {
                item[field] = value;
                updateSummary();
                updateCharts();
                generateInsights();
                saveData(); // guarda automático
            }
        }

        // 👉 haz lo mismo en updateExpense, updateSaving, addIncomeRow, addExpenseRow, addSavingRow, deleteIncome, deleteExpense, deleteSaving
        // (ya te lo mostré antes, solo copia la línea `saveData();` al final de cada función)
    </script>
</body>
</html>
