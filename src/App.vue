<template>
  <div>
    <h1>Weather App</h1>
    <input v-model="city" placeholder="Enter city name" />
    <button @click="fetchWeather">Get Weather</button>
    <p v-if="weather">{{ weather.name }}: {{ weather.main.temp }}°C</p>
    <p v-if="error" style="color: red;">{{ error }}</p>

    <!-- Google Map Container -->
    <div v-if="weather" id="map" style="height: 400px; width: 100%; margin-top: 20px;"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      city: '',
      weather: null,
      error: null,
    };
  },
  methods: {
    async fetchWeather() {
      const apiKey = 'e69c33cd5dd17350d5b414fc9c58eb20'; // Your OpenWeather API key
      const url = `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${apiKey}`;
      
      try {
        const response = await fetch(url);
        
        if (!response.ok) {
          throw new Error(`City not found or API error: ${response.status}`);
        }
        
        const data = await response.json();
        
        if (data && data.main && data.name) {
          this.weather = data;
          this.error = null; // Clear error if successful
          this.loadGoogleMapsScript(data.coord.lat, data.coord.lon); // Load the Google Maps script after fetching weather data
        } else {
          this.error = "Invalid data received from API";
        }
      } catch (error) {
        this.weather = null;
        this.error = error.message;
      }
    },
    loadGoogleMapsScript(lat, lon) {
      const googleMapsApiKey = 'AIzaSyAllpeYRW02po7mhRUKVLi0LF6tjSdRUYQ'; // Replace with your actual Google Maps API key
      const script = document.createElement('script');
      script.src = `https://maps.googleapis.com/maps/api/js?key=${googleMapsApiKey}`;
      script.async = true;
      script.defer = true;

      // When the script is loaded, initialize the map
      script.onload = () => {
        this.initMap(lat, lon);
      };

      document.head.appendChild(script);
    },
    initMap(lat, lon) {
      const mapOptions = {
        zoom: 8,
        center: { lat: lat, lng: lon }
      };
      const map = new google.maps.Map(document.getElementById('map'), mapOptions);
      new google.maps.Marker({
        position: { lat: lat, lng: lon },
        map: map
      });
    }
  }
};
</script>
