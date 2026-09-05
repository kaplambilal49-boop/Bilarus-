</html>
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Sarkastik Canlı Hava Durumu Platformu</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        body, html {
            height: 100%;
            width: 100%;
            overflow: hidden;
            background-color: #0b0e14;
            color: #fff;
        }

        /* Arka Plan Görseli */
        #bg-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
            transition: background-image 0.7s ease-in-out;
            z-index: 1;
        }

        /* Koyu Filtre */
        .dark-shield {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: linear-gradient(180deg, rgba(0,0,0,0.75) 0%, rgba(0,0,0,0.4) 50%, rgba(0,0,0,0.88) 100%);
            z-index: 2;
        }

        /* Ana Kapsayıcı */
        .app-container {
            position: relative;
            z-index: 3;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            align-items: center;
            padding: 20px 18px 30px 18px;
            text-align: center;
        }

        /* Arama Alanı */
        .search-box {
            display: flex;
            width: 100%;
            max-width: 420px;
            gap: 8px;
            margin-top: 5px;
        }

        .search-input {
            flex: 1;
            padding: 13px 18px;
            border-radius: 25px;
            border: 1px solid rgba(255, 255, 255, 0.3);
            background: rgba(0, 0, 0, 0.65);
            color: #fff;
            font-size: 0.95rem;
            outline: none;
            backdrop-filter: blur(10px);
        }

        .search-input::placeholder {
            color: rgba(255, 255, 255, 0.6);
        }

        .search-btn {
            padding: 13px 22px;
            border-radius: 25px;
            border: none;
            background: linear-gradient(135deg, #ff3b30, #ff2d55);
            color: #fff;
            font-weight: 800;
            cursor: pointer;
            box-shadow: 0 4px 15px rgba(255, 59, 48, 0.4);
        }

        /* Canlı Hava Durumu Göstergesi */
        .header {
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
        }

        .city-name {
            font-size: 1.6rem;
            font-weight: 800;
            letter-spacing: 1.5px;
            text-transform: uppercase;
            text-shadow: 0 3px 12px rgba(0,0,0,0.9);
            color: #ffffff;
        }

        .temp-display {
            font-size: 5.2rem;
            font-weight: 900;
            margin-top: -5px;
            text-shadow: 0 4px 20px rgba(0,0,0,0.9);
        }

        .weather-desc {
            font-size: 1.2rem;
            font-weight: 700;
            letter-spacing: 1px;
            color: #ffd60a;
            text-transform: uppercase;
            text-shadow: 0 2px 10px rgba(0,0,0,0.95);
        }

        /* Profesyonel Hava Detayları Kartı */
        .weather-stats {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 10px;
            width: 100%;
            max-width: 420px;
            margin-top: 15px;
        }

        .stat-item {
            background: rgba(0, 0, 0, 0.55);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.15);
            padding: 10px 12px;
            border-radius: 16px;
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .stat-label {
            font-size: 0.75rem;
            color: rgba(255, 255, 255, 0.65);
            text-transform: uppercase;
            font-weight: 600;
        }

        .stat-value {
            font-size: 1.05rem;
            font-weight: 800;
            color: #fff;
            margin-top: 2px;
        }

        /* Sarkastik Yorum Kartı */
        .quote-card {
            background: rgba(15, 15, 15, 0.85);
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border: 1.5px solid rgba(255, 255, 255, 0.2);
            padding: 22px 18px;
            border-radius: 22px;
            width: 100%;
            max-width: 420px;
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.8);
        }

        .quote-title {
            font-size: 0.75rem;
            color: #ff3b30;
            font-weight: 800;
            letter-spacing: 1.5px;
            text-transform: uppercase;
            margin-bottom: 8px;
        }

        .quote-text {
            font-size: 1.15rem;
            font-weight: 600;
            line-height: 1.45;
            color: #ffffff;
        }
    </style>
