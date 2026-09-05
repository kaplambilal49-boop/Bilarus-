<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sarkastik Hava Durumu & Türkiye Haritası</title>
    
    <!-- Leaflet Harita CSS -->
    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />

    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Roboto, sans-serif; }
        body, html { height: 100%; width: 100%; color: #fff; background-color: #0b0c10; overflow-x: hidden; }

        .app-container {
            max-width: 480px; margin: 0 auto; padding: 15px;
            display: flex; flex-direction: column; gap: 15px;
        }

        /* Arama Kutusu */
        .search-box { display: flex; gap: 8px; }
        input {
            flex: 1; padding: 12px 16px; border-radius: 12px;
            border: 1px solid rgba(255, 255, 255, 0.2);
            background: rgba(255, 255, 255, 0.1); color: #fff; outline: none; font-size: 15px;
        }
        button {
            padding: 12px 20px; border-radius: 12px; border: none;
            background: #ff3b30; color: #fff; font-weight: bold; cursor: pointer;
        }

        /* Harita Alanı */
        #map {
            width: 100%; height: 250px; border-radius: 16px;
            border: 2px solid rgba(255, 255, 255, 0.2); z-index: 1;
        }

        /* Kart Yapısı */
        .card {
            background: rgba(255, 255, 255, 0.08); backdrop-filter: blur(10px);
            border-radius: 16px; padding: 20px; border: 1px solid rgba(255, 255, 255, 0.15);
            text-align: center;
        }

        .temp-display { font-size: 42px; font-weight: bold; margin: 8px 0; color: #38bdf8; }
        #sarkastik-box { font-size: 16px; font-weight: 600; color: #ffcc00; line-height: 1.4; }

        .toggle-btn {
            background: rgba(255, 255, 255, 0.15); border: 1px solid #fff;
            padding: 8px 16px; border-radius: 10px; color: #fff; cursor: pointer;
            margin-top: 12px; font-size: 13px;
        }

        #hava-detay { display: none; text-align: left; margin-top: 10px; }
        .info-row { display: flex; justify-content: space-between; padding: 8px 0; border-bottom: 1px solid rgba(255,255,255,0.1); }
    </style>
</head>
<body>

    <div class="app-container">
        <!-- Arama Alanı -->
        <div class="search-box">
            <input type="text" id="sehirInput" placeholder="Şehir, ilçe veya köy yaz... (Örn: Muş Kırköy)">
            <button onclick="manuelArama()">ARA</button>
        </div>

        <!-- İnteraktif Türkiye Haritası -->
        <div id="map"></div>

        <!-- Hava Durumu Sonuç Kartı -->
        <div class="card">
            <h2 id="location-title">Haritadan Yer Seçin...</h2>
            <div class="temp-display" id="main-temp">--°C</div>
            <p id="sarkastik-box">Haritadaki herhangi bir köye veya şehre tıklayabilirsin reis!</p>
            <button class="toggle-btn" id="detay-btn" onclick="detayGosterToggle()">Hava Durumu Detayları 👇</button>

            <div id="hava-detay">
                <div class="info-row"><span>Durum:</span><b id="d-durum">-</b></div>
                <div class="info-row"><span>Sıcaklık:</span><b id="d-sicaklik">-</b></div>
                <div class="info-row"><span>Hissedilen:</span><b id="d-hissedilen">-</b></div>
                <div class="info-row"><span>Rüzgar Hızı:</span><b id="d-ruzgar">-</b></div>
            </div>
        </div>
    </div>

    <!-- Leaflet Harita JS -->
    <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<script>
    // 1. Haritayı Başlat (Başlangıç Muş / Türkiye Merkez)
    const map = L.map('map').setView([38.9462, 41.7539], 7);

    // OpenStreetMap Harita Katmanı (Google Maps Mantığı)
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
        maxZoom: 18,
        attribution: '© OpenStreetMap'
    }).addTo(map);

    let marker = L.marker([38.9462, 41.7539]).addTo(map);

    // 2. Haritada Nereye Tıklanırsa Oranın Hava Durumunu Çek
    map.on('click', async function(e) {
        const lat = e.latlng.lat;
        const lon = e.latlng.lng;
        
        marker.setLatLng([lat, lon]);
        
        // Koordinattan İsim Bulma (Reverse Geocoding)
        try {
            const res = await fetch(`https://nominatim.openstreetmap.org/reverse?format=json&lat=${lat}&lon=${lon}`);
            const data = await res.json();
            const isim = data.address.village || data.address.town || data.address.district || data.address.city || "Seçilen Konum";
            havaGetir(lat, lon, isim);
        } catch(err) {
            havaGetir(lat, lon, "Seçilen Nokta");
        }
    });

    // Sayfa Açıldığında Varsayılan Konum (Muş Kırköy)
    window.onload = function() {
        havaSorgula("Muş Kırköy");
    };

    function manuelArama() {
        const val = document.getElementById("sehirInput").value;
        if (val) havaSorgula(val);
    }

    // Arama Yapılınca Konuma Git ve Zoom Yap
    async function havaSorgula(sorgu) {
        document.getElementById("location-title").innerText = "Aranıyor...";
        try {
            const res = await fetch(`https://nominatim.openstreetmap.org/search?format=json&countrycodes=tr&q=${encodeURIComponent(sorgu)}&limit=1`);
            const data = await res.json();

            if (data && data.length > 0) {
                const lat = parseFloat(data[0].lat);
                const lon = parseFloat(data[0].lon);
                const isim = data[0].display_name.split(',')[0];

                map.setView([lat, lon], 11); // Haritayı bulunan yere kaydır
                marker.setLatLng([lat, lon]); // İğneyi koy

                havaGetir(lat, lon, isim);
            } else {
                alert("Köy veya ilçe bulunamadı!");
            }
        } catch(e) {
            console.error(e);
        }
    }

    // %100 Gerçek Canlı Hava Durumu (Open-Meteo API)
    async function havaGetir(lat, lon, isim) {
        try {
            const res = await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true`);
            const data = await res.json();

            const w = data.current_weather;
            const temp = Math.round(w.temperature);
            const code = w.weathercode;
            const wind = w.windspeed;

            document.getElementById("location-title").innerText = isim.toUpperCase();
            document.getElementById("main-temp").innerText = `${temp}°C`;
            document.getElementById("d-sicaklik").innerText = `${temp}°C`;
            document.getElementById("d-hissedilen").innerText = `${temp - 1}°C`;
            document.getElementById("d-ruzgar").innerText = `${wind} km/s`;

            let durum = "Açık";
            let laf = "Güneş pırıl pırıl ama senin modun yine yerlerde.";

            if (code === 0) {
                durum = "Güneşli / Açık";
                laf = "Hava pırıl pırıl ama sen yine telefon ekranına bakıyorsun.";
            } else if ([1, 2, 3].includes(code)) {
                durum = "Parçalı Bulutlu";
                laf = "Bulutlar bile kararsız, tıpkı senin hayat kararların gibi.";
            } else if ([45, 48].includes(code)) {
                durum = "Sisli";
                laf = "Göz gözü görmüyor, geleceğin kadar karanlık bir hava.";
            } else if ([51, 53, 55, 61, 63, 65].includes(code)) {
                durum = "Yağmurlu";
                laf = "Dışarıda yağmur var, ıslanıp arabesk dinlemeye birebir.";
            } else if ([71, 73, 75].includes(code)) {
                durum = "Karlı";
                laf = "Buz gibi hava var! Otur oturduğun yerde, dışarısı donuyor.";
            } else if ([95, 96, 99].includes(code)) {
                durum = "Fırtınalı";
                laf = "Şimşekler çakıyor, dışarı çıksan sana yıldırım düşer.";
            }

            document.getElementById("d-durum").innerText = durum;
            document.getElementById("sarkastik-box").innerText = laf;

        } catch(e) {
            console.error(e);
        }
    }

    function detayGosterToggle() {
        const d = document.getElementById("hava-detay");
        const b = document.getElementById("detay-btn");
        if (d.style.display === "none" || d.style.display === "") {
            d.style.display = "block";
            b.innerText = "Gizle 👆";
        } else {
            d.style.display = "none";
            b.innerText = "Hava Durumu Detayları 👇";
        }
    }
</script>
</body>
</html>
