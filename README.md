# Website-kalkulator
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Aplikasi Parkir</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      background: #f0f0f0;
    }
    h2 {
      color: #333;
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 15px;
    }
    table, th, td {
      border: 1px solid #aaa;
    }
    th, td {
      padding: 8px;
      text-align: center;
    }
    input, select, button {
      padding: 5px;
      margin: 5px 0;
    }
    .form-group {
      margin-bottom: 10px;
    }
  </style>
</head>
<body>
  <h2>Aplikasi Parkir Offline</h2>
  <div class="form-group">
    <label>Jenis Kendaraan:</label>
    <select id="jenis">
      <option value="mobil">Mobil</option>
      <option value="motor">Motor</option>
    </select>
  </div>
  <div class="form-group">
    <label>Jam Masuk:</label>
    <input type="time" id="jamMasuk">
  </div>
  <button onclick="hitung()">Hitung & Simpan</button>

  <table>
    <thead>
      <tr>
        <th>Jenis</th>
        <th>Jam Masuk</th>
        <th>Jam Keluar</th>
        <th>Biaya</th>
        <th>Aksi</th>
      </tr>
    </thead>
    <tbody id="dataParkir"></tbody>
  </table>

  <script>
    function loadData() {
      const data = JSON.parse(localStorage.getItem('parkir')) || [];
      const tbody = document.getElementById('dataParkir');
      tbody.innerHTML = '';
      data.forEach((item, index) => {
        tbody.innerHTML += `
          <tr>
            <td>${item.jenis}</td>
            <td>${item.jamMasuk}</td>
            <td>${item.jamKeluar}</td>
            <td>Rp ${item.biaya}</td>
            <td>
              <button onclick="edit(${index})">Edit</button>
              <button onclick="hapus(${index})">Hapus</button>
            </td>
          </tr>`;
      });
    }

    function hitung() {
      const jenis = document.getElementById('jenis').value;
      const jamMasuk = document.getElementById('jamMasuk').value;
      if (!jamMasuk) return alert("Jam masuk harus diisi!");

      const now = new Date();
      const jamKeluar = now.getHours().toString().padStart(2, '0') + ':' + now.getMinutes().toString().padStart(2, '0');
      const [jamM, menitM] = jamMasuk.split(':').map(Number);
      const [jamK, menitK] = jamKeluar.split(':').map(Number);

      let durasi = (jamK + menitK/60) - (jamM + menitM/60);
      if (durasi < 0) durasi += 24;

      let biaya = 0;
      if (jenis === 'mobil') {
        biaya = 3000 + Math.max(0, Math.ceil(durasi) - 1) * 2000;
        biaya = Math.min(biaya, 15000);
      } else {
        biaya = 2000 + Math.max(0, Math.ceil(durasi) - 1) * 1000;
        biaya = Math.min(biaya, 10000);
      }

      const data = JSON.parse(localStorage.getItem('parkir')) || [];
      data.push({ jenis, jamMasuk, jamKeluar, biaya });
      localStorage.setItem('parkir', JSON.stringify(data));
      loadData();
    }

    function hapus(index) {
      const data = JSON.parse(localStorage.getItem('parkir')) || [];
      if (confirm("Yakin ingin menghapus data ini?")) {
        data.splice(index, 1);
        localStorage.setItem('parkir', JSON.stringify(data));
        loadData();
      }
    }

    function edit(index) {
      const data = JSON.parse(localStorage.getItem('parkir'));
      const item = data[index];
      document.getElementById('jenis').value = item.jenis;
      document.getElementById('jamMasuk').value = item.jamMasuk;
      data.splice(index, 1);
      localStorage.setItem('parkir', JSON.stringify(data));
      loadData();
    }

    loadData();
  </script>
</body>
</html>
