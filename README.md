<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Generator Twin & Non Twin</title>

<style>
    body {
        font-family: Arial, sans-serif;
        background: #f5f5f5;
        padding: 20px;
        text-align: center;
    }

    h2 {
        margin-bottom: 20px;
    }

    input, select {
        width: 180px;
        padding: 10px;
        font-size: 16px;
        text-align: center;
        border: 1px solid #ccc;
        border-radius: 8px;
    }

    button {
        margin-top: 10px;
        padding: 10px 20px;
        font-size: 15px;
        border: none;
        border-radius: 8px;
        background: #222;
        color: white;
        cursor: pointer;
    }

    button:hover {
        background: #444;
    }

    .copy-btn {
        background: #0a7;
    }

    .copy-btn:hover {
        background: #096;
    }

    .result-container {
        margin-top: 30px;
        display: flex;
        gap: 20px;
        justify-content: center;
        flex-wrap: wrap;
    }

    .box {
        width: 45%;
        min-width: 320px;
        background: white;
        padding: 15px;
        border-radius: 12px;
        box-shadow: 0 0 10px rgba(0,0,0,0.08);
    }

    textarea {
        width: 100%;
        height: 220px;
        resize: none;
        border: 1px solid #ddd;
        border-radius: 8px;
        padding: 10px;
        font-size: 14px;
        box-sizing: border-box;
    }

    h3 {
        margin-bottom: 10px;
    }

    .split-section {
        margin-top: 40px;
    }

    .split-input-box {
        margin-top: 20px;
        background: white;
        padding: 15px;
        border-radius: 12px;
        box-shadow: 0 0 10px rgba(0,0,0,0.08);
        max-width: 900px;
        margin-left: auto;
        margin-right: auto;
    }

    .split-box {
        background: white;
        margin-top: 20px;
        padding: 15px;
        border-radius: 12px;
        box-shadow: 0 0 10px rgba(0,0,0,0.08);
        text-align: left;
    }

    .split-box textarea {
        height: 120px;
    }
</style>
</head>
<body>

<h2>Generator Twin & Non Twin</h2>

<input
    type="text"
    id="inputNumber"
    maxlength="4"
    placeholder="Masukkan 4 digit"
/>

<br>

<button type="button" onclick="generate()">Generate</button>

<div class="result-container">

    <div class="box">
        <h3>Twin</h3>
        <textarea id="twinResult" readonly></textarea>

        <button
            type="button"
            class="copy-btn"
            id="copyTwinBtn">
            Copy Twin
        </button>
    </div>

    <div class="box">
        <h3>Non Twin</h3>
        <textarea id="nonTwinResult" readonly></textarea>

        <button
            type="button"
            class="copy-btn"
            id="copyNonTwinBtn">
            Copy Non Twin
        </button>
    </div>

</div>

<div class="split-section">

    <h2>Pembagian Hasil Generate</h2>

    <div class="split-input-box">
        <h3>Masukkan Hasil yang Ingin Dibagi</h3>

        <textarea
            id="splitInput"
            placeholder="Paste hasil Twin atau Non Twin di sini (format koma)">
        </textarea>
    </div>

    <br>

    <select id="splitCount">
        <option value="">Pilih Pembagi</option>
        <option value="2">Bagi 2</option>
        <option value="3">Bagi 3</option>
        <option value="4">Bagi 4</option>
        <option value="5">Bagi 5</option>
        <option value="6">Bagi 6</option>
        <option value="7">Bagi 7</option>
        <option value="8">Bagi 8</option>
        <option value="9">Bagi 9</option>
        <option value="10">Bagi 10</option>
    </select>

    <br>

    <button type="button" onclick="splitResults()">Bagi Hasil</button>

    <div id="splitResultsContainer"></div>

</div>

<script>
/*
ATURAN:

TWIN:
- 1 pasang angka sama → 1223
- 2 pasang angka sama → 1122
- 3 angka sama tidak berurutan → 3303, 8988, 6676

DIBUANG:
- 3 angka sama berurutan → 3331, 7888, 9997
- 4 angka sama → 1111

NON TWIN:
- semua angka berbeda → 1234

FIX:
- semua tombol copy memakai addEventListener()
- bukan onclick di innerHTML
- semua tombol copy bagian 1–10 pasti berfungsi
*/


function isTwin(numStr) {
    let count = {};

    for (let digit of numStr) {
        count[digit] = (count[digit] || 0) + 1;
    }

    let values = Object.values(count);

    return values.some(v => v >= 2);
}


