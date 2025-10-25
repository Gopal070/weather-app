# weather-app
This Weather App provides real-time weather information for any city using the weatherstack API. It displays temperature, humidity, wind speed, and weather conditions with a clean and user-friendly interface. Built using Python and requests library, this project demonstrates working with REST APIs and JSON data handling.
[whetherapp1.html](https://github.com/user-attachments/files/23142132/whetherapp1.html)
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App - Gopal Singh</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        
        .container {
            width: 100%;
            max-width: 500px;
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            overflow: hidden;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .header {
            background: rgba(0, 0, 0, 0.2);
            padding: 20px;
            text-align: center;
            color: white;
        }
        
        .header h1 {
            font-size: 28px;
            margin-bottom: 5px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }
        
        .header p {
            font-size: 14px;
            opacity: 0.8;
        }
        
        .search-container {
            padding: 20px;
            display: flex;
            gap: 10px;
        }
        
        .search-container input {
            flex: 1;
            padding: 12px 20px;
            border: none;
            border-radius: 50px;
            background: rgba(255, 255, 255, 0.9);
            font-size: 16px;
            outline: none;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        }
        
        .search-container button {
            padding: 12px 25px;
            border: none;
            border-radius: 50px;
            background: #ff7e5f;
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }
        
        .search-container button:hover {
            background: #ff9a8b;
            transform: translateY(-2px);
        }
        
        .weather-info {
            padding: 20px;
            color: white;
            text-align: center;
        }
        
        .weather-icon {
            width: 120px;
            height: 120px;
            margin: 0 auto 15px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 60px;
        }
        
        .temperature {
            font-size: 48px;
            font-weight: bold;
            margin-bottom: 10px;
        }
        
        .description {
            font-size: 22px;
            margin-bottom: 20px;
            text-transform: capitalize;
        }
        
        .details {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 20px;
        }
        
        .detail-card {
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            padding: 15px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 8px;
            transition: transform 0.3s ease;
        }
        
        .detail-card:hover {
            transform: translateY(-5px);
            background: rgba(255, 255, 255, 0.15);
        }
        
        .detail-card i {
            font-size: 24px;
            color: #ffcc5c;
        }
        
        .detail-card .label {
            font-size: 14px;
            opacity: 0.8;
        }
        
        .detail-card .value {
            font-size: 18px;
            font-weight: bold;
        }
        
        .loading {
            display: none;
            text-align: center;
            padding: 20px;
            color: white;
        }
        
        .loading i {
            font-size: 30px;
            animation: spin 1s linear infinite;
        }
        
        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }
        
        .error {
            display: none;
            text-align: center;
            padding: 20px;
            color: #ff6b6b;
            background: rgba(255, 107, 107, 0.1);
            margin: 20px;
            border-radius: 10px;
        }
        
        .footer {
            text-align: center;
            padding: 15px;
            color: rgba(255, 255, 255, 0.6);
            font-size: 12px;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        @media (max-width: 500px) {
            .container {
                border-radius: 15px;
            }
            
            .header h1 {
                font-size: 24px;
            }
            
            .temperature {
                font-size: 40px;
            }
            
            .details {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1><i class="fas fa-cloud-sun"></i> Gopal's Weather App</h1>
            <p>Get real-time weather information for any city</p>
        </div>
        
        <div class="search-container">
            <input type="text" id="cityInput" placeholder="Enter city name...">
            <button onclick="getWeatherData()">Search <i class="fas fa-search"></i></button>
        </div>
        
        <div class="loading" id="loading">
            <i class="fas fa-spinner"></i>
            <p>Loading weather data...</p>
        </div>
        
        <div class="error" id="error">
            <i class="fas fa-exclamation-triangle"></i>
            <p id="errorMessage">City not found. Please try again.</p>
        </div>
        
        <div class="weather-info" id="weatherInfo">
            <div class="weather-icon">
                <i class="fas fa-sun" id="weatherIcon"></i>
            </div>
            <div class="temperature" id="temperature">--°C</div>
            <div class="description" id="description">--</div>
            
            <div class="details">
                <div class="detail-card">
                    <i class="fas fa-temperature-low"></i>
                    <div class="label">Feels Like</div>
                    <div class="value" id="feelsLike">--°C</div>
                </div>
                <div class="detail-card">
                    <i class="fas fa-wind"></i>
                    <div class="label">Wind Speed</div>
                    <div class="value" id="windSpeed">-- km/h</div>
                </div>
                <div class="detail-card">
                    <i class="fas fa-compass"></i>
                    <div class="label">Wind Direction</div>
                    <div class="value" id="windDirection">--</div>
                </div>
                <div class="detail-card">
                    <i class="fas fa-map-marker-alt"></i>
                    <div class="label">Location</div>
                    <div class="value" id="location">--</div>
                </div>
            </div>
        </div>
        
        <div class="footer">
            <p>Created by Gopal Singh | Weather data provided by Weatherstack</p>
        </div>
    </div>

    <script>
        const cityInput = document.getElementById('cityInput');
        const weatherInfo = document.getElementById('weatherInfo');
        const loading = document.getElementById('loading');
        const error = document.getElementById('error');
        const errorMessage = document.getElementById('errorMessage');
        
        // Elements to update
        const temperature = document.getElementById('temperature');
        const feelsLike = document.getElementById('feelsLike');
        const windSpeed = document.getElementById('windSpeed');
        const windDirection = document.getElementById('windDirection');
        const description = document.getElementById('description');
        const weatherIcon = document.getElementById('weatherIcon');
        const locationElement = document.getElementById('location');
        
        // Allow Enter key to trigger search
        cityInput.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                getWeatherData();
            }
        });
        
        async function getWeatherData() {
            const city = cityInput.value.trim();
            
            if (!city) {
                showError("Please enter a city name");
                return;
            }
            
            // Show loading, hide other sections
            loading.style.display = 'block';
            weatherInfo.style.display = 'none';
            error.style.display = 'none';
            
            try {
                const apiKey = "8475e560f4782349c431c9d80bf43f0b";
                const apiUrl = `http://api.weatherstack.com/current?access_key=${apiKey}&query=${city}`;
                
                const response = await fetch(apiUrl);
                const data = await response.json();
                
                if (data.success === false) {
                    throw new Error(data.error.info || "City not found");
                }
                
                // Update UI with weather data
                updateWeatherUI(data);
                
                // Show weather info, hide loading
                weatherInfo.style.display = 'block';
                loading.style.display = 'none';
                
            } catch (err) {
                showError(err.message);
                loading.style.display = 'none';
            }
        }
        
        function updateWeatherUI(data) {
            const current = data.current;
            const location = data.location;
            
            temperature.textContent = `${current.temperature}°C`;
            feelsLike.textContent = `${current.feelslike}°C`;
            windSpeed.textContent = `${current.wind_speed} km/h`;
            windDirection.textContent = current.wind_dir;
            description.textContent = current.weather_descriptions[0];
            locationElement.textContent = `${location.name}, ${location.country}`;
            
            // Update weather icon based on weather code
            updateWeatherIcon(current.weather_code);
        }
        
        function updateWeatherIcon(weatherCode) {
            // Map weather codes to Font Awesome icons
            const iconMap = {
                '113': 'fas fa-sun', // Sunny
                '116': 'fas fa-cloud-sun', // Partly cloudy
                '119': 'fas fa-cloud', // Cloudy
                '122': 'fas fa-cloud', // Overcast
                '143': 'fas fa-smog', // Mist
                '176': 'fas fa-cloud-rain', // Patchy rain
                '179': 'fas fa-cloud-snow', // Patchy snow
                '182': 'fas fa-cloud-meatball', // Patchy sleet
                '185': 'fas fa-cloud-rain', // Patchy freezing drizzle
                '200': 'fas fa-bolt', // Thundery outbreaks
                '227': 'fas fa-wind', // Blowing snow
                '230': 'fas fa-snowflake', // Blizzard
                '248': 'fas fa-smog', // Fog
                '260': 'fas fa-smog', // Freezing fog
                '263': 'fas fa-cloud-rain', // Patchy light drizzle
                '266': 'fas fa-cloud-rain', // Light drizzle
                '281': 'fas fa-cloud-rain', // Freezing drizzle
                '284': 'fas fa-cloud-rain', // Heavy freezing drizzle
                '293': 'fas fa-cloud-rain', // Patchy light rain
                '296': 'fas fa-cloud-rain', // Light rain
                '299': 'fas fa-cloud-rain', // Moderate rain
                '302': 'fas fa-cloud-rain', // Heavy rain
                '305': 'fas fa-cloud-rain', // Light freezing rain
                '308': 'fas fa-cloud-rain', // Heavy freezing rain
                '311': 'fas fa-cloud-rain', // Light sleet
                '314': 'fas fa-cloud-rain', // Moderate/heavy sleet
                '317': 'fas fa-cloud-rain', // Light snow
                '320': 'fas fa-snowflake', // Moderate/heavy snow
                '323': 'fas fa-snowflake', // Patchy light snow
                '326': 'fas fa-snowflake', // Light snow
                '329': 'fas fa-snowflake', // Patchy moderate snow
                '332': 'fas fa-snowflake', // Moderate snow
                '335': 'fas fa-snowflake', // Patchy heavy snow
                '338': 'fas fa-snowflake', // Heavy snow
                '350': 'fas fa-cloud-meatball', // Ice pellets
                '353': 'fas fa-cloud-rain', // Light rain shower
                '356': 'fas fa-cloud-rain', // Moderate/heavy rain shower
                '359': 'fas fa-cloud-rain', // Torrential rain shower
                '362': 'fas fa-cloud-meatball', // Light sleet showers
                '365': 'fas fa-cloud-meatball', // Moderate/heavy sleet showers
                '368': 'fas fa-snowflake', // Light snow showers
                '371': 'fas fa-snowflake', // Moderate/heavy snow showers
                '374': 'fas fa-cloud-meatball', // Light showers of ice pellets
                '377': 'fas fa-cloud-meatball', // Moderate/heavy showers of ice pellets
                '386': 'fas fa-bolt', // Patchy light rain with thunder
                '389': 'fas fa-bolt', // Moderate/heavy rain with thunder
                '392': 'fas fa-bolt', // Patchy light snow with thunder
                '395': 'fas fa-bolt' // Moderate/heavy snow with thunder
            };
            
            const iconClass = iconMap[weatherCode] || 'fas fa-cloud';
            weatherIcon.className = iconClass;
        }
        
        function showError(message) {
            errorMessage.textContent = message;
            error.style.display = 'block';
            weatherInfo.style.display = 'none';
        }
        
        // Initialize with a default city
        window.onload = function() {
            cityInput.value = "New Delhi";
            getWeatherData();
        };
    </script>
</body>
</html>
