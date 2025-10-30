# Huncsik-TEch
#Hello im a 14y old hungary kid ho coding
<!DOCTYPE html>
<html lang="hu">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Milan project 2 🌤️</title>

    <style>
        /* ----- ALAP OLDAL ----- */
        
        body {
            font-family: "Poppins", Arial, sans-serif;
            background: linear-gradient(135deg, #74b9ff, #a29bfe);
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0;
        }
        /* ----- KÁRTYA ----- */
        
        .card {
            background: rgba(255, 255, 255, 0.9);
            padding: 30px;
            border-radius: 16px;
            box-shadow: 0 8px 25px rgba(0, 0, 0, 0.2);
            width: 340px;
            text-align: center;
            transition: transform 0.3s ease, box-shadow 0.3s ease;
        }
        
        .card:hover {
            transform: translateY(-5px);
            box-shadow: 0 12px 35px rgba(0, 0, 0, 0.25);
        }
        /* ----- INPUT ÉS GOMB ----- */
        
        input {
            width: 80%;
            padding: 10px;
            margin-top: 12px;
            border-radius: 8px;
            border: 1px solid #ccc;
            font-size: 16px;
            outline: none;
            transition: border-color 0.3s;
        }
        
        input:focus {
            border-color: #0984e3;
        }
        
        button {
            margin-top: 15px;
            background-color: #0984e3;
            border: none;
            color: white;
            padding: 10px 18px;
            font-size: 16px;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s, transform 0.1s;
        }
        
        button:hover {
            background-color: #74b9ff;
        }
        
        button:active {
            transform: scale(0.97);
        }
        /* ----- KIÍRÁS ----- */
        
        .result {
            margin-top: 20px;
            padding: 15px;
            background: #dfe6e9;
            border-radius: 10px;
            font-size: 15px;
        }
        
        .error {
            color: #d63031;
            margin-top: 10px;
        }
        
        .loading {
            margin-top: 10px;
            font-style: italic;
            color: #636e72;
            animation: blink 1.2s infinite;
        }
        
        @keyframes blink {
            0%,
            100% {
                opacity: 0.2;
            }
            50% {
                opacity: 1;
            }
        }
    </style>
</head>

<body>
    <div class="card">
        <h1>🌤️ Időjárás</h1>
        <p>Írd be a várost, és mutatom az adatokat!</p>

        <input id="city" type="search" placeholder="pl. Zedország
		">
        <br>
        <button id="searchBtn">Lekérdez</button>

        <div id="loading" class="loading" style="display: none;">⏳ Kérem várj...</div>
        <div id="error" class="error"></div>
        <div id="output"></div>
    </div>

    <script>
        const btn = document.getElementById('searchBtn');
        const cityInput = document.getElementById('city');
        const output = document.getElementById('output');
        const errorBox = document.getElementById('error');
        const loading = document.getElementById('loading');

        async function lookupWeather(city) {
            if (!city) {
                errorBox.textContent = "❗ Kérlek, írj be egy városnevet.";
                return;
            }

            output.innerHTML = "";
            errorBox.textContent = "";
            loading.style.display = "block";

            try {
                const geoUrl = `https://geocoding-api.open-meteo.com/v1/search?name=${encodeURIComponent(city)}&count=1&language=hu`;
                const geoResp = await fetch(geoUrl);
                const geo = await geoResp.json();

                if (!geo.results || geo.results.length === 0) {
                    throw new Error("Nem található ilyen város.");
                }

                const lat = geo.results[0].latitude;
                const lon = geo.results[0].longitude;
                const place = geo.results[0].name;

                const weatherUrl = `https://api.open-meteo.com/v1/forecast?latitude=${lat}&longitude=${lon}&current_weather=true&temperature_unit=celsius&windspeed_unit=kmh`;
                const weatherResp = await fetch(weatherUrl);
                const weather = await weatherResp.json();

                const cw = weather.current_weather;

                output.innerHTML = `
          <div class="result">
            🌍 <strong>${place}</strong><br>
            🌡️ Hőmérséklet: <strong>${cw.temperature} °C</strong><br>
            💨 Szél: <strong>${cw.windspeed} km/h</strong><br>
            🕒 Időpont: ${new Date(cw.time).toLocaleTimeString('hu-HU')}
          </div>
        `;
            } catch (err) {
                errorBox.textContent = "⚠️ " + err.message;
            } finally {
                loading.style.display = "none";
            }
        }

        btn.addEventListener('click', () => lookupWeather(cityInput.value));
        cityInput.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') lookupWeather(cityInput.value);
        });
    </script>
</body>

</html>
