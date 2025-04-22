- 👋 Hi, I’m @radalqrcodeio
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...

<!---
radalqrcodeio/radalqrcodeio is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QR Kod Oluşturucu & PDF Kaydet</title>
  <script src="https://cdn.jsdelivr.net/npm/qrcode/build/qrcode.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.3/xlsx.full.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Poppins', sans-serif; }
    body, html { height: 100%; background: linear-gradient(135deg, #74ebd5 0%, #acb6e5 100%); display: flex; justify-content: center; align-items: center; }
    .container { background: #fff; padding: 30px; border-radius: 15px; box-shadow: 0 8px 16px rgba(0,0,0,0.25); width: 95%; max-width: 500px; text-align: center; }
    h1 { margin-bottom: 20px; font-size: 26px; color: #333; }
    input, button, select { width: 100%; padding: 12px; margin: 10px 0; border: 1px solid #ccc; border-radius: 8px; font-size: 16px; }
    button { background: #007bff; color: white; border: none; transition: background 0.3s; cursor: pointer; }
    button:hover { background: #0056b3; }
    #qrResult { margin-top: 20px; }
  </style>
</head>
<body>

<div class="container">
  <h1>QR Kod & PDF Üret</h1>

  <input type="text" id="ad" placeholder="İsim" required>
  <input type="text" id="soyad" placeholder="Soyisim" required>
  <input type="text" id="sinif" placeholder="Sınıf" required>
  <input type="text" id="numara" placeholder="Numara" required>

  <label>Onay Durumu:</label>
  <select id="onay">
    <option value="Onaylandı">Onaylandı</option>
    <option value="Onaylanmadı">Onaylanmadı</option>
  </select>

  <label>QR Rengi:</label>
  <input type="color" id="qrColor" value="#000000">

  <label>QR Arkaplan Rengi:</label>
  <input type="color" id="bgColor" value="#ffffff">

  <button onclick="generateQRCode()">QR Kodu Oluştur</button>
  <button onclick="saveToExcel()">Excel'e Kaydet</button>
  <button onclick="downloadExcel()">Excel İndir</button>
  <button onclick="downloadPDF()">PDF İndir</button>

  <div id="qrResult">
    <canvas id="qrCanvas"></canvas>
  </div>
</div>

<script>
const { jsPDF } = window.jspdf;

let excelData = [["İsim", "Soyisim", "Sınıf", "Numara", "Onay Durumu"]];

function generateQRCode() {
  const ad = document.getElementById('ad').value.trim();
  const soyad = document.getElementById('soyad').value.trim();
  const sinif = document.getElementById('sinif').value.trim();
  const numara = document.getElementById('numara').value.trim();
  const onay = document.getElementById('onay').value;
  const qrColor = document.getElementById('qrColor').value;
  const bgColor = document.getElementById('bgColor').value;

  if (!ad || !soyad || !sinif || !numara) {
    alert("Lütfen tüm alanları doldurun!");
    return;
  }

  const bilgi = `${ad}|${soyad}|${sinif}|${numara}|${onay}`;
  const canvas = document.getElementById('qrCanvas');

  QRCode.toCanvas(canvas, bilgi, {
    color: {
      dark: qrColor,
      light: bgColor
    }
  }, function (error) {
    if (error) {
      console.error(error);
      alert("QR kodu oluşturulurken hata oluştu.");
    } else {
      addNameToQRCode(canvas, ad + ' ' + soyad);
    }
  });
}

function addNameToQRCode(canvas, text) {
  const ctx = canvas.getContext('2d');
  ctx.font = "bold 14px Poppins";
  ctx.fillStyle = "#000";
  ctx.textAlign = "center";
  ctx.fillText(text, canvas.width / 2, canvas.height - 10);
}

function saveToExcel() {
  const ad = document.getElementById('ad').value.trim();
  const soyad = document.getElementById('soyad').value.trim();
  const sinif = document.getElementById('sinif').value.trim();
  const numara = document.getElementById('numara').value.trim();
  const onay = document.getElementById('onay').value;

  if (!ad || !soyad || !sinif || !numara) {
    alert("Lütfen tüm alanları doldurun!");
    return;
  }

  excelData.push([ad, soyad, sinif, numara, onay]);
  alert("Excel verisine kaydedildi!");
}

function downloadExcel() {
  const wb = XLSX.utils.book_new();
  const ws = XLSX.utils.aoa_to_sheet(excelData);
  XLSX.utils.book_append_sheet(wb, ws, "QR_Kayitlar");

  const wbout = XLSX.write(wb, {bookType:'xlsx', type:'array'});
  saveAs(new Blob([wbout], {type:"application/octet-stream"}), "QR_Kayitlar.xlsx");
}

function downloadPDF() {
  const ad = document.getElementById('ad').value.trim();
  const soyad = document.getElementById('soyad').value.trim();
  const sinif = document.getElementById('sinif').value.trim();
  const numara = document.getElementById('numara').value.trim();
  const onay = document.getElementById('onay').value;
  const canvas = document.getElementById('qrCanvas');

  if (!ad || !soyad || !sinif || !numara) {
    alert("Lütfen tüm alanları doldurun!");
    return;
  }

  const pdf = new jsPDF();
  pdf.setFont("Poppins", "bold");
  pdf.setFontSize(20);
  pdf.text("\u00d6\u011frenci Bilgileri", 105, 20, null, null, "center");

  pdf.setFontSize(12);
  pdf.text(`\u0130sim: ${ad}`, 20, 40);
  pdf.text(`Soyisim: ${soyad}`, 20, 50);
  pdf.text(`S\u0131n\u0131f: ${sinif}`, 20, 60);
  pdf.text(`Numara: ${numara}`, 20, 70);
  pdf.text(`Onay Durumu: ${onay}`, 20, 80);

  const imgData = canvas.toDataURL("image/png");
  pdf.addImage(imgData, 'PNG', 60, 90, 90, 90);

  pdf.save("QR_Bilgi.pdf");
}
</script>

</body>
</html>
**<!DOCTYPE html>
<html lang="tr">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QR Kod Oluşturucu & PDF Kaydet</title>
  <script src="https://cdn.jsdelivr.net/npm/qrcode/build/qrcode.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.3/xlsx.full.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Poppins', sans-serif; }
    body, html { height: 100%; background: linear-gradient(135deg, #74ebd5 0%, #acb6e5 100%); display: flex; justify-content: center; align-items: center; }
    .container { background: #fff; padding: 30px; border-radius: 15px; box-shadow: 0 8px 16px rgba(0,0,0,0.25); width: 95%; max-width: 500px; text-align: center; }
    h1 { margin-bottom: 20px; font-size: 26px; color: #333; }
    input, button, select { width: 100%; padding: 12px; margin: 10px 0; border: 1px solid #ccc; border-radius: 8px; font-size: 16px; }
    button { background: #007bff; color: white; border: none; transition: background 0.3s; cursor: pointer; }
    button:hover { background: #0056b3; }
    #qrResult { margin-top: 20px; }
  </style>
</head>
<body>

<div class="container">
  <h1>QR Kod & PDF Üret</h1>

  <input type="text" id="ad" placeholder="İsim" required>
  <input type="text" id="soyad" placeholder="Soyisim" required>
  <input type="text" id="sinif" placeholder="Sınıf" required>
  <input type="text" id="numara" placeholder="Numara" required>

  <label>Onay Durumu:</label>
  <select id="onay">
    <option value="Onaylandı">Onaylandı</option>
    <option value="Onaylanmadı">Onaylanmadı</option>
  </select>

  <label>QR Rengi:</label>
  <input type="color" id="qrColor" value="#000000">

  <label>QR Arkaplan Rengi:</label>
  <input type="color" id="bgColor" value="#ffffff">

  <button onclick="generateQRCode()">QR Kodu Oluştur</button>
  <button onclick="saveToExcel()">Excel'e Kaydet</button>
  <button onclick="downloadExcel()">Excel İndir</button>
  <button onclick="downloadPDF()">PDF İndir</button>

  <div id="qrResult">
    <canvas id="qrCanvas"></canvas>
  </div>
</div>

<script>
const { jsPDF } = window.jspdf;

let excelData = [["İsim", "Soyisim", "Sınıf", "Numara", "Onay Durumu"]];

function generateQRCode() {
  const ad = document.getElementById('ad').value.trim();
  const soyad = document.getElementById('soyad').value.trim();
  const sinif = document.getElementById('sinif').value.trim();
  const numara = document.getElementById('numara').value.trim();
  const onay = document.getElementById('onay').value;
  const qrColor = document.getElementById('qrColor').value;
  const bgColor = document.getElementById('bgColor').value;

  if (!ad || !soyad || !sinif || !numara) {
    alert("Lütfen tüm alanları doldurun!");
    return;
  }

  const bilgi = `${ad}|${soyad}|${sinif}|${numara}|${onay}`;
  const canvas = document.getElementById('qrCanvas');

  QRCode.toCanvas(canvas, bilgi, {
    color: {
      dark: qrColor,
      light: bgColor
    }
  }, function (error) {
    if (error) {
      console.error(error);
      alert("QR kodu oluşturulurken hata oluştu.");
    } else {
      addNameToQRCode(canvas, ad + ' ' + soyad);
    }
  });
}

function addNameToQRCode(canvas, text) {
  const ctx = canvas.getContext('2d');
  ctx.font = "bold 14px Poppins";
  ctx.fillStyle = "#000";
  ctx.textAlign = "center";
  ctx.fillText(text, canvas.width / 2, canvas.height - 10);
}

function saveToExcel() {
  const ad = document.getElementById('ad').value.trim();
  const soyad = document.getElementById('soyad').value.trim();
  const sinif = document.getElementById('sinif').value.trim();
  const numara = document.getElementById('numara').value.trim();
  const onay = document.getElementById('onay').value;

  if (!ad || !soyad || !sinif || !numara) {
    alert("Lütfen tüm alanları doldurun!");
    return;
  }

  excelData.push([ad, soyad, sinif, numara, onay]);
  alert("Excel verisine kaydedildi!");
}

function downloadExcel() {
  const wb = XLSX.utils.book_new();
  const ws = XLSX.utils.aoa_to_sheet(excelData);
  XLSX.utils.book_append_sheet(wb, ws, "QR_Kayitlar");

  const wbout = XLSX.write(wb, {bookType:'xlsx', type:'array'});
  saveAs(new Blob([wbout], {type:"application/octet-stream"}), "QR_Kayitlar.xlsx");
}

function downloadPDF() {
  const ad = document.getElementById('ad').value.trim();
  const soyad = document.getElementById('soyad').value.trim();
  const sinif = document.getElementById('sinif').value.trim();
  const numara = document.getElementById('numara').value.trim();
  const onay = document.getElementById('onay').value;
  const canvas = document.getElementById('qrCanvas');

  if (!ad || !soyad || !sinif || !numara) {
    alert("Lütfen tüm alanları doldurun!");
    return;
  }

  const pdf = new jsPDF();
  pdf.setFont("Poppins", "bold");
  pdf.setFontSize(20);
  pdf.text("\u00d6\u011frenci Bilgileri", 105, 20, null, null, "center");

  pdf.setFontSize(12);
  pdf.text(`\u0130sim: ${ad}`, 20, 40);
  pdf.text(`Soyisim: ${soyad}`, 20, 50);
  pdf.text(`S\u0131n\u0131f: ${sinif}`, 20, 60);
  pdf.text(`Numara: ${numara}`, 20, 70);
  pdf.text(`Onay Durumu: ${onay}`, 20, 80);

  const imgData = canvas.toDataURL("image/png");
  pdf.addImage(imgData, 'PNG', 60, 90, 90, 90);

  pdf.save("QR_Bilgi.pdf");
}
</script>

</body>
</html>

**
