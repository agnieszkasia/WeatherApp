<template>
  <div id="app" :class="(typeof weather.city != 'undefined' && Math.round(weather.list[0].main.temp) < 0) ? 'cold' : (typeof weather.city != 'undefined' && Math.round(weather.list[0].main.temp) > 20) ? 'hot' : ''">
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

      <div class="weather-wrap" v-if="typeof weather.city != 'undefined'">
        <div class="location-box">

          <div class="location">{{ weather.city.name }} {{ weather.city.country }}</div>
          <div class="main-date">{{dateBuilder()}}</div>
        </div>

        <div class="weather-box">

          <div class="main-temp">
            <img class="main-icon" v-bind:src="'http://openweathermap.org/img/wn/'+weather.list[0].weather[0].icon+'.png'" alt="Weather icon">
            <div class="margin">{{ Math.round(weather.list[0].main.temp)}}°c</div>
            <div class="feels-like">Odczuwalna: {{ Math.round(weather.list[0].main.feels_like)}}°c</div>
          </div>
          <div class="weather">{{ weather.list[0].weather[0].description.toUpperCase() }}</div><br>
          <div class="main-date">Zachmurzenie: {{weather.list[0].clouds.all}}%</div>
          <div class="main-date">Opady: {{weather.list[0].rain['3h']}} mm</div>
          <div class="main-date">Zachód słońca: {{ weather.list[0].wind.speed }} m/s</div><br>


        </div>

        <div class="weather-box">
        <div class="hourly-weather">
          <div v-on:click="weekWeather = !weekWeather" class="weather-botton">
            <div v-show="!weekWeather" class="" >
              Pokaż pogodę godzinową
            </div>
            <div v-show="weekWeather" class="">
              Schowaj pogodę godzinową
            </div>
          
          </div>
          <div v-show="weekWeather" class="">
            <table class="table-hover" v-if="weather">
              <tbody>
              <tr v-for="item in weather.list" :key="item.id" class="weather-item">
                <td class="hour"><div class="date"> {{ convertDate(item.dt) }}</div> {{ convertTime(item.dt) }}</td>
                <td class="rain" >
                  <div v-if="typeof item.rain != 'undefined'">{{ item.rain['3h'] }} mm</div>
                  <div v-if="typeof item.rain == 'undefined'">0 mm</div>
                </td>
                <td class="temp">
                  <div class="temp-line">
                      <img v-bind:src="'http://openweathermap.org/img/wn/'+item.weather[0].icon+'.png'" alt="Weather icon">

                      {{ Math.round(item.main.temp) }}°c


                  </div>
                </td>
                <!--                <td>{{ item.status }}></td>-->
<!--                <td>{{ item.title }}></td>-->
<!--                <td>{{ item.summary }}></td>-->
                <!-- and so on -->
              </tr>
              </tbody>
            </table>
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
      weather: {},
    }
  },
  methods: {
    fetchWeather (e) {
      if (e.key == "Enter") {
        fetch(`${this.url_base}forecast?q=${this.query}&units=metric&lang=pl&APPID=${this.api_key}`)

            .then(res => {
            return res.json();
          }).then(this.setResults);
      }
    },
    setResults (results) {
      this.weather = results;
      console.log(this.weather)
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
      let time = +hour + ":" + minute.substr(-2);
      return time;
    },
    convertDate(results){
      let date = new Date(results * 1000);
      let month = (date.getMonth()+1).toLocaleString('pl-PL', {minimumIntegerDigits: 2, useGrouping:false});
      let day = date.getDate();
      let dateString = day+"."+month;
      return dateString;
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
  width: 100vw;
  background: url('./assets/warm-bg.jpg') fixed;
  background-size: cover;
  transition: 0.4s;
}

#app.cold {
  background: url('./assets/cold-bg.jpg') fixed;
  background-size: cover;

}

#app.hot {
  background: url('./assets/hot-bg.jpg') fixed;
  background-size: cover;

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

.main-date {
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
  text-shadow: none;
}

.weather-box {
  text-align: center;
}

.weather-box .main-temp {
  
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

.main-temp .margin{
  margin-top: -50px;
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

.weather-box .hourly-weather {
  width: 100%;
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

.table-hover{
  width: 100%;
}

.hourly-weather .hour{
  padding-top: 15px;
  border-collapse: collapse;
  display: flex;
}

.hourly-weather .date{
  padding-top: 10px;
  color: #FFF;
  font-size: 15px;
  text-align: center;
  text-shadow: none;
  margin-right: 10px;

}

table {
  border-collapse: collapse;
}

.table-hover td{
  block-size: 60px;
  border-top: #d9d9d9 solid 1px;

}


.hourly-weather .temp{
  text-align: end;
  color: #FFF;
  font-size: 38px;
  font-weight: 700;
  margin: 30px 0;
}

.temp-line{
  display: flex;

}

.temp-line *{
  margin-left: auto;
  margin-right: 0;
}


.hourly-weather .rain{

}

tr{
  margin-top: 20px;
}

.main-icon{
  height: 130px;
}

</style>
