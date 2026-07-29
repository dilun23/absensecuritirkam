<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Absen Satpam Pos Bugel</title>
<style>
    body{font-family:'Arial';background:#f0f2f5;padding:20px}
 .container{max-width:600px;margin:auto;background:white;padding:25px;border-radius:12px;box-shadow:0 4px 10px rgba(0,0,0,0.1)}
    h1{text-align:center;color:#2C3E50}
    label{font-weight:bold;display:block;margin-top:15px}
    input, button{width:100%;padding:12px;margin-top:5px;border:1px solid #ccc;border-radius:8px;font-size:16px}
    input[readonly]{background:#eee}
    button{background:#27AE60;color:white;border:none;cursor:pointer;font-weight:bold}
    button:hover{background:#219150}
    #preview{width:100%;margin-top:10px;border-radius:8px;max-height:200px}
 .success{display:none;background:#D4EDDA;color:#155724;padding:10px;border-radius:8px;margin-top:15px}
    table{width:100%;border-collapse:collapse;margin-top:20px;font-size:14px}
    th,td{border:1px solid #ddd;padding:8px;text-align:center}
    th{background:#2C3E50;color:white}
    @media print{button,.no-print{display:none}}
</style>
</head>
<body>

<div class="container">
    <h1>ABSEN SATPAM POS BUGEL</h1>

    <form id="formAbsen">
        <label>Nama Satpam</label>
        <input type="text" id="nama" required>

        <label>Shift / Keterangan</label>
        <input type="text" id="shift" placeholder="Contoh: Shift 1, Jaga Gerbang, Lembur" required>

        <label>Waktu Absen [Otomatis]</label>
        <input type="text" id="waktu" readonly>

        <label>Foto Selfie + Keadaan Pos</label>
        <input type="file" id="foto" accept="image/*" capture="camera" required>
        <img id="preview" src="">

        <label>Catatan</label>
        <input type="text" id="catatan" placeholder="Opsional">

        <button type="submit">KIRIM ABSEN</button>
    </form>

    <div class="success" id="notif">Absen Berhasil Dikirim! ✅</div>

    <hr class="no-print">
    <button class="no-print" onclick="tampilkanData()">Lihat Data + Print Akhir Bulan</button>
    <button class="no-print" onclick="exportCSV()">Export ke Excel</button>
    <button class="no-print" onclick="window.print()">Print Halaman Ini</button>

    <div id="tabelData"></div>
</div>

<script>
    let dataAbsen = JSON.parse(localStorage.getItem('dataAbsen')) || [];

    // Isi Waktu Otomatis pas buka
    function isiWaktu(){
        const sekarang = new Date();
        document.getElementById('waktu').value = sekarang.toLocaleString('id-ID');
    }
    isiWaktu();

    // Preview Foto
    document.getElementById('foto').onchange = function(e){
        const reader = new FileReader();
        reader.onload = function(){ document.getElementById('preview').src = reader.result }
        reader.readAsDataURL(e.target.files[0]);
    }

    // Submit Form
    document.getElementById('formAbsen').onsubmit = function(e){
        e.preventDefault();
        const nama = document.getElementById('nama').value;
        const shift = document.getElementById('shift').value;
        const waktu = document.getElementById('waktu').value;
        const catatan = document.getElementById('catatan').value;
        const foto = document.getElementById('preview').src;

        dataAbsen.push({nama, shift, waktu, catatan, foto});
        localStorage.setItem('dataAbsen', JSON.stringify(dataAbsen));

        document.getElementById('notif').style.display = 'block';
        document.getElementById('formAbsen').reset();
        document.getElementById('preview').src = '';
        isiWaktu(); // Reset jam lagi
        setTimeout(()=>{document.getElementById('notif').style.display = 'none'}, 3000);
    }

    // Tampilkan Tabel
    function tampilkanData(){
        let html = '<h2>Data Absensi</h2><table><tr><th>No</th><th>Nama</th><th>Shift/Ket</th><th>Waktu</th><th>Foto</th><th>Catatan</th></tr>';
        dataAbsen.forEach((d,i)=>{
            html += `<tr>
                <td>${i+1}</td>
                <td>${d.nama}</td>
                <td>${d.shift}</td>
                <td>${d.waktu}</td>
                <td><img src="${d.foto}" width="80"></td>
                <td>${d.catatan}</td>
            </tr>`
        });
        html += '</table>';
        document.getElementById('tabelData').innerHTML = html;
    }

    // Export ke Excel/CSV
    function exportCSV(){
        let csv = 'No,Nama,Shift,Waktu,Catatan\n';
        dataAbsen.forEach((d,i)=>{
            csv += `${i+1},${d.nama},${d.shift},${d.waktu},${d.catatan}\n`
        });
        const blob = new Blob([csv], {type: 'text/csv'});
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url; a.download = 'Data_Absen_Satpam.csv';
        a.click();
    }
</script>

</body>
</html>
