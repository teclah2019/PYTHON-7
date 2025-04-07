# PYTHON-7

import requests

API_KEY = 'your_api_key_here'  # Get your API key from https://openweathermap.org/api

def get_weather(city):
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric"
    response = requests.get(url)
    data = response.json()

    if data['cod'] == 200:
        weather_data = {
            'city': city,
            'temperature': data['main']['temp'],
            'weather': data['weather'][0]['description'],
            'humidity': data['main']['humidity'],
            'pressure': data['main']['pressure']
        }
        return weather_data
    else:
        return None

def display_weather(city):
    weather_data = get_weather(city)
    
    if weather_data:
        print(f"Weather in {weather_data['city']}:\n")
        print(f"Temperature: {weather_data['temperature']}°C")
        print(f"Weather: {weather_data['weather']}")
        print(f"Humidity: {weather_data['humidity']}%")
        print(f"Pressure: {weather_data['pressure']} hPa")
    else:
        print("City not found or API error!")

# Example usage
if __name__ == "__main__":
    city = input("Enter the city name: ")
    display_weather(city)


from flask import Flask, render_template, request
import requests

app = Flask(__name__)

API_KEY = 'your_api_key_here'  # Replace with your OpenWeatherMap API key

def get_weather(city):
    url = f"http://api.openweathermap.org/data/2.5/weather?q={city}&appid={API_KEY}&units=metric"
    response = requests.get(url)
    data = response.json()

    if data['cod'] == 200:
        weather_data = {
            'city': city,
            'temperature': data['main']['temp'],
            'weather': data['weather'][0]['description'],
            'humidity': data['main']['humidity'],
            'pressure': data['main']['pressure']
        }
        return weather_data
    else:
        return None

@app.route("/", methods=["GET", "POST"])
def index():
    weather_data = None
    if request.method == "POST":
        city = request.form.get('city')
        weather_data = get_weather(city)
        if weather_data is None:
            weather_data = "City not found or API error!"
    
    return render_template("index.html", weather_data=weather_data)

if __name__ == "__main__":
    app.run(debug=True)


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather App</title>
</head>
<body>
    <h1>Weather App</h1>

    <form method="POST">
        <label for="city">Enter City: </label>
        <input type="text" id="city" name="city" required>
        <button type="submit">Get Weather</button>
    </form>

    {% if weather_data %}
        {% if weather_data == 'City not found or API error!' %}
            <p>{{ weather_data }}</p>
        {% else %}
            <h2>Weather in {{ weather_data['city'] }}</h2>
            <p>Temperature: {{ weather_data['temperature'] }}°C</p>
            <p>Weather: {{ weather_data['weather'] }}</p>
            <p>Humidity: {{ weather_data['humidity'] }}%</p>
            <p>Pressure: {{ weather_data['pressure'] }} hPa</p>
        {% endif %}
    {% endif %}
</body>
</html>