function isNonTwin(numStr) {
    return new Set(numStr).size === 4;
}


function hasTripleOrQuadConsecutive(numStr) {
    return (
        (numStr[0] === numStr[1] && numStr[1] === numStr[2]) ||
        (numStr[1] === numStr[2] && numStr[2] === numStr[3])
    );
}


function generate() {
    const input = document.getElementById("inputNumber").value.trim();

    if (!/^\d{4}$/.test(input)) {
        alert("Masukkan tepat 4 digit angka");
        return;
    }

    const forbidden = input.split("");

    let twin = [];
    let nonTwin = [];

    for (let i = 0; i <= 9999; i++) {
        let num = i.toString().padStart(4, "0");

        // buang jika posisi digit sama
        if (
            num[0] === forbidden[0] ||
            num[1] === forbidden[1] ||
            num[2] === forbidden[2] ||
            num[3] === forbidden[3]
        ) {
            continue;
        }

        // buang jika triple/quad berurutan
        if (hasTripleOrQuadConsecutive(num)) {
            continue;
        }

        if (isTwin(num)) {
            twin.push(num);
        } else if (isNonTwin(num)) {
            nonTwin.push(num);
        }
    }

    document.getElementById("twinResult").value = twin.join(", ");
    document.getElementById("nonTwinResult").value = nonTwin.join(", ");
}


function splitResults() {
    const rawInput = document.getElementById("splitInput").value.trim();

    if (!rawInput) {
        alert("Masukkan hasil yang ingin dibagi terlebih dahulu");
        return;
    }

    const splitCount = parseInt(
        document.getElementById("splitCount").value
    );

    if (!splitCount) {
        alert("Pilih jumlah pembagi");
        return;
    }

    const numbers = rawInput
        .split(",")
        .map(item => item.trim())
        .filter(Boolean);

    const container = document.getElementById("splitResultsContainer");
    container.innerHTML = "";

    let groups = Array.from(
        { length: splitCount },
        () => []
    );

    numbers.forEach((num, index) => {
        groups[index % splitCount].push(num);
    });

    groups.forEach((group, index) => {
        const finalText = group.join("*");

        const box = document.createElement("div");
        box.className = "split-box";

        const title = document.createElement("h3");
        title.textContent = `Bagian ${index + 1}`;

        const textarea = document.createElement("textarea");
        textarea.id = `resultBox${index}`;
        textarea.readOnly = true;
        textarea.value = finalText;

        const button = document.createElement("button");
        button.type = "button";
        button.className = "copy-btn";
        button.textContent = "Copy ke Clipboard";

        // FIX UTAMA:
        // pakai addEventListener agar semua tombol aktif
        button.addEventListener("click", function () {
            copyFromElement(`resultBox${index}`);
        });

        box.appendChild(title);
        box.appendChild(textarea);
        box.appendChild(document.createElement("br"));
        box.appendChild(button);

        container.appendChild(box);
    });
}


function copyFromElement(id) {
    const el = document.getElementById(id);

    if (!el) {
        alert("Data tidak ditemukan");
        return;
    }

    copyText(el.value);
}


function copyText(text) {
    if (!text) {
        alert("Tidak ada data untuk disalin");
        return;
    }

    if (navigator.clipboard && window.isSecureContext) {
        navigator.clipboard.writeText(text)
            .then(function () {
                alert("Berhasil disalin");
            })
            .catch(function () {
                fallbackCopy(text);
            });
    } else {
        fallbackCopy(text);
    }
}


function fallbackCopy(text) {
    const temp = document.createElement("textarea");

    temp.value = text;
    temp.style.position = "fixed";
    temp.style.left = "-9999px";
    temp.style.top = "0";

    document.body.appendChild(temp);

    temp.focus();
    temp.select();
    temp.setSelectionRange(0, 999999);

    try {
        document.execCommand("copy");
        alert("Berhasil disalin");
    } catch (e) {
        alert("Gagal menyalin");
    }

    document.body.removeChild(temp);
}


/* tombol copy utama */
document.getElementById("copyTwinBtn")
    .addEventListener("click", function () {
        copyFromElement("twinResult");
    });

document.getElementById("copyNonTwinBtn")
    .addEventListener("click", function () {
        copyFromElement("nonTwinResult");
    });
</script>

</body>
</html>
