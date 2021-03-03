<template>
  <div id="app" :class="(typeof weather.main != 'undefined' && Math.round(weather.main.temp) < 0) ? 'cold' : (typeof weather.main != 'undefined' && Math.round(weather.main.temp) > 20) ? 'hot' : ''">
    <main>
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar" 
          placeholder="Podaj miasto..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{dateBuilder()}}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp)}}°c
          <div class="feels-like">Odczuwalna: {{ Math.round(weather.main.feels_like)}}°c</div>
</div>
          <div class="weather">{{ weather.weather[0].description.toUpperCase() }}</div><br>
          <div class="date">Zachmurzenie: {{weather.clouds.all}}%</div>
          <div class="date">Wschód słońca: {{convertTime(weather.sys.sunrise)}}</div>
          <div class="date">Zachód słońca: {{convertTime(weather.sys.sunset)}}</div><br>
          

        </div>

        <div class="weather-box">
        <div class="week-weather">
          <div v-on:click="weekWeather = !weekWeather" class="weather-botton">
            <div v-show="!weekWeather" class="">
              Pokaż pogodę na następne dni
            </div>
            <div v-show="weekWeather" class="">
              Schowaj pogodę na następne dni
            </div>
          
          </div>
          <div v-show="weekWeather" class="">
            jakas podoga
          </div>
        </div>
        </div>

        
       
      </div>

    </main>
  </div>
</template>

<script>

export default {
  name: 'app',
  data () {
    return {
      weekWeather: false,
      api_key: '37dd4b294eaebd27d5b3350697b71fec',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {}
    }
  },
  methods: {
    fetchWeather (e) {
      if (e.key == "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&lang=pl&APPID=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
      }
    },
    setResults (results) {
      this.weather = results;
    },
    dateBuilder (){
      let d = new Date();
      let months = ["Styczenia", "Lutego", "Marca", "Kwietnia",
                    "Maja", "Czerwca", "Lipca", "Sierpnia",
                    "Września", "Października", "Listopada", "Grudnia"];
      let days = ["Poniedziałek", "Wtorek", "Środa", "Czwartek", 
                  "Piątek", "Sobota", "Niedziela"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day}, ${date} ${month} ${year}`;
    },
    convertTime(results){
      let date = new Date(results * 1000);
      let hour = date.getHours();
      let minute = "0" + date.getMinutes();
      let time = hour + ":" + minute.substr(-2);
      return time;
    },
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'montserrat', sans-serif;
}

#app {
  background-image: url('./assets/warm-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.cold {
  background-image: url('./assets/cold-bg.jpg');
}

#app.hot {
  background-image: url('./assets/hot-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;

  background-image: linear-gradient(to bottom, rgba(30, 30, 30, 0.4), rgba(30, 30, 30, 0.4));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  
  color: #313131;
  font-size: 20px;

  appearance: none;
  border:none;
  outline: none;
  background: none;

  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 16px 16px 16px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 16px 16px 16px;
}

.location-box .location {
  color: #FFF;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 700;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  
  background-color:rgba(255, 255, 255, 0.5);
  border-radius: 16px;
  margin: 30px 0;

  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  transition: 0.6s;
}

.weather-box .feels-like {
  color: #FFF;
  font-size: 24px;
  font-weight: 600;
  text-shadow: 3px 3px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 40px;
  font-weight: 600;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-botton {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 16px;
  font-weight: 700;

  text-shadow: 3px 3px rgba(0, 0, 0, 0.25);
  
  background-color:rgba(255, 255, 255, 0);
  border: none;
  margin: 10px 0;
}

.weather-box .week-weather {
  
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 24px;
  font-weight: 700;

  text-shadow: 3px 3px rgba(0, 0, 0, 0.25);
  
  background-color:rgba(255, 255, 255, 0.5);
  border-radius: 16px;
  margin: 30px 0;

  box-shadow: 0 0 8px rgba(0, 0, 0, 0.25);
  transition: 0.4s;
}

</style>
