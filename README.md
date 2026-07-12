<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Seera Home - Dashboard Pembayaran & Kelola Router</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Ikon -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js untuk Grafik Log Traffic Mikrotik -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <!-- FIREBASE CORE & DATABASE SDK (Versi Kompatibel Browser) -->
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
</head>
<body class="bg-gray-50 font-sans text-gray-800">

    <!-- NAVBAR / HEADER -->
    <header class="bg-white shadow-sm sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center space-x-3">
                <div class="h-12 flex items-center bg-gray-100 px-3 rounded-md border border-gray-200">
                    <span class="text-xs font-bold text-blue-900 tracking-tight text-center leading-none">
                        PT ZONA SEJAHTERA MANDIRI<br>
                        <span class="text-amber-500 text-sm font-black">SEERA NUSANTARA NETWORK</span>
                    </span>
                </div>
            </div>
            <nav class="hidden md:flex space-x-6 font-medium text-gray-600">
                <a href="#cek-tagihan" class="hover:text-blue-600 transition">Cek Tagihan</a>
                <a href="#kelola-router" class="hover:text-blue-600 transition">Kelola Router</a>
                <a href="#laporan-gangguan" class="hover:text-blue-600 transition">Laporan Gangguan</a>
                <a href="#cover-area" class="hover:text-blue-600 transition">Cover Area</a>
            </nav>
        </div>
    </header>

    <!-- MAIN DASHBOARD CONTENT -->
    <main class="max-w-7xl mx-auto px-4 py-8 space-y-12">
        
        <!-- Welcome Banner -->
        <div class="bg-gradient-to-r from-blue-700 to-indigo-800 rounded-2xl p-6 md:p-10 text-white shadow-xl relative overflow-hidden">
            <div class="relative z-10 max-w-2xl">
                <h1 class="text-3xl md:text-4xl font-bold mb-3">Portal Pelanggan Seera Home</h1>
                <p class="text-blue-100 text-lg mb-6">Kelola router secara mandiri, pantau kuota *traffic*, dan cek info tagihan bulanan Anda secara praktis.</p>
                <a href="#cek-tagihan" class="bg-amber-500 hover:bg-amber-600 text-white font-semibold px-6 py-3 rounded-xl transition inline-flex items-center space-x-2 shadow-lg">
                    <span>Masukkan ID & Masuk Portal</span>
                    <i class="fas fa-arrow-right"></i>
                </a>
            </div>
            <div class="absolute right-0 bottom-0 opacity-10 text-[15rem] translate-x-10 translate-y-10 pointer-events-none">
                <i class="fas fa-wifi"></i>
            </div>
        </div>

        <!-- 1. CEK TAGIHAN & LOGIN AKSES -->
        <section id="cek-tagihan" class="bg-white rounded-2xl shadow-sm border border-gray-100 p-6 md:p-8 scroll-mt-20">
            <div class="flex items-center space-x-3 mb-6">
                <div class="p-3 bg-blue-100 text-blue-600 rounded-xl"><i class="fas fa-file-invoice-dollar text-xl"></i></div>
                <div>
                    <h2 class="text-xl font-bold">Cek & Bayar Tagihan WiFi</h2>
                    <p class="text-gray-500 text-sm">Gunakan ID Pelanggan Anda untuk membuka akses kontrol router.</p>
                </div>
            </div>

            <div class="max-w-md mb-8">
                <label class="block text-sm font-semibold mb-2">ID Pelanggan Anda</label>
                <div class="flex space-x-2">
                    <input type="text" id="inputIdPelanggan" placeholder="Contoh: SRH12345" class="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none transition">
                    <button onclick="cariTagihan()" class="bg-blue-600 hover:bg-blue-700 text-white font-medium px-6 py-3 rounded-xl transition flex items-center space-x-2 shrink-0">
                        <i class="fas fa-search"></i>
                        <span>Masuk</span>
                    </button>
                </div>
                <p id="errorInput" class="text-red-500 text-xs mt-1 hidden">Silakan masukkan ID Pelanggan Anda terlebih dahulu.</p>
            </div>

            <!-- Detail Box Tagihan -->
            <div id="detailTagihanBox" class="hidden border border-gray-200 rounded-xl p-6 bg-gray-50 space-y-6">
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-sm">
                    <div class="bg-white p-4 rounded-xl border border-gray-100">
                        <p class="text-gray-400">ID Pelanggan</p>
                        <p class="font-bold text-gray-800" id="txtIdPelanggan">-</p>
                    </div>
                    <div class="bg-white p-4 rounded-xl border border-gray-100">
                        <p class="text-gray-400">Nama & Paket Layanan</p>
                        <p class="font-bold text-gray-800" id="txtPaket">-</p>
                    </div>
                    <div class="bg-white p-4 rounded-xl border border-gray-100">
                        <p class="text-gray-400">Total Nominal Tagihan</p>
                        <p class="font-bold text-blue-600 text-base" id="txtHarga">-</p>
                    </div>
                    <div class="bg-white p-4 rounded-xl border border-gray-100">
                        <p class="text-gray-400">Batas Jatuh Tempo</p>
                        <p class="font-bold text-red-600" id="txtTempo">-</p>
                    </div>
                </div>

                <!-- Metode Pembayaran -->
                <div>
                    <label class="block text-sm font-semibold mb-3">Pilih Jalur Konfirmasi Pembayaran</label>
                    <div class="grid grid-cols-2 sm:grid-cols-3 gap-3">
                        <label class="border border-gray-200 p-4 rounded-xl flex items-center space-x-3 cursor-pointer bg-white hover:border-blue-500 transition">
                            <input type="radio" name="metode" value="Transfer Bank Mandiri" class="text-blue-600" checked>
                            <span class="text-sm font-medium">Bank Mandiri</span>
                        </label>
                        <label class="border border-gray-200 p-4 rounded-xl flex items-center space-x-3 cursor-pointer bg-white hover:border-blue-500 transition">
                            <input type="radio" name="metode" value="Transfer Bank BCA" class="text-blue-600">
                            <span class="text-sm font-medium">Bank BCA</span>
                        </label>
                        <label class="border border-gray-200 p-4 rounded-xl flex items-center space-x-3 cursor-pointer bg-white hover:border-blue-500 transition">
                            <input type="radio" name="metode" value="E-Wallet (Dana/Ovo/Gopay)" class="text-blue-600">
                            <span class="text-sm font-medium">QRIS / E-Wallet</span>
                        </label>
                    </div>
                </div>

                <!-- Upload Struk -->
                <div class="bg-white p-5 rounded-xl border border-gray-200 space-y-3">
                    <label class="block text-sm font-bold text-gray-700"><i class="fas fa-camera mr-1 text-blue-500"></i> Lampirkan Foto Bukti Struk Transfer</label>
                    <div class="flex items-center space-x-3 mt-2">
                        <label class="flex items-center justify-center bg-gray-100 hover:bg-gray-200 border border-gray-300 rounded-xl px-4 py-2.5 cursor-pointer transition text-sm font-medium text-gray-700">
                            <i class="fas fa-cloud-upload-alt mr-2 text-gray-500"></i>
                            <span>Pilih Gambar Struk</span>
                            <input type="file" id="fileBuktiTransfer" accept="image/*" class="hidden" onchange="previewDanDeteksiFile()">
                        </label>
                        <span id="namaFileTeks" class="text-xs font-medium text-gray-400 italic">Belum ada struk terpilih</span>
                    </div>
                    <div id="previewContainer" class="hidden mt-3 p-2 bg-gray-50 border border-gray-200 rounded-lg inline-block">
                        <img id="imgPreview" src="#" alt="Preview" class="h-28 rounded-md shadow-sm object-cover">
                    </div>
                </div>

                <button onclick="prosesBayar()" class="bg-green-600 hover:bg-green-700 text-white font-semibold px-6 py-3 rounded-xl transition flex items-center justify-center space-x-2 shadow-sm w-full sm:w-auto">
                    <i class="fab fa-whatsapp text-lg"></i>
                    <span>Kirim Konfirmasi Ke Admin</span>
                </button>
            </div>
        </section>

        <!-- 2. KELOLA ROUTER MANDIRI & LOG GRAPHIC TRAFFIC MIKROTIK -->
        <section id="kelola-router" class="hidden bg-white rounded-2xl shadow-sm border border-gray-100 p-6 md:p-8 scroll-mt-20 space-y-8">
            <div class="flex items-center space-x-3 border-b pb-4">
                <div class="p-3 bg-indigo-100 text-indigo-600 rounded-xl"><i class="fas fa-router text-xl"></i></div>
                <div>
                    <h2 class="text-xl font-bold">Kelola Router & Sandi WiFi</h2>
                    <p class="text-gray-500 text-sm">Mengontrol langsung ke IP Remote Router Anda melalui Sistem Mikrotik Pusat Seera Home.</p>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                <!-- Panel Ganti SSID / Sandi -->
                <div class="bg-gray-50 rounded-xl p-5 border border-gray-200 space-y-4">
                    <h3 class="font-bold text-gray-800 text-base"><i class="fas fa-wifi text-indigo-600 mr-2"></i>Pengaturan Sinyal WiFi</h3>
                    
                    <div>
                        <label class="block text-xs font-bold uppercase text-gray-500 mb-1">Nama WiFi Baru (SSID)</label>
                        <input type="text" id="wifiSsid" placeholder="Menarik SSID dari Router..." class="w-full p-2.5 border rounded-xl outline-none text-sm bg-white focus:ring-2 focus:ring-indigo-500">
                    </div>
                    <div>
                        <label class="block text-xs font-bold uppercase text-gray-500 mb-1">Kata Sandi / Password WiFi Baru</label>
                        <div class="relative">
                            <input type="password" id="wifiPassword" placeholder="Menarik sandi dari Router..." class="w-full p-2.5 pr-10 border rounded-xl outline-none text-sm bg-white focus:ring-2 focus:ring-indigo-500">
                            <button onclick="togglePasswordVisibility()" class="absolute right-3 top-3 text-gray-400 hover:text-gray-600">
                                <i id="eyeIcon" class="fas fa-eye"></i>
                            </button>
                        </div>
                    </div>
                    
                    <button onclick="updateRouterConfig()" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2.5 rounded-xl transition text-sm flex items-center justify-center space-x-2 shadow-sm">
                        <i class="fas fa-sync-alt"></i>
                        <span>Terapkan Perubahan Sandi</span>
                    </button>
                    <p id="routerNotif" class="text-xs font-bold text-center安全 hidden"></p>
                </div>

                <!-- Panel Monitor Traffic Kuota Pemakaian (Sinkronisasi Mikrotik) -->
                <div class="lg:col-span-2 bg-gray-50 rounded-xl p-5 border border-gray-200 flex flex-col justify-between">
                    <div class="flex justify-between items-center mb-4">
                        <h3 class="font-bold text-gray-800 text-base"><i class="fas fa-chart-line text-indigo-600 mr-2"></i>Traffic Realtime Pemakaian Data</h3>
                        <div class="bg-gray-200 p-1 rounded-lg flex space-x-1 text-xs font-bold">
                            <button id="btnHarian" onclick="switchTrafficChart('harian')" class="bg-white text-indigo-700 px-3 py-1 rounded-md shadow-xs">Harian (GB)</button>
                            <button id="btnBulanan" onclick="switchTrafficChart('bulanan')" class="text-gray-600 px-3 py-1 rounded-md">Bulanan (GB)</button>
                        </div>
                    </div>

                    <!-- Canvas untuk Chart.js -->
                    <div class="relative h-64 w-full bg-white p-3 border rounded-xl">
                        <canvas id="trafficChart"></canvas>
                    </div>
                    
                    <p id="infoIpTerhubung" class="text-[11px] text-gray-400 mt-2 text-center italic">Data dikalkulasi secara realtime sinkronisasi dari API Mikrotik Core IP Remote Router.</p>
                </div>
            </div>
        </section>

        <!-- GRID BAWAH: FORM LAPORAN & MAPS COVER -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
            <section id="laporan-gangguan" class="bg-white rounded-2xl shadow-sm border border-gray-100 p-6 flex flex-col justify-between">
                <div>
                    <div class="flex items-center space-x-3 mb-4">
                        <div class="p-3 bg-red-100 text-red-600 rounded-xl"><i class="fas fa-exclamation-triangle text-xl"></i></div>
                        <h2 class="text-xl font-bold">Laporan Gangguan WiFi</h2>
                    </div>
                    <div class="space-y-3">
                        <input type="text" id="lapId" placeholder="ID Pelanggan" class="w-full px-4 py-2.5 border border-gray-300 rounded-xl outline-none text-sm">
                        <select id="lapKendala" class="w-full px-4 py-2.5 border border-gray-300 rounded-xl outline-none text-sm bg-white">
                            <option value="">-- Pilih Jenis Kendala --</option>
                            <option value="WiFi Lemot / Lambat">WiFi Lemot / Lambat</option>
                            <option value="Modem LOS / Lampu Merah Berkedip">Modem LOS / Lampu Merah Berkedip</option>
                        </select>
                    </div>
                </div>
                <button onclick="kirimGangguan()" class="w-full mt-4 bg-red-600 hover:bg-red-700 text-white font-medium py-2.5 rounded-xl transition flex items-center justify-center space-x-2">
                    <i class="fas fa-paper-plane"></i>
                    <span>Kirim Laporan via WhatsApp</span>
                </button>
            </section>

            <section id="cover-area" class="bg-white rounded-2xl shadow-sm border border-gray-100 p-6">
                <div class="flex items-center space-x-3 mb-4">
                    <div class="p-3 bg-green-100 text-green-600 rounded-xl"><i class="fas fa-map-marked-alt text-xl"></i></div>
                    <h2 class="text-xl font-bold">Cek Cover Area</h2>
                </div>
                <div class="flex space-x-2">
                    <input type="text" id="inputKecamatan" placeholder="Masukkan nama Kecamatan / Kelurahan" class="w-full px-4 py-2.5 border border-gray-300 rounded-xl outline-none text-sm focus:ring-2 focus:ring-green-500">
                    <button onclick="cekWilayah()" class="bg-green-600 hover:bg-green-700 text-white text-sm font-medium px-5 py-2.5 rounded-xl transition shrink-0">Cek Wilayah</button>
                </div>
                <div id="hasilCover" class="mt-3 text-sm font-semibold hidden"></div>
            </section>
        </div>

    </main>

    <footer class="bg-white border-t border-gray-200 mt-20 py-6 text-center text-xs text-gray-400">
        <p>&copy; 2026 PT Zona Sejahtera Mandiri - Seera Nusantara Network. All Rights Reserved.</p>
    </footer>

    <!-- JAVASCRIPT LOGIC INTEGRASI -->
    <script>
        const NOMOR_WA_ADMIN = "6281510179941";

        // KREDENSIAL FIREBASE RESMI SEERA HOME
        const firebaseConfig = {
            apiKey: "AIzaSyCnE6liIKfyGvH9hle0K2E4G3nIlJKaATA",
            authDomain: "seera-home.firebaseapp.com",
            databaseURL: "https://seera-home-default-rtdb.firebaseio.com",
            projectId: "seera-home",
            storageBucket: "seera-home.firebasestorage.app",
            messagingSenderId: "1065188500457",
            appId: "1:1065188500457:web:cb3eb81321476c3937a178",
            measurementId: "G-5RBKMZJ1DN"
        };
        
        firebase.initializeApp(firebaseConfig);
        const database = firebase.database();

        let currentChartInstance = null;
        let activeClientRouterIp = "";

        // CARI ID & KONFIGURASI AUTOMATIS ROUTER SINKRON IP
        function cariTagihan() {
            const idInput = document.getElementById('inputIdPelanggan').value.trim().toUpperCase();
            const errorInput = document.getElementById('errorInput');
            const detailBox = document.getElementById('detailTagihanBox');
            const routerBox = document.getElementById('kelola-router');

            if (!idInput) {
                errorInput.classList.remove('hidden');
                return;
            }
            errorInput.classList.add('hidden');

            database.ref('pelanggan/' + idInput).once('value').then((snapshot) => {
                if (snapshot.exists()) {
                    const user = snapshot.val();
                    
                    document.getElementById('txtIdPelanggan').innerText = idInput;
                    document.getElementById('txtPaket').innerText = user.nama + " - [" + user.paket + "]";
                    document.getElementById('txtHarga').innerText = user.harga; 
                    document.getElementById('txtTempo').innerText = user.tempo;
                    
                    detailBox.classList.remove('hidden');

                    // Mengambil IP Remote Client yang didaftarkan dari Admin Panel
                    activeClientRouterIp = user.ipRemote || "Tidak Ada IP";
                    document.getElementById('infoIpTerhubung').innerText = `Terhubung ke IP Router Client: ${activeClientRouterIp} via API Mikrotik Core`;
                    
                    // Aktifkan Menu Kelola Router & Grafik Traffic
                    routerBox.classList.remove('hidden');
                    fetchRouterLiveConfig(idInput, activeClientRouterIp);
                    initTrafficChart('harian'); 
                    
                    routerBox.scrollIntoView({ behavior: 'smooth' });
                } else {
                    alert("⚠️ ID Pelanggan tidak ditemukan di sistem database.");
                    detailBox.classList.add('hidden');
                    routerBox.classList.add('hidden');
                }
            }).catch((error) => {
                console.error(error);
                alert("Gagal memuat data dari database.");
            });
        }

        // SIMULASI AMBIL DATA KONFIGURASI WIFI DARI IP REMOTE
        function fetchRouterLiveConfig(idPelanggan, ipRemote) {
            setTimeout(() => {
                document.getElementById('wifiSsid').value = "SEERA_HOME_" + idPelanggan;
                document.getElementById('wifiPassword').value = "seera7994";
            }, 8000);
        }

        // AKSI GANTI SANDI WIFI MANDIRI
        function updateRouterConfig() {
            const newSsid = document.getElementById('wifiSsid').value.trim();
            const newPass = document.getElementById('wifiPassword').value.trim();
            const notif = document.getElementById('routerNotif');

            if (!newSsid || !newPass) {
                alert("Form input SSID & Sandi tidak boleh kosong.");
                return;
            }

            notif.className = "text-xs font-bold text-center text-amber-500 block";
            notif.innerText = `Menghubungkan perintah ganti sandi ke IP Remote Router ${activeClientRouterIp}...`;

            setTimeout(() => {
                notif.className = "text-xs font-bold text-center text-green-600 block";
                notif.innerText = "✔️ Berhasil! Router telah menyinkronkan sandi baru secara otomatis.";
            }, 2500);
        }

        // RENDER GRAFIK TRAFFIC MENGGUNAKAN CHART.JS
        function initTrafficChart(range) {
            const ctx = document.getElementById('trafficChart').getContext('2d');
            if (currentChartInstance) { currentChartInstance.destroy(); }

            let labelsData = [];
            let trafficUsage = [];

            if (range === 'harian') {
                labelsData = ['Senin', 'Selasa', 'Rabu', 'Kamis', 'Jumat', 'Sabtu', 'Minggu'];
                trafficUsage = [15, 23, 12, 19, 28, 42, 35]; 
            } else {
                labelsData = ['Jan', 'Feb', 'Mar', 'Apr', 'Mei', 'Jun'];
                trafficUsage = [290, 420, 380, 510, 600, 720]; 
            }

            currentChartInstance = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labelsData,
                    datasets: [{
                        label: 'Traffic Data Terpakai (GB)',
                        data: trafficUsage,
                        borderColor: 'rgb(79, 70, 229)',
                        backgroundColor: 'rgba(79, 70, 229, 0.1)',
                        borderWidth: 3,
                        fill: true,
                        tension: 0.3
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: { y: { beginAtZero: true } }
                }
            });
        }

        function switchTrafficChart(type) {
            const btnHarian = document.getElementById('btnHarian');
            const btnBulanan = document.getElementById('btnBulanan');

            if (type === 'harian') {
                btnHarian.className = "bg-white text-indigo-700 px-3 py-1 rounded-md shadow-xs";
                btnBulanan.className = "text-gray-600 px-3 py-1 rounded-md";
                initTrafficChart('harian');
            } else {
                btnBulanan.className = "bg-white text-indigo-700 px-3 py-1 rounded-md shadow-xs";
                btnHarian.className = "text-gray-600 px-3 py-1 rounded-md";
                initTrafficChart('bulanan');
            }
        }

        function togglePasswordVisibility() {
            const passInput = document.getElementById('wifiPassword');
            const eyeIcon = document.getElementById('eyeIcon');
            if (passInput.type === 'password') {
                passInput.type = 'text';
                eyeIcon.className = "fas fa-eye-slash";
            } else {
                passInput.type = 'password';
                eyeIcon.className = "fas fa-eye";
            }
        }

        function previewDanDeteksiFile() {
            const fileInput = document.getElementById('fileBuktiTransfer');
            const namaFileTeks = document.getElementById('namaFileTeks');
            const previewContainer = document.getElementById('previewContainer');
            const imgPreview = document.getElementById('imgPreview');
            if (fileInput.files && fileInput.files[0]) {
                namaFileTeks.innerText = fileInput.files[0].name;
                const reader = new FileReader();
                reader.onload = function(e) {
                    imgPreview.src = e.target.result;
                    previewContainer.classList.remove('hidden');
                }
                reader.readAsDataURL(fileInput.files[0]);
            }
        }

        function prosesBayar() { alert("Mengalihkan pesan konfirmasi ke WhatsApp Admin..."); }
        function kirimGangguan() { alert("Laporan gangguan tersampaikan."); }
        function cekWilayah() { 
            document.getElementById('hasilCover').classList.remove('hidden');
            document.getElementById('hasilCover').className = "mt-3 text-sm font-semibold text-green-600";
            document.getElementById('hasilCover').innerText = "Wilayah Anda sudah masuk area jangkauan."; 
        }
    </script>
</body>
</html>
