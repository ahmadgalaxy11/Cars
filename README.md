<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حاسبة صيدات السيارات - مستعمل</title>
    <style>
        :root {
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --accent: #38bdf8;
            --text: #f8fafc;
            --fair: #3b82f6;
            --deal: #22c55e;
            --over: #ef4444;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text);
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
        }
        .container {
            width: 100%;
            max-width: 600px;
            background: var(--card-bg);
            padding: 25px;
            border-radius: 16px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.5);
        }
        h2 { text-align: center; color: var(--accent); margin-bottom: 20px; }
        .form-group { margin-bottom: 15px; }
        label { display: block; margin-bottom: 5px; font-weight: bold; }
        select, input {
            width: 100%;
            padding: 10px;
            border-radius: 8px;
            border: 1px solid #334155;
            background-color: #0f172a;
            color: var(--text);
            font-size: 16px;
            box-sizing: border-box;
        }
        button {
            width: 100%;
            padding: 12px;
            background-color: var(--accent);
            color: #0f172a;
            font-weight: bold;
            font-size: 18px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            margin-top: 10px;
            transition: 0.2s;
        }
        button:hover { opacity: 0.9; }
        .results {
            margin-top: 25px;
            display: none;
            grid-template-columns: repeat(3, 1fr);
            gap: 10px;
            text-align: center;
        }
        .card {
            padding: 15px 5px;
            border-radius: 10px;
            font-weight: bold;
        }
        .card-deal { background-color: rgba(34, 197, 94, 0.2); border: 2px solid var(--deal); color: var(--deal); }
        .card-fair { background-color: rgba(59, 130, 246, 0.2); border: 2px solid var(--fair); color: var(--fair); }
        .card-over { background-color: rgba(239, 68, 68, 0.2); border: 2px solid var(--over); color: var(--over); }
        .price-val { font-size: 18px; margin-top: 8px; }
    </style>
</head>
<body>

<div class="container">
    <h2>🚘 حاسبة تقييم أسعار السيارات</h2>

    <div class="form-group">
        <label>اختر السيارة:</label>
        <select id="carModel">
            <option value="corolla">تويوتا كورولا</option>
            <option value="yaris">تويوتا يارس</option>
            <option value="elantra">هيونداي النترا</option>
            <option value="accent">هيونداي اكسنت</option>
        </select>
    </div>

    <div class="form-group">
        <label>الموديل (سنة الصنع):</label>
        <input type="number" id="carYear" min="2015" max="2026" value="2022">
    </div>

    <div class="form-group">
        <label>الممشى (بالكيلومتر):</label>
        <input type="number" id="mileage" placeholder="مثال: 80000" value="50000">
    </div>

    <div class="form-group">
        <label>حالة البودي:</label>
        <select id="bodyStatus">
            <option value="clean">بودي بلد 100% (بدون أي رش)</option>
            <option value="cold">تعديل بارد بدون رش</option>
            <option value="part_repaint">رش جزئي (رفرف أو باب واحد)</option>
            <option value="full_repaint">رش حزام / تجميلي كامل</option>
            <option value="accident">رش مع حوادث وسماكر</option>
        </select>
    </div>

    <button onclick="calculatePrice()">احسب السعر المتوقع</button>

    <div class="results" id="resultsBox">
        <div class="card card-deal">
            <div>السعر الصيدة 🎯</div>
            <div class="price-val" id="dealPrice">0</div>
        </div>
        <div class="card card-fair">
            <div>السعر العادل ⚖️</div>
            <div class="price-val" id="fairPrice">0</div>
        </div>
        <div class="card card-over">
            <div>مبالغ فيه ⚠️</div>
            <div class="price-val" id="overPrice">0</div>
        </div>
    </div>
</div>

<script>
function calculatePrice() {
    const model = document.getElementById('carModel').value;
    const year = parseInt(document.getElementById('carYear').value);
    const mileage = parseInt(document.getElementById('mileage').value);
    const body = document.getElementById('bodyStatus').value;

    // 1. القيمة الأساسية لسيارة جديدة موديل 2024
    const basePrices = {
        'corolla': 75000,
        'elantra': 72000,
        'yaris': 58000,
        'accent': 55000
    };

    let price = basePrices[model];

    // 2. خصم سنة الموديل (تراجع قيمة ~ 7% سنوياً)
    const currentYear = 2026;
    const age = currentYear - year;
    price = price * Math.pow(0.93, age);

    // 3. خصم الممشى (المعدل الطبيعي 20 ألف كم سنوياً)
    const expectedMileage = age * 20000;
    const extraMileage = mileage - expectedMileage;
    if (extraMileage > 0) {
        // خصم لكل 10,000 كم زيادة
        const mileageDeduction = (extraMileage / 10000) * 1500;
        price -= mileageDeduction;
    }

    // 4. معامل حالة البودي
    const bodyMultipliers = {
        'clean': 1.0,
        'cold': 0.96,
        'part_repaint': 0.91,
        'full_repaint': 0.83,
        'accident': 0.72
    };
    price = price * bodyMultipliers[body];

    // حماية السعر الأقل
    if (price < 15000) price = 15000;

    // حساب النطاقات
    const fair = Math.round(price / 500) * 500;
    const deal = Math.round((fair * 0.88) / 500) * 500;
    const over = Math.round((fair * 1.15) / 500) * 500;

    // عرض النتائج
    document.getElementById('dealPrice').innerText = deal.toLocaleString() + ' ريال';
    document.getElementById('fairPrice').innerText = fair.toLocaleString() + ' ريال';
    document.getElementById('overPrice').innerText = over.toLocaleString() + ' ريال+';

    document.getElementById('resultsBox').style.display = 'grid';
}
</script>

</body>
</html>