</head>
<body>

    <div id="bg-overlay"></div>
    <div class="dark-shield"></div>

    <div class="app-container">
        <!-- Arama Alanı -->
        <div class="search-box">
            <input type="text" id="locationInput" class="search-input" placeholder="İl, ilçe veya köy adı yaz...">
            <button class="search-btn" onclick="manuelAra()">ARA</button>
        </div>

        <div class="header">
            <div class="city-name" id="city">YÜKLENİYOR...</div>
            <div class="temp-display" id="temp">--°C</div>
            <div class="weather-desc" id="condition">--</div>

            <!-- Canlı İstatistikler -->
            <div class="weather-stats">
                <div class="stat-item">
                    <span class="stat-label">Hissedilen</span>
                    <span class="stat-value" id="feelsLike">--°C</span>
                </div>
                <div class="stat-item">
                    <span class="stat-label">Nem Oranı</span>
                    <span class="stat-value" id="humidity">%--</span>
                </div>
                <div class="stat-item">
                    <span class="stat-label">Rüzgar Hızı</span>
                    <span class="stat-value" id="wind">-- km/s</span>
                </div>
                <div class="stat-item">
                    <span class="stat-label">Yağış Olasılığı</span>
                    <span class="stat-value" id="precip">%--</span>
                </div>
            </div>
        </div>

        <!-- Otomatik Sarkastik Yorum Kartı -->
        <div class="quote-card">
            <div class="quote-title">🤖 SARKASTİK YORUM</div>
            <div class="quote-text" id="quote">Canlı hava verisi taranıyor...</div>
        </div>
    </div>

    <script>
        // Yüksek Çözünürlüklü Arka Plan Temaları
        const temalar = {
            gunesli: "https://images.unsplash.com/photo-1507525428034-b723cf961d3e?auto=format&fit=crop&w=1200&q=80",
            bulutlu: "https://images.unsplash.com/photo-1534088568595-a066f410bcda?auto=format&fit=crop&w=1200&q=80",
            yagmur: "https://images.unsplash.com/photo-1519692933481-e162a57d6721?auto=format&fit=crop&w=1200&q=80",
            soguk: "https://images.unsplash.com/photo-1486496146582-9ffcd0b2b2b7?auto=format&fit=crop&w=1200&q=80",
            kar: "https://images.unsplash.com/photo-1517299321529-b98d2b96316a?auto=format&fit=crop&w=1200&q=80"
        };

        // Kategoriye Göre Sarkastik Laf Havuzu
        const laflar = {
            gunesli: [
                "Güneş tepede parıldıyor, sen hala odanda ekran ışığıyla yaşlanıyorsun.",
                "Dışarısı yanıyor, sen hala ekrana bakıyorsun. Git biraz AVM kliması emil.",
                "Perdeyi açsaydın görürdün, güneş tepede evde çürüyorsun.",
                "Güneş kremim yok deme, bu kafayla dışarı çıksan zaten yanarsın."
            ],
            bulutlu: [
                "Hava kapalı, tıpkı geleceğin gibi. Ne yağacağı belli ne açacağı.",
                "Bulutlar bile senden daha enerjik duruyor bugün.",
                "Güneş arkaya saklanmış, sen de git battaniyenin altına saklan.",
                "Kasvetli hava tam senin modun. Hiç bozma evde otur."
            ],
            yagmur: [
                "Dışarıda yağmur var. Şemsiyesiz çık da ıslak kediye dön gör gününü.",
                "Camı açıp bakmak yerine uygulamaya girmen tam sana yakışır bir üşengeçlik.",
                "Yağmur yağıyor işte, romantik takılacaksan çık dışarı ıslan.",
                "Silecekler bile senden daha verimli çalışıyor şu an."
            ],
            soguk: [
                "Buz gibi hava var. Giyinmeyi biliyorsan kat kat giyin, bilmiyorsan don.",
                "Dışarısı ayaz, çıkarsan burnun düşer benden söylemesi.",
                "Rüzgarda uçan kartal gibisin, bir dışarı çıksan savrulup gideceksin.",
                "Evde oturup çay içmek varken havaya bakma derdin ne?"
            ],
            kar: [
                "Lapa lapa kar yağıyor, kartopu oynayacak arkadaşın da yoktur şimdi senin.",
                "Karda kayak yapanlara bakıp imren, sen ancak evde battaniye altında oturursun.",
                "Buz tutmuş yollar. Dışarı çıkıp iki adımda kayıp düşmek istiyorsan buyur çık.",
                "Dışarısı kartpostal gibi ama sen hala bu ekrana bakıyorsun."
            ]
        };

        let mevcutKategori = "gunesli";

        // Open-Meteo Weathercode -> Türkçe
        function havaninDurumuTurkce(code) {
            if (code === 0) return "AÇIK / GÜNEŞLİ";
            if (code >= 1 && code <= 3) return "PARÇALI BULUTLU";
            if (code >= 45 && code <= 48) return "SİSLİ VE SOĞUK";
            if (code >= 51 && code <= 67) return "SAĞANAK YAĞMURLU";
            if (code >= 71 && code <= 77) return "LAPA LAPA KARLI";
            if (code >= 80 && code <= 82) return "ŞİDDETLİ YAĞMUR";
            if (code >= 85 && code <= 86) return "YOĞUN KAR YAĞIŞLI";
            return "BULUTLU / RÜZGARLI";
        }

        // Manuel Arama
        async function manuelAra() {
            const input = document.getElementById("locationInput").value.trim();
            if (input !== "") {
                document.getElementById("city").innerText = "ARANIYOR...";
                await konumBulVeYukle(input);
            }
        }

        // Enter Tuşu Desteği
        document.getElementById("locationInput")?.addEventListener("keypress", function (e) {
            if (e.key === 'Enter') {
                manuelAra();
            }
        });

        // Hassas Arama (Geocoding API)
        async function konumBulVeYukle(yerAdi) {
            try {
                const res = await fetch(`https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(yerAdi)}&count=5&language=tr&format=json`);
                const data = await res.json();
                
                if (data.results && data.results.length > 0) {
                    const trSonuc = data.results.find(r => r.country_code === "TR") || data.results[0];
                    const gosterilecekIsim = trSonuc.admin1 ? `${trSonuc.name}, ${trSonuc.admin1}` : trSonuc.name;
                    await canlıHavaVerisiYukle(trSonuc.latitude, trSonuc.longitude, gosterilecekIsim);
                } else {
                    document.getElementById("city").innerText = "YER BULUNAMADI";
                }
            } catch(e) {
                document.getElementById("city").innerText = "HATA OLUŞTU";
            }
        }

        // Canlı Gerçek Hava Verisini Çekme
        async function canlıHavaVerisiYukle(lat, lon, yerIsmi) {
            try {
                const url = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current=temperature_2m,relative_humidity_2m,apparent_temperature,precipitation_probability,weather_code,wind_speed_10m&wind_speed_unit=kmh&timezone=auto`;
                const res = await fetch(url);
                const data = await res.json();

                const c = data.current;
                const temp = Math.round(c.temperature_2m);
                const feels = Math.round(c.apparent_temperature);
                const humidity = c.relative_humidity_2m;
                const wind = Math.round(c.wind_speed_10m);
                const precip = c.precipitation_probability !== undefined ? c.precipitation_probability : 0;
                const code = c.weather_code;

                // Ekranı Güncelle
                document.getElementById("city").innerText = yerIsmi.toUpperCase();
                document.getElementById("temp").innerText = `${temp}°C`;
                document.getElementById("condition").innerText = havaninDurumuTurkce(code);
                
                document.getElementById("feelsLike").innerText = `${feels}°C`;
                document.getElementById("humidity").innerText = `%${humidity}`;
                document.getElementById("wind").innerText = `${wind} km/s`;
                document.getElementById("precip").innerText = `%${precip}`;

                // Hava Durumuna Göre Tema Kategori Tespiti
                if ((code >= 51 && code <= 67) || (code >= 80 && code <= 82)) {
                    mevcutKategori = "yagmur";
                } else if (code >= 71) {
                    mevcutKategori = "kar";
                } else if (code >= 1 && code <= 3) {
                    mevcutKategori = "bulutlu";
                } else if (temp < 10) {
                    mevcutKategori = "soguk";
                } else {
                    mevcutKategori = "gunesli";
                }

                // Arka Planı Değiştir ve Otomatik Sarkastik Laf Bas
                document.getElementById("bg-overlay").style.backgroundImage = `url('${temalar[mevcutKategori]}')`;
                otomatikLafSok();

            } catch(e) {
                document.getElementById("city").innerText = "VERİ ALINAMADI";
            }
        }

        // Otomatik Sarkastik Laf Üretici
        function otomatikLafSok() {
            const liste = laflar[mevcutKategori];
            const rastgele = Math.floor(Math.random() * liste.length);
            document.getElementById("quote").innerText = liste[rastgele];
        }

        // Otomatik Cihaz Konumu Veya Varsayılan Şehir
        function baslat() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(
                    async (pos) => {
                        await canlıHavaVerisiYukle(pos.coords.latitude, pos.coords.longitude, "SENİN KONUMUN");
                    },
                    async () => {
                        await konumBulVeYukle("İstanbul");
                    }
                );
            } else {
                konumBulVeYukle("İstanbul");
            }
        }

        baslat();
    </script>
</body>
</html>
