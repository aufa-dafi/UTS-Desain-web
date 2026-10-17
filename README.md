<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>PasarKita - Belanja dari UMKM Lokal</title>
<style>
  body { font-family: Arial, sans-serif; margin: 0; background: #faf6ef; color: #3a2e27; }
  h1, h2, h3 { font-family: Georgia, serif; }
  header { background: #fff; border-bottom: 2px solid #e6dccb; padding: 12px 24px; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; }
  .logo { font-family: Georgia, serif; font-size: 24px; color: #b5532b; cursor: pointer; }
  nav button { background: none; border: none; font-size: 15px; margin-left: 14px; cursor: pointer; color: #3a2e27; }
  nav button:hover { color: #b5532b; text-decoration: underline; }
  .wrap { max-width: 980px; margin: 0 auto; padding: 24px 16px; }
  .hero { background: #f0e3cf; padding: 32px 24px; border-radius: 6px; margin-bottom: 24px; }
  .hero p { max-width: 520px; line-height: 1.5; }
  .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(210px, 1fr)); gap: 16px; }
  .card { background: #fff; border: 1px solid #e6dccb; border-radius: 6px; overflow: hidden; }
  .card img { width: 100%; height: 150px; object-fit: cover; display: block; background: #e6dccb; }
  .card .isi { padding: 12px; }
  .card h3 { margin: 0 0 4px; font-size: 17px; }
  .kecil { font-size: 13px; color: #7a6a5d; }
  .harga { color: #b5532b; font-weight: bold; margin: 6px 0; }
  button.btn { background: #b5532b; color: #fff; border: none; padding: 8px 14px; border-radius: 4px; cursor: pointer; font-size: 14px; }
  button.btn:hover { background: #944220; }
  button.abu { background: #8a7b6e; }
  button.merah { background: #a33; }
  table { width: 100%; border-collapse: collapse; background: #fff; margin-top: 10px; }
  th, td { border: 1px solid #e6dccb; padding: 8px; text-align: left; font-size: 14px; }
  th { background: #f0e3cf; }
  input, select, textarea { padding: 7px; border: 1px solid #cdbfa9; border-radius: 4px; font-size: 14px; font-family: inherit; }
  .kotak { background: #fff; border: 1px solid #e6dccb; border-radius: 6px; padding: 16px; margin-bottom: 16px; }
  .tab button { margin-right: 6px; margin-bottom: 10px; }
  .bintang { color: #d99a1e; }
  .ulasan { border-bottom: 1px dashed #d9ccb6; padding: 8px 0; font-size: 14px; }
  footer { text-align: center; padding: 24px; font-size: 13px; color: #7a6a5d; }
  .hidden { display: none; }
</style>
</head>
<body>

<header>
  <div class="logo" onclick="tampilHalaman('beranda')">PasarKita</div>
  <nav>
    <button onclick="tampilHalaman('beranda')">Beranda</button>
    <button onclick="tampilHalaman('keranjang')">Keranjang (<span id="jumlahKeranjang">0</span>)</button>
    <button onclick="tampilHalaman('admin')">Admin</button>
  </nav>
</header>

<div class="wrap">
  <div id="beranda">
    <div class="hero">
      <h1>Belanja langsung dari pelaku UMKM</h1>
      <p>Roti, kopi, dan kerajinan dari tetangga sendiri. Setiap toko punya halaman sendiri, jadi kamu tahu siapa yang bikin barangnya.</p>
    </div>
    <h2>Toko UMKM</h2>
    <div class="grid" id="daftarVendor"></div>
    <h2 style="margin-top:28px">Semua Produk</h2>
    <div class="grid" id="daftarProduk"></div>
  </div>

  <div id="toko" class="hidden"></div>

  <div id="keranjang" class="hidden">
    <h2>Keranjang Belanja</h2>
    <div id="isiKeranjang"></div>
  </div>

  <div id="admin" class="hidden">
    <h2>Panel Admin</h2>
    <div class="tab">
      <button class="btn" onclick="tampilAdmin('aVendor')">Vendor</button>
      <button class="btn" onclick="tampilAdmin('aProduk')">Produk</button>
      <button class="btn" onclick="tampilAdmin('aLaporan')">Laporan Keuangan</button>
      <button class="btn" onclick="tampilAdmin('aKomisi')">Komisi</button>
    </div>
    <div id="aVendor"></div>
    <div id="aProduk" class="hidden"></div>
    <div id="aLaporan" class="hidden"></div>
    <div id="aKomisi" class="hidden"></div>
  </div>
</div>

<footer>PasarKita &copy; 2026 - Proyek belajar e-commerce multi vendor</footer>

<script>
var komisiPersen = 10;

var vendors = [
  { id: 1, nama: "Roti Bu Sari", kota: "Surabaya", deskripsi: "Roti dan kue rumahan, dipanggang setiap pagi.", foto: "foto/roti1.jpg", aktif: true },
  { id: 2, nama: "Kopi Pak Joko", kota: "Malang", deskripsi: "Kopi lokal dari petani Jawa Timur, digiling sendiri.", foto: "foto/kopi1.jpg", aktif: true },
  { id: 3, nama: "Anyaman Mbak Ayu", kota: "Sidoarjo", deskripsi: "Tas dan keranjang anyaman buatan tangan.", foto: "foto/anyaman1.jpg", aktif: true }
];

var produk = [
  { id: 1, vendorId: 1, nama: "Roti Tawar Gandum", harga: 22000, foto: "foto/roti1.jpg" },
  { id: 2, vendorId: 1, nama: "Croissant Mentega", harga: 15000, foto: "foto/roti2.jpg" },
  { id: 3, vendorId: 1, nama: "Brownies Panggang", harga: 35000, foto: "foto/roti3.jpg" },
  { id: 4, vendorId: 2, nama: "Kopi Arabika 200gr", harga: 58000, foto: "foto/kopi1.jpg" },
  { id: 5, vendorId: 2, nama: "Kopi Robusta 200gr", harga: 45000, foto: "foto/kopi2.jpg" },
  { id: 6, vendorId: 3, nama: "Tas Anyaman Rotan", harga: 120000, foto: "foto/anyaman1.jpg" },
  { id: 7, vendorId: 3, nama: "Keranjang Serbaguna", harga: 75000, foto: "foto/anyaman2.jpg" }
];

var ulasan = [
  { vendorId: 1, nama: "Rina", bintang: 5, isi: "Rotinya lembut, pengiriman cepat." },
  { vendorId: 2, nama: "Dimas", bintang: 4, isi: "Kopinya wangi, tapi kemasan bisa lebih rapi." }
];

var keranjang = [];   
var pesanan = [];    
var fotoPengganti = "data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='300' height='200'%3E%3Crect width='300' height='200' fill='%23e6dccb'/%3E%3C/svg%3E";

function rupiah(angka) {
  return "Rp " + angka.toLocaleString("id-ID");
}

function cariVendor(id) {
  for (var i = 0; i < vendors.length; i++) {
    if (vendors[i].id == id) return vendors[i];
  }
}

function cariProduk(id) {
  for (var i = 0; i < produk.length; i++) {
    if (produk[i].id == id) return produk[i];
  }
}

function rataRata(vendorId) {
  var total = 0, n = 0;
  for (var i = 0; i < ulasan.length; i++) {
    if (ulasan[i].vendorId == vendorId) { total += ulasan[i].bintang; n++; }
  }
  if (n == 0) return "Belum ada ulasan";
  return (total / n).toFixed(1) + " / 5 (" + n + " ulasan)";
}

function kartuProduk(p) {
  var v = cariVendor(p.vendorId);
  return '<div class="card"><img src="' + p.foto + '" onerror="this.src=fotoPengganti">' +
    '<div class="isi"><h3>' + p.nama + '</h3>' +
    '<div class="kecil">oleh <a href="#" onclick="bukaToko(' + v.id + '); return false;">' + v.nama + '</a></div>' +
    '<div class="harga">' + rupiah(p.harga) + '</div>' +
    '<button class="btn" onclick="tambahKeranjang(' + p.id + ')">+ Keranjang</button></div></div>';
}

function tampilHalaman(nama) {
  var halaman = ["beranda", "toko", "keranjang", "admin"];
  for (var i = 0; i < halaman.length; i++) {
    document.getElementById(halaman[i]).classList.add("hidden");
  }
  document.getElementById(nama).classList.remove("hidden");
  if (nama == "beranda") tampilBeranda();
  if (nama == "keranjang") tampilKeranjang();
  if (nama == "admin") tampilAdmin("aVendor");
  window.scrollTo(0, 0);
}

function tampilBeranda() {
  var html = "";
  for (var i = 0; i < vendors.length; i++) {
    var v = vendors[i];
    if (v.aktif == false) continue;
    html += '<div class="card"><img src="' + v.foto + '" onerror="this.src=fotoPengganti">' +
      '<div class="isi"><h3>' + v.nama + '</h3>' +
      '<div class="kecil">' + v.kota + '</div>' +
      '<div class="kecil bintang">' + rataRata(v.id) + '</div><br>' +
      '<button class="btn" onclick="bukaToko(' + v.id + ')">Kunjungi Toko</button></div></div>';
  }
  document.getElementById("daftarVendor").innerHTML = html;

  var html2 = "";
  for (var j = 0; j < produk.length; j++) {
    if (cariVendor(produk[j].vendorId).aktif) html2 += kartuProduk(produk[j]);
  }
  document.getElementById("daftarProduk").innerHTML = html2;
}

function bukaToko(id) {
  var v = cariVendor(id);
  var html = '<button class="btn abu" onclick="tampilHalaman(\'beranda\')">&laquo; Kembali</button>';
  html += '<div class="kotak" style="margin-top:12px"><h2>' + v.nama + '</h2>' +
    '<p>' + v.deskripsi + '</p><div class="kecil">' + v.kota + ' &middot; <span class="bintang">' + rataRata(id) + '</span></div></div>';
  html += '<h3>Produk</h3><div class="grid">';
  for (var i = 0; i < produk.length; i++) {
    if (produk[i].vendorId == id) html += kartuProduk(produk[i]);
  }
  html += '</div><h3 style="margin-top:24px">Ulasan Pembeli</h3><div class="kotak">';
  var adaUlasan = false;
  for (var j = 0; j < ulasan.length; j++) {
    if (ulasan[j].vendorId == id) {
      adaUlasan = true;
      html += '<div class="ulasan"><b>' + ulasan[j].nama + '</b> <span class="bintang">' + "★".repeat(ulasan[j].bintang) + '</span><br>' + ulasan[j].isi + '</div>';
    }
  }
  if (!adaUlasan) html += '<p class="kecil">Belum ada ulasan.</p>';
  html += '<h4>Tulis ulasan</h4>' +
    '<input id="uNama" placeholder="Nama kamu"> ' +
    '<select id="uBintang"><option value="5">5 bintang</option><option value="4">4 bintang</option><option value="3">3 bintang</option><option value="2">2 bintang</option><option value="1">1 bintang</option></select><br><br>' +
    '<textarea id="uIsi" rows="3" style="width:90%" placeholder="Ceritakan pengalaman belanja kamu"></textarea><br><br>' +
    '<button class="btn" onclick="kirimUlasan(' + id + ')">Kirim Ulasan</button></div>';
  document.getElementById("toko").innerHTML = html;
  tampilHalamanToko();
}

function tampilHalamanToko() {
  document.getElementById("beranda").classList.add("hidden");
  document.getElementById("keranjang").classList.add("hidden");
  document.getElementById("admin").classList.add("hidden");
  document.getElementById("toko").classList.remove("hidden");
  window.scrollTo(0, 0);
}

function kirimUlasan(vendorId) {
  var nama = document.getElementById("uNama").value;
  var isi = document.getElementById("uIsi").value;
  var bintang = parseInt(document.getElementById("uBintang").value);
  if (nama == "" || isi == "") {
    alert("Nama dan ulasan harus diisi ya");
    return;
  }
  ulasan.push({ vendorId: vendorId, nama: nama, bintang: bintang, isi: isi });
  bukaToko(vendorId);
}

function tambahKeranjang(id) {
  var ketemu = false;
  for (var i = 0; i < keranjang.length; i++) {
    if (keranjang[i].produkId == id) {
      keranjang[i].jumlah++;
      ketemu = true;
    }
  }
  if (!ketemu) keranjang.push({ produkId: id, jumlah: 1 });
  updateJumlah();
  alert("Produk masuk keranjang");
}

function updateJumlah() {
  var total = 0;
  for (var i = 0; i < keranjang.length; i++) total += keranjang[i].jumlah;
  document.getElementById("jumlahKeranjang").innerText = total;
}

function hapusDariKeranjang(index) {
  keranjang.splice(index, 1);
  updateJumlah();
  tampilKeranjang();
}

function tampilKeranjang() {
  if (keranjang.length == 0) {
    document.getElementById("isiKeranjang").innerHTML = '<p>Keranjang masih kosong.</p>';
    return;
  }
  var html = '<table><tr><th>Produk</th><th>Toko</th><th>Harga</th><th>Jumlah</th><th>Subtotal</th><th></th></tr>';
  var total = 0;
  for (var i = 0; i < keranjang.length; i++) {
    var p = cariProduk(keranjang[i].produkId);
    var sub = p.harga * keranjang[i].jumlah;
    total += sub;
    html += '<tr><td>' + p.nama + '</td><td>' + cariVendor(p.vendorId).nama + '</td><td>' + rupiah(p.harga) + '</td>' +
      '<td>' + keranjang[i].jumlah + '</td><td>' + rupiah(sub) + '</td>' +
      '<td><button class="btn merah" onclick="hapusDariKeranjang(' + i + ')">Hapus</button></td></tr>';
  }
  html += '</table><h3>Total: ' + rupiah(total) + '</h3>' +
    '<div class="kotak"><h3>Pembayaran</h3>' +
    '<input id="bNama" placeholder="Nama penerima"><br><br>' +
    '<input id="bAlamat" placeholder="Alamat lengkap" style="width:80%"><br><br>' +
    '<select id="bMetode"><option>Transfer Bank</option><option>QRIS</option><option>E-Wallet (OVO/GoPay/Dana)</option><option>Bayar di Tempat (COD)</option></select><br><br>' +
    '<button class="btn" onclick="bayar()">Bayar Sekarang</button></div>';
  document.getElementById("isiKeranjang").innerHTML = html;
}

function bayar() {
  var nama = document.getElementById("bNama").value;
  var alamat = document.getElementById("bAlamat").value;
  var metode = document.getElementById("bMetode").value;
  if (nama == "" || alamat == "") {
    alert("Isi nama dan alamat dulu ya");
    return;
  }
  for (var i = 0; i < keranjang.length; i++) {
    var p = cariProduk(keranjang[i].produkId);
    var total = p.harga * keranjang[i].jumlah;
    var komisi = total * komisiPersen / 100;
    pesanan.push({
      no: pesanan.length + 1, pembeli: nama, vendorId: p.vendorId, produk: p.nama,
      total: total, komisi: komisi, persen: komisiPersen, metode: metode
    });
  }
  keranjang = [];
  updateJumlah();
  alert("Pembayaran berhasil! Terima kasih sudah belanja, " + nama + ".");
  tampilHalaman("beranda");
}

function tampilAdmin(bagian) {
  var semua = ["aVendor", "aProduk", "aLaporan", "aKomisi"];
  for (var i = 0; i < semua.length; i++) {
    document.getElementById(semua[i]).classList.add("hidden");
  }
  document.getElementById(bagian).classList.remove("hidden");
  if (bagian == "aVendor") adminVendor();
  if (bagian == "aProduk") adminProduk();
  if (bagian == "aLaporan") adminLaporan();
  if (bagian == "aKomisi") adminKomisi();
}

function adminVendor() {
  var html = '<table><tr><th>Nama</th><th>Kota</th><th>Status</th><th>Aksi</th></tr>';
  for (var i = 0; i < vendors.length; i++) {
    var v = vendors[i];
    var status = v.aktif ? "Aktif" : "Dinonaktifkan";
    var tombol = v.aktif ? "Nonaktifkan" : "Aktifkan";
    html += '<tr><td>' + v.nama + '</td><td>' + v.kota + '</td><td>' + status + '</td>' +
      '<td><button class="btn abu" onclick="ubahStatus(' + v.id + ')">' + tombol + '</button></td></tr>';
  }
  html += '</table><div class="kotak" style="margin-top:14px"><h4>Tambah vendor baru</h4>' +
    '<input id="vNama" placeholder="Nama toko"> <input id="vKota" placeholder="Kota"> ' +
    '<button class="btn" onclick="tambahVendor()">Tambah</button></div>';
  document.getElementById("aVendor").innerHTML = html;
}

function ubahStatus(id) {
  var v = cariVendor(id);
  v.aktif = !v.aktif;
  adminVendor();
}

function tambahVendor() {
  var nama = document.getElementById("vNama").value;
  var kota = document.getElementById("vKota").value;
  if (nama == "") { alert("Nama toko wajib diisi"); return; }
  vendors.push({ id: vendors.length + 1, nama: nama, kota: kota, deskripsi: "Toko baru di PasarKita.", foto: "", aktif: true });
  adminVendor();
}

function adminProduk() {
  var html = '<table><tr><th>Produk</th><th>Vendor</th><th>Harga</th><th>Aksi</th></tr>';
  for (var i = 0; i < produk.length; i++) {
    html += '<tr><td>' + produk[i].nama + '</td><td>' + cariVendor(produk[i].vendorId).nama + '</td><td>' + rupiah(produk[i].harga) + '</td>' +
      '<td><button class="btn merah" onclick="hapusProduk(' + i + ')">Hapus</button></td></tr>';
  }
  html += '</table>';
  document.getElementById("aProduk").innerHTML = html;
}

function hapusProduk(index) {
  if (confirm("Yakin hapus produk ini?")) {
    produk.splice(index, 1);
    adminProduk();
  }
}

function adminLaporan() {
  var totalJual = 0, totalKomisi = 0;
  var html = '<table><tr><th>No</th><th>Pembeli</th><th>Toko</th><th>Produk</th><th>Metode</th><th>Total</th><th>Komisi</th></tr>';
  for (var i = 0; i < pesanan.length; i++) {
    var o = pesanan[i];
    totalJual += o.total;
    totalKomisi += o.komisi;
    html += '<tr><td>' + o.no + '</td><td>' + o.pembeli + '</td><td>' + cariVendor(o.vendorId).nama + '</td><td>' + o.produk + '</td><td>' + o.metode + '</td><td>' + rupiah(o.total) + '</td><td>' + rupiah(o.komisi) + '</td></tr>';
  }
  if (pesanan.length == 0) html += '<tr><td colspan="7">Belum ada transaksi. Coba belanja dulu di halaman depan.</td></tr>';
  html += '</table>';
  var ringkas = '<div class="kotak"><b>Total penjualan:</b> ' + rupiah(totalJual) +
    '<br><b>Pendapatan platform (komisi):</b> ' + rupiah(totalKomisi) +
    '<br><b>Dibayarkan ke vendor:</b> ' + rupiah(totalJual - totalKomisi) + '</div>';
  document.getElementById("aLaporan").innerHTML = ringkas + html;
}

function adminKomisi() {
  document.getElementById("aKomisi").innerHTML = '<div class="kotak"><p>Komisi saat ini: <b>' + komisiPersen + '%</b> dari setiap penjualan.</p>' +
    '<input id="kPersen" type="number" min="0" max="100" value="' + komisiPersen + '"> % ' +
    '<button class="btn" onclick="simpanKomisi()">Simpan</button>' +
    '<p class="kecil">Perubahan hanya berlaku untuk pesanan berikutnya.</p></div>';
}

function simpanKomisi() {
  var angka = parseFloat(document.getElementById("kPersen").value);
  if (isNaN(angka) || angka < 0 || angka > 100) { alert("Isi angka 0 sampai 100"); return; }
  komisiPersen = angka;
  adminKomisi();
  alert("Komisi disimpan");
}

tampilBeranda();
</script>
</body>
</html>
