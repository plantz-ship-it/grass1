<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <title>匯率轉換器</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background: #eef2f5;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .converter {
            background: white;
            padding: 25px 30px;
            border-radius: 10px;
            width: 360px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.15);
        }
        h2 {
            text-align: center;
        }
        label {
            margin-top: 10px;
            display: block;
        }
        input, select, button {
            width: 100%;
            padding: 8px;
            margin-top: 5px;
            font-size: 14px;
        }
        button {
            margin-top: 15px;
            background: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background: #0056b3;
        }
        .result {
            margin-top: 15px;
            text-align: center;
            font-weight: bold;
            line-height: 1.6;
        }
    </style>
</head>
<body>

<div class="converter">
    <h2>匯率轉換器</h2>

    <label>金額</label>
    <input type="number" id="amount" value="1" min="0" step="any">

    <label>來源貨幣</label>
    <select id="fromCurrency"></select>

    <label>目標貨幣</label>
    <select id="toCurrency"></select>

    <button onclick="convert()">轉換</button>

    <div class="result" id="result"></div>
</div>

<script>
const currencyNames = {
    TWD: "台幣",
    USD: "美元",
    EUR: "歐元",
    JPY: "日圓",
    CNY: "人民幣",
    HKD: "港幣",
    KRW: "韓元",
    SGD: "新加坡幣",
    THB: "泰銖",
    GBP: "英鎊",
    AUD: "澳幣",
    CAD: "加幣",
    CHF: "瑞士法郎",
    INR: "印度盧比",
    IDR: "印尼盾",
    PHP: "菲律賓披索",
    VND: "越南盾",
    NZD: "紐西蘭幣",
    ZAR: "南非幣"
};

const fromSelect = document.getElementById("fromCurrency");
const toSelect = document.getElementById("toCurrency");

for (let code in currencyNames) {
    fromSelect.add(new Option(`${currencyNames[code]} (${code})`, code));
    toSelect.add(new Option(`${currencyNames[code]} (${code})`, code));
}

fromSelect.value = "TWD";
toSelect.value = "USD";

async function convert() {
    const amount = parseFloat(document.getElementById("amount").value);
    const from = fromSelect.value;
    const to = toSelect.value;
    const resultDiv = document.getElementById("result");

    if (!amount || amount <= 0) {
        resultDiv.innerText = "請輸入有效金額";
        return;
    }

    resultDiv.innerText = "轉換中...";

    try {
        // 先以 USD 為基準抓全部匯率
        const response = await fetch("https://open.er-api.com/v6/latest/USD");
        const data = await response.json();

        if (data.result !== "success") {
            throw new Error("API 失敗");
        }

        const rates = data.rates;

        // 交叉換算
        const rate = rates[to] / rates[from];
        const converted = (amount * rate).toFixed(4);

        resultDiv.innerHTML = `
            ${amount} ${from} <br>
            = ${converted} ${to} <br>
            <small>匯率：1 ${from} = ${rate.toFixed(4)} ${to}</small>
        `;
    } catch (error) {
        resultDiv.innerText = "匯率取得失敗";
    }
}
</script>

</body>
</html>
