<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HULO KOPI - Hybrid POS System v5.0</title>
    <script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>
    <style>
        :root {
            --primary-red: #e74c3c;
            --dark-red: #c0392b;
            --black-bg: #0d0d0d;
            --panel-bg: #161616;
            --card-bg: #1f1f1f;
            --text-light: #f5f6fa;
            --text-muted: #7f8c8d;
            --border-color: #222222;
            --success-green: #2ecc71;
            --warning-orange: #f39c12;
            --neon-purple: #9b59b6;
            --neon-blue: #3498db;
        }

        * { box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; transition: all 0.2s ease; }
        body { background-color: var(--black-bg); color: var(--text-light); margin: 0; padding: 0; display: flex; height: 100vh; overflow: hidden; }

        /* SCREEN LOGIN */
        #login-screen { position: fixed; top:0; left:0; width:100vw; height:100vh; background: radial-gradient(circle, #2c0b0b 0%, #000000 100%); display: flex; justify-content: center; align-items: center; z-index: 9999; }
        .login-box { background: rgba(22, 22, 22, 0.95); padding: 40px; border-radius: 15px; width: 400px; border: 2px solid var(--primary-red); box-shadow: 0 0 25px rgba(231, 76, 60, 0.3); text-align: center; }
        .login-box h2 { color: var(--primary-red); margin-bottom: 5px; font-size: 32px; letter-spacing: 2px; margin-top: 0; }
        .login-box p { color: var(--text-muted); margin-bottom: 30px; font-size: 14px; }
        .input-group { margin-bottom: 20px; text-align: left; }
        .input-group label { display: block; font-size: 12px; margin-bottom: 5px; color: var(--text-light); text-transform: uppercase; letter-spacing: 1px; }
        .input-group input, .input-group select { width: 100%; padding: 12px; background: #222; border: 1px solid #444; border-radius: 6px; color: #fff; font-size: 14px; }
        .input-group input:focus, .input-group select:focus { border-color: var(--primary-red); outline: none; }
        .btn-login { background: var(--primary-red); color: white; border: none; padding: 14px; width: 100%; font-weight: bold; font-size: 16px; border-radius: 6px; cursor: pointer; margin-top: 10px; text-transform: uppercase; }
        .btn-login:hover { background: var(--dark-red); box-shadow: 0 0 15px rgba(231,76,60,0.5); }

        /* FEATURE BADGES (STIKER PENANDA) */
        .badge { display: inline-block; padding: 3px 8px; font-size: 10px; font-weight: bold; border-radius: 4px; text-transform: uppercase; margin-left: 5px; letter-spacing: 0.5px; vertical-align: middle; }
        .badge-ai { background: var(--neon-purple); color: #fff; box-shadow: 0 0 8px rgba(155, 89, 182, 0.4); }
        .badge-checkout { background: var(--primary-red); color: #fff; box-shadow: 0 0 8px rgba(231, 76, 60, 0.4); }
        .badge-cloud { background: var(--success-green); color: #0d0d0d; }
        .badge-alarm { background: var(--warning-orange); color: #fff; animation: pulse 1.5s infinite; }
        .badge-profit { background: #27ae60; color: #fff; }
        .badge-discount { background: #e67e22; color: #fff; }
        .badge-config { background: var(--neon-blue); color: #fff; }

        @keyframes pulse {
            0% { opacity: 0.6; }
            50% { opacity: 1; }
            100% { opacity: 0.6; }
        }

        /* MAIN APP WRAPPER */
        #app-container { display: none; width: 100%; height: 100vh; flex-direction: row; }
        .sidebar { width: 280px; background: #000000; padding: 25px 20px; border-right: 2px solid var(--primary-red); display: flex; flex-direction: column; justify-content: space-between; }
        .logo-area { text-align: center; border-bottom: 1px solid #222; padding-bottom: 20px; }
        .logo-area h1 { color: var(--primary-red); font-weight: 900; letter-spacing: 3px; margin: 0; font-size: 26px; }
        .logo-area span { color: #fff; font-size: 11px; text-transform: uppercase; }
        
        .nav-menu { margin-top: 30px; flex-grow: 1; }
        .menu-item { padding: 14px 15px; background: var(--panel-bg); margin-bottom: 12px; border-radius: 6px; cursor: pointer; border-left: 4px solid var(--primary-red); font-weight: 600; font-size: 13px; display: flex; align-items: center; justify-content: space-between; }
        .menu-item:hover, .menu-item.active { background: var(--primary-red); color: white; border-left-color: #fff; }

        .main-wrapper { flex: 1; display: flex; flex-direction: column; height: 100vh; }
        .global-header { background: #111; height: 70px; border-bottom: 1px solid var(--border-color); display: flex; align-items: center; justify-content: space-between; padding: 0 25px; }
        
        /* STATUS BAR JARINGAN */
        .status-bar-container { display: flex; align-items: center; gap: 15px; }
        .status-pill { display: flex; align-items: center; gap: 8px; padding: 6px 14px; border-radius: 20px; font-size: 13px; font-weight: bold; border: 1px solid transparent; }
        .status-pill.online { background: rgba(46, 204, 113, 0.15); color: var(--success-green); border-color: rgba(46, 204, 113, 0.3); }
        .status-pill.offline { background: rgba(231, 76, 60, 0.15); color: var(--primary-red); border-color: rgba(231, 76, 60, 0.3); }
        .status-dot { width: 8px; height: 8px; border-radius: 50%; display: inline-block; }
        .status-pill.online .status-dot { background: var(--success-green); }
        .status-pill.offline .status-dot { background: var(--primary-red); }

        .btn-sync { background: #2980b9; color: #fff; border: none; padding: 8px 16px; border-radius: 6px; font-size: 12px; font-weight: bold; cursor: pointer; }
        .btn-sync:hover { background: #3498db; }

        /* WORKSPACE CONTENT */
        .workspace { flex: 1; position: relative; background: var(--black-bg); }
        .content-view { width: 100%; padding: 25px; overflow-y: auto; display: none; position: absolute; top:0; left:0; height: 100%; }
        .content-view.active { display: flex; flex-direction: column; }

        .dashboard-banner { background: linear-gradient(135deg, #1c1c1c 0%, #290a0a 100%); padding: 20px; border-radius: 12px; border: 1px solid #333; margin-bottom: 20px; display: flex; justify-content: space-between; align-items: center; }

        /* KASIR LAYOUT */
        .cashier-layout { display: flex; gap: 20px; height: calc(100vh - 190px); }
        .product-grid { flex: 2; display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 15px; overflow-y: auto; align-content: start; padding-right: 5px; }
        .product-card { background: var(--card-bg); border: 1px solid #222; border-radius: 10px; padding: 20px; text-align: center; cursor: pointer; position: relative; display: flex; flex-direction: column; justify-content: space-between; min-height: 140px; }
        .product-card:hover { border-color: var(--primary-red); transform: translateY(-3px); }
        .product-card.low-stock { border: 1px dashed var(--warning-orange); box-shadow: inset 0 0 10px rgba(243, 156, 18, 0.1); }
        .product-price { color: var(--primary-red); font-weight: 700; margin-top: 5px; font-size: 16px; }

        /* CART & PROMO PANEL */
        .cart-panel { width: 400px; background: var(--panel-bg); border-radius: 10px; padding: 20px; display: flex; flex-direction: column; border: 1px solid #222; }
        .cart-items { flex: 1; overflow-y: auto; margin-bottom: 15px; }
        .cart-item { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 1px solid #222; font-size: 14px; }
        .cart-item-actions { display: flex; gap: 5px; }
        .btn-item-qty { background: #333; border: none; color: #fff; width: 24px; height: 24px; border-radius: 4px; cursor: pointer; font-weight: bold; }
        .btn-item-qty:hover { background: var(--primary-red); }
        
        .promo-selector-box { margin-bottom: 12px; background: #111; padding: 10px; border-radius: 6px; border: 1px solid #333; }
        .promo-selector-box label { font-size: 11px; text-transform: uppercase; color: var(--text-muted); display: block; margin-bottom: 5px; }
        .promo-select { width: 100%; background: #222; border: 1px solid #444; color: #fff; padding: 8px; border-radius: 4px; font-size: 13px; }

        .cart-summary { background: #0d0d0d; padding: 15px; border-radius: 8px; border: 1px solid #222; }
        .summary-row { display: flex; justify-content: space-between; margin-bottom: 8px; font-size: 13px; color: var(--text-muted); }
        .total-row { display: flex; justify-content: space-between; margin-top: 5px; padding-top: 5px; border-top: 1px dashed #333; font-weight: bold; font-size: 16px; }

        /* FINANCIAL REPORT CARDS */
        .report-summary-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 15px; margin-bottom: 20px; }
        .rep-card { background: var(--panel-bg); padding: 20px; border-radius: 10px; border: 1px solid #222; text-align: left; }
        .rep-card h4 { margin: 0 0 10px 0; font-size: 12px; color: var(--text-muted); text-transform: uppercase; letter-spacing: 1px; }
        .rep-card .rep-val { font-size: 22px; font-weight: bold; color: #fff; }

        /* INPUT CONFIG FORM */
        .config-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; background: var(--panel-bg); padding: 25px; border-radius: 10px; border: 1px solid #222; }
        .form-actions { margin-top: 20px; text-align: right; }
        .btn-save-config { background: var(--success-green); color: #000; border: none; padding: 12px 25px; font-weight: bold; border-radius: 6px; cursor: pointer; }
        .btn-save-config:hover { opacity: 0.9; }

        /* TABLES */
        .table-responsive { width: 100%; overflow-x: auto; background: var(--panel-bg); padding: 20px; border-radius: 8px; border: 1px solid #222; margin-top: 15px; }
        table { width: 100%; border-collapse: collapse; }
        th, td { padding: 12px 15px; border-bottom: 1px solid #222; text-align: left; font-size: 14px; }
        th { background-color: #000; color: var(--primary-red); text-transform: uppercase; font-size: 12px; letter-spacing: 1px; }

        /* PRINTER STYLE */
        #print-area { display: none; }
        @media print {
            body * { visibility: hidden; }
            #print-area, #print-area * { visibility: visible; }
            #print-area { display: block; position: absolute; left: 0; top: 0; width: 58mm; padding: 2mm; color: #000; font-family: monospace; font-size: 11px; }
        }
    </style>
</head>
<body>

    <div id="login-screen">
        <div class="login-box">
            <h2>HULO KOPI</h2>
            <p>Hybrid POS Engine v5.0</p>
            <div class="input-group">
                <label>Pilih Hak Akses</label>
                <select id="login-role">
                    <option value="kasir">Kasir Toko</option>
                    <option value="admin">Administrator / Owner</option>
                </select>
            </div>
            <div class="input-group">
                <label>Username</label>
                <input type="text" id="login-username" value="kasirhulo">
            </div>
            <div class="input-group">
                <label>Password</label>
                <input type="password" id="login-password" value="hulo312">
            </div>
            <button class="btn-login" onclick="eksekusiLoginSistem()">Masuk Sistem</button>
        </div>
    </div>

    <div id="app-container">
        <div class="sidebar">
            <div>
                <div class="logo-area">
                    <h1>HULO KOPI</h1>
                    <span id="role-badge">KASIR MODE</span>
                </div>
                <div class="nav-menu">
                    <div class="menu-item active" id="nav-pos" onclick="pindahTabSistem('pos-view')">
                        <span>Mesin Kasir</span><span class="badge badge-checkout">POS</span>
                    </div>
                    <div class="menu-item" id="nav-crud" onclick="pindahTabSistem('crud-view')">
                        <span>Kelola Produk</span><span class="badge badge-ai">HPP/AI</span>
                    </div>
                    <div class="menu-item" id="nav-report" onclick="pindahTabSistem('report-view')">
                        <span>Laporan Keuangan</span><span class="badge badge-profit">PROFIT</span>
                    </div>
                    <div class="menu-item" id="nav-config" onclick="pindahTabSistem('config-view')">
                        <span>Pengaturan Gateway</span><span class="badge badge-config">GATEWAY</span>
                    </div>
                </div>
            </div>
            <div style="color: var(--text-muted); font-size: 12px; border-top: 1px solid #222; padding-top: 15px;">
                <p style="margin:0 0 10px 0;">Petugas: <b id="user-logged" style="color: #fff;">-</b></p>
                <button style="background:#c0392b; color:white; border:none; padding:8px; width:100%; border-radius:4px; font-weight:bold; cursor:pointer; text-transform:uppercase;" onclick="logoutSistem()">Keluar Aplikasi</button>
            </div>
        </div>

        <div class="main-wrapper">
            <div class="global-header">
                <div style="font-size: 14px; font-weight: bold; color: var(--text-muted);" id="liveClock"></div>
                <div class="status-bar-container">
                    <button class="btn-sync" onclick="tarikDataGoogleSheets()">🔄 Tarik Cloud Data</button>
                    <div id="connection-status-pill" class="status-pill online">
                        <span class="status-dot"></span>
                        <span id="connection-status-text">CLOUD CONNECTED</span>
                    </div>
                </div>
            </div>

            <div class="workspace">
                
                <div id="pos-view" class="content-view active">
                    <div class="dashboard-banner">
                        <div>
                            <h3 style="margin: 0 0 5px 0;">Hybrid Core Engine v5.0</h3>
                            <p style="margin: 0; font-size: 13px; color: var(--text-muted);" id="dashboard-sync-desc">Sistem lokal terhubung secara aman dengan basis data cloud.</p>
                        </div>
                        <div id="db-indicator-box" style="padding: 8px 15px; border-radius: 6px; font-size: 12px; font-weight: bold; background: rgba(46, 204, 113, 0.1); color: var(--success-green);">
                            DATABASE: ACTIVE
                        </div>
                    </div>

                    <div class="cashier-layout">
                        <div class="product-grid" id="productGrid"></div>
                        
                        <div class="cart-panel">
                            <h3 style="margin:0; border-bottom:1px solid #333; padding-bottom:10px; font-size:16px;">Billing System</h3>
                            <div class="cart-items" id="cartItems">
                                <p style="text-align:center; margin-top:40%; color:var(--text-muted);">Keranjang Belanja Kosong</p>
                            </div>
                            
                            <div class="promo-selector-box">
                                <label>Terapkan Program Promo <span class="badge badge-discount">% / Rp</span></label>
                                <select id="promoSelect" class="promo-select" onchange="updateBillUI()">
                                    <option value="NONE">Tanpa Potongan Diskon</option>
                                    <option value="DISC10">Diskon Member Baru - 10%</option>
                                    <option value="DISC20">Diskon Happy Hour - 20%</option>
                                    <option value="POT5K">Potongan Langsung - Rp 5.000</option>
                                    <option value="POT10K">Potongan Voucher Owner - Rp 10.000</option>
                                </select>
                            </div>

                            <div class="cart-summary">
                                <div class="summary-row"><span>Subtotal Produk</span><span id="txt-subtotal">Rp 0</span></div>
                                <div class="summary-row"><span>Pajak Resto (10%)</span><span id="txt-tax">Rp 0</span></div>
                                <div class="summary-row"><span style="color:var(--warning-orange);">Potongan Diskon</span><span id="txt-discount" style="color:var(--warning-orange);">Rp 0</span></div>
                                <div class="total-row"><span>TOTAL AKHIR</span><span id="txt-total" style="color:var(--primary-red);">Rp 0</span></div>
                            </div>
                            <button class="btn-checkout" onclick="prosesTransaksiKasir()">LOG TRANS_DATA & PRINT</button>
                        </div>
                    </div>
                </div>

                <div id="crud-view" class="content-view">
                    <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
                        <h3 style="margin:0;">Manajemen Produk & AI Target Margin</h3>
                        <button class="btn-sync" style="background:var(--success-green); color:#000;" onclick="bukaModalTambahProduk()">+ Tambah Menu Baru</button>
                    </div>
                    <div class="table-responsive">
                        <table>
                            <thead>
                                <tr>
                                    <th>ID Menu</th>
                                    <th>Nama Produk</th>
                                    <th>Kategori</th>
                                    <th>HPP Asli</th>
                                    <th>Target Margin (%)</th>
                                    <th>Harga Jual AI (Bulat)</th>
                                    <th>Sisa Stok</th>
                                    <th>Aksi</th>
                                </tr>
                            </thead>
                            <tbody id="crudTableBody"></tbody>
                        </table>
                    </div>
                </div>

                <div id="report-view" class="content-view">
                    <h3>Audit Laporan Finansial Sesi Ini <span class="badge badge-profit">Realtime</span></h3>
                    
                    <div class="report-summary-grid">
                        <div class="rep-card">
                            <h4>Omzet Kotor (Gross)</h4>
                            <div class="rep-val" id="rep-omzet" style="color:var(--neon-blue);">Rp 0</div>
                        </div>
                        <div class="rep-card">
                            <h4>Total HPP Terjual</h4>
                            <div class="rep-val" id="rep-hpp" style="color:var(--warning-orange);">Rp 0</div>
                        </div>
                        <div class="rep-card">
                            <h4>Pajak Terkumpul</h4>
                            <div class="rep-val" id="rep-pajak">Rp 0</div>
                        </div>
                        <div class="rep-card" style="border-color:var(--success-green); background:rgba(46, 204, 113, 0.05)">
                            <h4>Keuntungan Bersih (Net Profit)</h4>
                            <div class="rep-val" id="rep-profit" style="color:var(--success-green);">Rp 0</div>
                        </div>
                    </div>

                    <div class="table-responsive">
                        <table>
                            <thead>
                                <tr>
                                    <th>ID Invoice</th>
                                    <th>Waktu Transaksi</th>
                                    <th>Petugas Kasir</th>
                                    <th>Total Bayar</th>
                                    <th>Laba Bersih</th>
                                </tr>
                            </thead>
                            <tbody id="reportTableBody"></tbody>
                        </table>
                    </div>
                </div>

                <div id="config-view" class="content-view">
                    <h3>Pengaturan Gateway & Cloud Endpoint <span class="badge badge-config">Kunci Parameter</span></h3>
                    <p style="color:var(--text-muted); margin-bottom:20px; font-size:14px;">Sesuaikan konfigurasi di bawah ini untuk menghubungkan aplikasi POS lokal dengan ekosistem database Google Sheets Anda dan bot notifikasi otomatis.</p>
                    
                    <div class="config-grid">
                        <div class="input-group">
                            <label>Google Apps Script Webapp URL</label>
                            <input type="text" id="cfg-sheet-url" placeholder="https://script.google.com/macros/s/.../exec">
                        </div>
                        <div class="input-group">
                            <label>Nomor WhatsApp Owner (Notifikasi)</label>
                            <input type="text" id="cfg-owner-wa" placeholder="62812xxxx">
                        </div>
                        <div class="input-group">
                            <label>API Gateway Gateway Token / Bot Token</label>
                            <input type="password" id="cfg-api-token" placeholder="Masukkan token integrasi pesan/bot">
                        </div>
                        <div class="input-group">
                            <label>Nama Cabang Outlet</label>
                            <input type="text" id="cfg-outlet-name" value="HULO KOPI - Sentani">
                        </div>
                    </div>
                    <div class="form-actions">
                        <button class="btn-save-config" onclick="simpanKonfigurasiGateway()">Simpan Sistem Parameter</button>
                    </div>
                </div>

            </div>
        </div>
    </div>

    <div id="print-area">
        <div style="text-align:center; margin-bottom:10px;">
            <h3 style="margin:0; font-size:14px;" id="p-outlet-title">HULO KOPI</h3>
            <p style="margin:2px 0; font-size:9px;">Cloud Synchronized POS System</p>
            <p id="p-date" style="margin:2px 0; font-size:9px;"></p>
            <p id="p-invoice" style="margin:2px 0; font-size:9px; font-weight:bold;"></p>
        </div>
        <div style="border-top:1px dashed #000; margin:5px 0;"></div>
        <div id="p-items"></div>
        <div style="border-top:1px dashed #000; margin:5px 0;"></div>
        <div style="font-size:10px;">
            <div style="display:flex; justify-content:space-between;"><span>Subtotal:</span><span id="p-subtotal"></span></div>
            <div style="display:flex; justify-content:space-between;"><span>Pajak (10%):</span><span id="p-tax"></span></div>
            <div style="display:flex; justify-content:space-between; color:#000;"><span id="p-disc-label">Diskon:</span><span id="p-discount"></span></div>
            <div style="display:flex; justify-content:space-between; font-weight:bold; font-size:12px; margin-top:4px;">
                <span>TOTAL AKHIR:</span><span id="p-grandtotal"></span>
            </div>
        </div>
        <div style="text-align:center; margin-top:15px; font-size:9px;">
            <p style="margin:0;">Semoga hari ini menyenangkan buat kalian!</p>
            <p style="margin:2px 0 0 0; font-weight:bold;">Sesi Berhasil Disinkronisasi</p>
        </div>
    </div>

    <script>
        // Default Configuration (Disimpan di localStorage agar permanen walau di-refresh)
        let systemConfig = {
            sheetUrl: localStorage.getItem('hulo_sheetUrl') || "URL_DEPLOYMENT_ANDA_DI_SINI",
            ownerWa: localStorage.getItem('hulo_ownerWa') || "6281245834165",
            apiToken: localStorage.getItem('hulo_apiToken') || "TOKEN_DEFAULT",
            outletName: localStorage.getItem('hulo_outletName') || "HULO KOPI - Sentani"
        };

        let dataKredensialAkun = {
            admin: { username: "hulokopi", password: "hulo312", nama: "Admin Owner Hulo" },
            kasir: { username: "kasirhulo", password: "hulo312", nama: "Kasir Sesi Utama" }
        };

        // Database lokal + fallback data awal
        let databaseItemPOS = JSON.parse(localStorage.getItem('hulo_db')) || [
            { id: "HULO-01", nama: "Espresso Nusantara", kategori: "Coffee", hpp: 5000, margin: 200, stok: 99 },
            { id: "HULO-02", nama: "Kopi Susu Hulo Aren", kategori: "Coffee", hpp: 7000, margin: 185, stok: 8 },
            { id: "HULO-03", nama: "Americano Bold", kategori: "Coffee", hpp: 4500, margin: 220, stok: 45 }
        ];

        let keranjangSesi = [];
        let logTransaksiHariIni = [];
        let userSesiAktif = null;
        let isSystemOnline = true;

        // Terapkan nilai config ke kolom form saat dimuat
        document.getElementById('cfg-sheet-url').value = systemConfig.sheetUrl;
        document.getElementById('cfg-owner-wa').value = systemConfig.ownerWa;
        document.getElementById('cfg-api-token').value = systemConfig.apiToken;
        document.getElementById('cfg-outlet-name').value = systemConfig.outletName;

        function eksekusiLoginSistem() {
            const role = document.getElementById('login-role').value;
            const userIn = document.getElementById('login-username').value.trim();
            const passIn = document.getElementById('login-password').value.trim();

            if (userIn === dataKredensialAkun[role].username && passIn === dataKredensialAkun[role].password) {
                userSesiAktif = { role: role, nama: dataKredensialAkun[role].nama };
                document.getElementById('user-logged').innerText = userSesiAktif.nama;
                document.getElementById('role-badge').innerText = role === 'admin' ? "ADMIN SECURITY" : "KASIR ACTIVE";
                
                // Pembatasan Hak Akses Menu Kontrol
                document.getElementById('nav-crud').style.display = role === 'admin' ? 'flex' : 'none';
                document.getElementById('nav-report').style.display = role === 'admin' ? 'flex' : 'none';
                document.getElementById('nav-config').style.display = role === 'admin' ? 'flex' : 'none';

                document.getElementById('login-screen').style.display = 'none';
                document.getElementById('app-container').style.display = 'flex';
                
                kalkulasiRekomendasiHargaAI();
                renderProdukKeKasir();
                renderTableCRUD();
                hitungUlangLaporanKeuangan();
                tarikDataGoogleSheets(); 
            } else {
                Swal.fire({ icon: 'error', title: 'Akses Ditolak', text: 'Kredensial salah!', background: '#161616', color: '#fff', confirmButtonColor: '#e74c3c' });
            }
        }

        function setStatusSistemJaringan(online, errorMsg = "") {
            isSystemOnline = online;
            document.getElementById('connection-status-pill').className = online ? "status-pill online" : "status-pill offline";
            document.getElementById('connection-status-text').innerText = online ? "CLOUD SYNCED" : "EDGE LOCAL MODE";
            document.getElementById('db-indicator-box').style.color = online ? "var(--success-green)" : "var(--primary-red)";
            document.getElementById('db-indicator-box').innerText = online ? "DATABASE: CLOUD ACTIVE" : "DATABASE: LOCAL AMAN";
            if(!online) {
                document.getElementById('dashboard-sync-desc').innerHTML = `<span style="color:var(--primary-red); font-weight:bold;">Offline Error: ${errorMsg}. Transaksi tetap aman berjalan lokal.</span>`;
            } else {
                document.getElementById('dashboard-sync-desc').innerText = "Koneksi stabil. Seluruh data transaksi dilaunching langsung ke Google Sheets.";
            }
        }

        function tarikDataGoogleSheets() {
            if(systemConfig.sheetUrl === "URL_DEPLOYMENT_ANDA_DI_SINI" || systemConfig.sheetUrl.trim() === "") {
                setStatusSistemJaringan(false, "Konfigurasi URL Webapp Kosong");
                return;
            }
            
            fetch(systemConfig.sheetUrl + "?action=read_produk")
            .then(res => res.json())
            .then(data => {
                if(data && data.length > 0) {
                    databaseItemPOS = data;
                    localStorage.setItem('hulo_db', JSON.stringify(databaseItemPOS));
                }
                kalkulasiRekomendasiHargaAI();
                renderProdukKeKasir();
                renderTableCRUD();
                setStatusSistemJaringan(true);
            })
            .catch(err => setStatusSistemJaringan(false, "Server Cloud Sibuk"));
        }

        function kalkulasiRekomendasiHargaAI() {
            databaseItemPOS.forEach(item => {
                // Formula AI: Mengkalkulasi Harga Berdasarkan HPP + Target Margin % lalu dibulatkan ke kelipatan Rp 500 terdekat
                item.hargaJual = Math.ceil((parseInt(item.hpp) * (1 + (parseFloat(item.margin) / 100))) / 500) * 500;
            });
        }

        function renderProdukKeKasir() {
            const grid = document.getElementById('productGrid');
            grid.innerHTML = '';
            databaseItemPOS.forEach(p => {
                let lowStockClass = p.stok <= 10 ? 'low-stock' : '';
                let badgeStock = p.stok <= 10 ? `<span class="badge badge-alarm">Sisa ${p.stok}</span>` : `<span style="font-size:11px; color:var(--text-muted);">Stok: ${p.stok}</span>`;
                
                grid.innerHTML += `
                    <div class="product-card ${lowStockClass}" onclick="tambahItemKeBill('${p.id}')">
                        <h3 style="margin:0 0 5px 0; font-size:14px;">${p.nama}</h3>
                        <div>
                            <div class="product-price">Rp ${p.hargaJual.toLocaleString('id-ID')}</div>
                            <div style="margin-top:8px;">${badgeStock}</div>
                        </div>
                    </div>
                `;
            });
        }

        function tambahItemKeBill(id) {
            const prod = databaseItemPOS.find(i => i.id === id);
            
            // Cek Batas Stok Maksimal
            const exist = keranjangSesi.find(i => i.id === id);
            if (exist) {
                if (exist.qty >= prod.stok) {
                    Swal.fire({ icon: 'warning', title: 'Stok Habis', text: 'Tidak bisa melebihi stok aktif!', background: '#161616', color: '#fff' });
                    return;
                }
                exist.qty++;
            } else {
                if (prod.stok <= 0) {
                    Swal.fire({ icon: 'warning', title: 'Stok Kosong', text: 'Menu ini memerlukan restock bahan!', background: '#161616', color: '#fff' });
                    return;
                }
                keranjangSesi.push({ id: prod.id, nama: prod.nama, hargaJual: prod.hargaJual, hpp: parseInt(prod.hpp), qty: 1 });
            }
            updateBillUI();
        }

        function ubahQtyItemBill(id, delta) {
            const exist = keranjangSesi.find(i => i.id === id);
            const prod = databaseItemPOS.find(i => i.id === id);
            if(exist) {
                exist.qty += delta;
                if(exist.qty > prod.stok) {
                    exist.qty = prod.stok;
                    Swal.fire({ icon: 'warning', title: 'Maksimal Stok', text: 'Batas kuantitas tercapai', background: '#161616', color: '#fff' });
                }
                if(exist.qty <= 0) {
                    keranjangSesi = keranjangSesi.filter(i => i.id !== id);
                }
            }
            updateBillUI();
        }

        function updateBillUI() {
            const box = document.getElementById('cartItems'); box.innerHTML = '';
            let subtotal = 0;
            
            if(keranjangSesi.length === 0) {
                box.innerHTML = '<p style="text-align:center; margin-top:40%; color:var(--text-muted);">Keranjang Belanja Kosong</p>';
                document.getElementById('txt-subtotal').innerText = "Rp 0";
                document.getElementById('txt-tax').innerText = "Rp 0";
                document.getElementById('txt-discount').innerText = "Rp 0";
                document.getElementById('txt-total').innerText = "Rp 0";
                return;
            }

            keranjangSesi.forEach(item => {
                subtotal += (item.hargaJual * item.qty);
                box.innerHTML += `
                    <div class="cart-item">
                        <div>
                            <div style="font-weight:600;">${item.nama}</div>
                            <div style="font-size:12px; color:var(--primary-red);">Rp ${item.hargaJual.toLocaleString('id-ID')}</div>
                        </div>
                        <div class="cart-item-actions">
                            <button class="btn-item-qty" onclick="ubahQtyItemBill('${item.id}', -1)">-</button>
                            <span style="min-width:20px; text-align:center; font-weight:bold;">${item.qty}</span>
                            <button class="btn-item-qty" onclick="ubahQtyItemBill('${item.id}', 1)">+</button>
                        </div>
                    </div>
                `;
            });

            // Marketing Promo Engine Calculation Logic
            const promoType = document.getElementById('promoSelect').value;
            let potonganDiskon = 0;

            if (promoType === "DISC10") potonganDiskon = subtotal * 0.10;
            else if (promoType === "DISC20") potonganDiskon = subtotal * 0.20;
            else if (promoType === "POT5K") potonganDiskon = 5000;
            else if (promoType === "POT10K") potonganDiskon = 10000;

            if(potonganDiskon > subtotal) potonganDiskon = subtotal; // Proteksi agar nilai tidak minus

            let pajak = (subtotal - potonganDiskon) * 0.10; 
            let totalAkhir = (subtotal - potonganDiskon) + pajak;

            document.getElementById('txt-subtotal').innerText = "Rp " + subtotal.toLocaleString('id-ID');
            document.getElementById('txt-tax').innerText = "Rp " + pajak.toLocaleString('id-ID');
            document.getElementById('txt-discount').innerText = "Rp " + potonganDiskon.toLocaleString('id-ID');
            document.getElementById('txt-total').innerText = "Rp " + totalAkhir.toLocaleString('id-ID');
        }

        function prosesTransaksiKasir() {
            if(keranjangSesi.length === 0) return;
            
            let subtotal = 0;
            let hppKumulatif = 0;
            
            keranjangSesi.forEach(i => {
                subtotal += (i.hargaJual * i.qty);
                hppKumulatif += (i.hpp * i.qty);
                let master = databaseItemPOS.find(m => m.id === i.id);
                master.stok -= i.qty;
            });

            const promoType = document.getElementById('promoSelect').value;
            let potonganDiskon = 0;
            if (promoType === "DISC10") potonganDiskon = subtotal * 0.10;
            else if (promoType === "DISC20") potonganDiskon = subtotal * 0.20;
            else if (promoType === "POT5K") potonganDiskon = 5000;
            else if (promoType === "POT10K") potonganDiskon = 10000;
            if(potonganDiskon > subtotal) potonganDiskon = subtotal;

            let tax = (subtotal - potonganDiskon) * 0.1; 
            let totalFinal = (subtotal - potonganDiskon) + tax;
            let netProfit = totalFinal - tax - hppKumulatif; // Rumus Laba Bersih: Total Bayar Bersih - Pajak - Modal Awal HPP

            const idTrx = "TRX-" + Date.now();
            const waktuSkrg = new Date().toLocaleString('id-ID');

            // Formulasi Data Payload untuk Google Sheets Cloud & Gateway Notifikasi
            const payload = {
                action: "simpan_transaksi",
                invoice: idTrx,
                kasir: userSesiAktif.nama,
                subtotal: subtotal,
                pajak: tax,
                diskon: potonganDiskon,
                total: totalFinal,
                profit: netProfit,
                hpp_total: hppKumulatif,
                items: keranjangSesi,
                sms_target: systemConfig.ownerWa,
                api_token: systemConfig.apiToken,
                outlet: systemConfig.outletName
            };

            // Kirim asynchronous data ke cloud webapp
            if(systemConfig.sheetUrl !== "URL_DEPLOYMENT_ANDA_DI_SINI") {
                fetch(systemConfig.sheetUrl, { method: 'POST', body: JSON.stringify(payload) })
                .then(() => { setStatusSistemJaringan(true); })
                .catch(() => { setStatusSistemJaringan(false, "Simpan Transaksi Offline Terproteksi"); });
            } else {
                setStatusSistemJaringan(false, "Aplikasi Berjalan Mode Tanpa Server");
            }

            Swal.fire({ 
                icon: 'success', 
                title: 'Transaksi Sukses', 
                text: 'Data Terkunci. Mengaktifkan Printer Termal...', 
                background: '#161616', 
                color: '#fff',
                timer: 1500,
                showConfirmButton: false
            });
            
            // Masukkan data transaksi ke Log Audit Lokal
            logTransaksiHariIni.push({ id: idTrx, waktu: waktuSkrg, kasir: userSesiAktif.nama, total: totalFinal, profit: netProfit, hpp: hppKumulatif, pajak: tax });
            
            // Update UI Sisi Kasir dan Tabel Laporan Finansial
            localStorage.setItem('hulo_db', JSON.stringify(databaseItemPOS));
            hitungUlangLaporanKeuangan();

            // Membangun Tampilan Nota Struk Printer Termal (58mm)
            document.getElementById('p-outlet-title').innerText = systemConfig.outletName;
            document.getElementById('p-date').innerText = waktuSkrg;
            document.getElementById('p-invoice').innerText = idTrx;
            
            const pItemsBox = document.getElementById('p-items'); pItemsBox.innerHTML = '';
            keranjangSesi.forEach(item => {
                pItemsBox.innerHTML += `
                    <div style="display:flex; justify-content:space-between; font-size:10px; margin-bottom:2px;">
                        <span>${item.nama} x${item.qty}</span>
                        <span>Rp ${(item.hargaJual*item.qty).toLocaleString('id-ID')}</span>
                    </div>`;
            });

            document.getElementById('p-subtotal').innerText = "Rp " + subtotal.toLocaleString('id-ID');
            document.getElementById('p-tax').innerText = "Rp " + tax.toLocaleString('id-ID');
            document.getElementById('p-disc-label').innerText = "Diskon (" + promoType + "):";
            document.getElementById('p-discount').innerText = "-Rp " + potonganDiskon.toLocaleString('id-ID');
            document.getElementById('p-grandtotal').innerText = "Rp " + totalFinal.toLocaleString('id-ID');
            
            // Eksekusi print langsung via hardware printer driver
            window.print();

            // Reset Sesi Keranjang Belanja
            keranjangSesi = []; 
            document.getElementById('promoSelect').value = "NONE";
            updateBillUI(); 
            renderProdukKeKasir(); 
            renderTableCRUD();
        }

        function renderTableCRUD() {
            const tbody = document.getElementById('crudTableBody'); tbody.innerHTML = '';
            databaseItemPOS.forEach(p => {
                tbody.innerHTML += `
                    <tr>
                        <td><b>${p.id}</b></td>
                        <td>${p.nama}</td>
                        <td><span style="color:var(--text-muted); font-size:12px;">${p.kategori}</span></td>
                        <td>Rp ${parseInt(p.hpp).toLocaleString('id-ID')}</td>
                        <td>${p.margin}%</td>
                        <td style="color:var(--primary-red); font-weight:bold;">Rp ${p.hargaJual.toLocaleString('id-ID')}</td>
                        <td><b style="color:${p.stok <= 10 ? 'var(--warning-orange)' : '#fff'}">${p.stok} unit</b></td>
                        <td>
                            <button style="background:#c0392b; border:none; color:#fff; padding:4px 8px; border-radius:4px; cursor:pointer; font-size:11px;" onclick="hapusProdukSistem('${p.id}')">Hapus</button>
                        </td>
                    </tr>`;
            });
        }

        function bukaModalTambahProduk() {
            Swal.fire({
                title: 'Tambah Menu Kustom Baru',
                background: 'var(--panel-bg)',
                color: '#fff',
                html:
                    '<input id="swal-input-id" class="swal2-input" placeholder="ID Menu (Contoh: HULO-04)" style="color:#fff; background:#222; font-size:14px;">' +
                    '<input id="swal-input-nama" class="swal2-input" placeholder="Nama Produk" style="color:#fff; background:#222; font-size:14px;">' +
                    '<input id="swal-input-kat" class="swal2-input" placeholder="Kategori (Coffee / Non-Coffee / Food)" style="color:#fff; background:#222; font-size:14px;">' +
                    '<input id="swal-input-hpp" type="number" class="swal2-input" placeholder="Nilai HPP Modal Asli (Rp)" style="color:#fff; background:#222; font-size:14px;">' +
                    '<input id="swal-input-margin" type="number" class="swal2-input" placeholder="Target Profit Margin (%)" style="color:#fff; background:#222; font-size:14px;">' +
                    '<input id="swal-input-stok" type="number" class="swal2-input" placeholder="Jumlah Stok Awal" style="color:#fff; background:#222; font-size:14px;">',
                focusConfirm: false,
                confirmButtonText: 'Kunci & Rekomendasi AI',
                confirmButtonColor: 'var(--success-green)',
                preConfirm: () => {
                    return {
                        id: document.getElementById('swal-input-id').value.trim(),
                        nama: document.getElementById('swal-input-nama').value.trim(),
                        kategori: document.getElementById('swal-input-kat').value.trim(),
                        hpp: parseInt(document.getElementById('swal-input-hpp').value),
                        margin: parseInt(document.getElementById('swal-input-margin').value),
                        stok: parseInt(document.getElementById('swal-input-stok').value)
                    }
                }
            }).then((result) => {
                if (result.isConfirmed) {
                    const data = result.value;
                    if(!data.id || !data.nama || isNaN(data.hpp)) {
                        Swal.fire({ icon: 'error', title: 'Gagal', text: 'Data tidak valid atau kolom kosong.' });
                        return;
                    }
                    databaseItemPOS.push(data);
                    localStorage.setItem('hulo_db', JSON.stringify(databaseItemPOS));
                    kalkulasiRekomendasiHargaAI();
                    renderProdukKeKasir();
                    renderTableCRUD();
                    Swal.fire({ icon: 'success', title: 'Tersimpan', text: 'Produk dihitung dengan AI kalkulator.', background: '#161616', color: '#fff' });
                }
            });
        }

        function hapusProdukSistem(id) {
            databaseItemPOS = databaseItemPOS.filter(i => i.id !== id);
            localStorage.setItem('hulo_db', JSON.stringify(databaseItemPOS));
            renderProdukKeKasir();
            renderTableCRUD();
            Swal.fire({ icon: 'success', title: 'Terhapus', text: 'Menu telah dibersihkan dari sistem.', background: '#161616', color: '#fff' });
        }

        function hitungUlangLaporanKeuangan() {
            let totalOmzet = 0;
            let totalHpp = 0;
            let totalPajak = 0;
            let totalProfit = 0;
            
            const tbody = document.getElementById('reportTableBody'); tbody.innerHTML = '';

            logTransaksiHariIni.forEach(t => {
                totalOmzet += t.total;
                totalHpp += t.hpp;
                totalPajak += t.pajak;
                totalProfit += t.profit;
                
                tbody.innerHTML += `
                    <tr>
                        <td><b>${t.id}</b></td>
                        <td>${t.waktu}</td>
                        <td>${t.kasir}</td>
                        <td style="color:var(--neon-blue); font-weight:bold;">Rp ${t.total.toLocaleString('id-ID')}</td>
                        <td style="color:var(--success-green); font-weight:bold;">Rp ${t.profit.toLocaleString('id-ID')}</td>
                    </tr>`;
            });

            document.getElementById('rep-omzet').innerText = "Rp " + totalOmzet.toLocaleString('id-ID');
            document.getElementById('rep-hpp').innerText = "Rp " + totalHpp.toLocaleString('id-ID');
            document.getElementById('rep-pajak').innerText = "Rp " + totalPajak.toLocaleString('id-ID');
            document.getElementById('rep-profit').innerText = "Rp " + totalProfit.toLocaleString('id-ID');
        }

        function simpanKonfigurasiGateway() {
            systemConfig.sheetUrl = document.getElementById('cfg-sheet-url').value.trim();
            systemConfig.ownerWa = document.getElementById('cfg-owner-wa').value.trim();
            systemConfig.apiToken = document.getElementById('cfg-api-token').value.trim();
            systemConfig.outletName = document.getElementById('cfg-outlet-name').value.trim();

            localStorage.setItem('hulo_sheetUrl', systemConfig.sheetUrl);
            localStorage.setItem('hulo_ownerWa', systemConfig.ownerWa);
            localStorage.setItem('hulo_apiToken', systemConfig.apiToken);
            localStorage.setItem('hulo_outletName', systemConfig.outletName);

            Swal.fire({ icon: 'success', title: 'Parameter Disimpan', text: 'Gerbang integrasi telah dikunci ulang.', background: '#161616', color: '#fff', confirmButtonColor: 'var(--success-green)' });
            tarikDataGoogleSheets();
        }

        function pindahTabSistem(viewId) {
            document.querySelectorAll('.content-view').forEach(view => view.classList.remove('active'));
            document.querySelectorAll('.menu-item').forEach(m => m.classList.remove('active'));
            document.getElementById(viewId).classList.add('active');
            
            // Sorot menu sidebar aktif
            if(viewId === 'pos-view') document.getElementById('nav-pos').classList.add('active');
            else if(viewId === 'crud-view') document.getElementById('nav-crud').classList.add('active');
            else if(viewId === 'report-view') document.getElementById('nav-report').classList.add('active');
            else if(viewId === 'config-view') document.getElementById('nav-config').classList.add('active');
        }

        function logoutSistem() { window.location.reload(); }

        // Jam Operasional Sistem Realtime
        setInterval(() => {
            document.getElementById('liveClock').innerText = "WAKTU OPERASIONAL POS: " + new Date().toLocaleString('id-ID');
        }, 1000);
    </script>
</body>
</html>
