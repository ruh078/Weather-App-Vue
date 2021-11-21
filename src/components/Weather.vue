<template>
<div id="main" :class="isday?'day':'night'">
  <input v-model="citysearch" placeholder="Enter City" v-on:keyup.enter="citySearch">
  <div class="container" :class="[main]" v-if="visible">
    <div style="display:flex;">
    <div :class="islocal?'button':'button1'" v-on:click="changetolocal">Local Time of respective country</div>
    <div :class="!islocal?'button':'button1'" v-on:click="changetoyour">Local Time of your country</div></div>
    <div class="city">{{city}}</div>
    <br>
    <div class="country">{{country}}</div>
    <br><br>
    <div class="temperature">{{temp}} °C</div>
    <div class="min-max">
      <div class="item">↓ {{mintemp}} °C</div>
      <div class="item">↑ {{maxtemp}} °C</div>
    </div>
    <br><br>
    <div class="description">{{description}}</div>
    <div class="feels-like">Feels like {{feelslike}} °C</div>
    <br><br><hr>
    
    <div class="details">
       <div class="element">Sunrise<div class="value">{{sunrise}}</div></div>
       <div class="element">Sunset<div class="value">{{sunset}}</div></div>
       <div class="element">Humidity<div class="value">{{humidity}}%</div></div>
       <div class="element">Cloudness<div class="value">{{clouds}}%</div></div>
       <div class="element">Wind<div class="value">{{wind}}m/s</div></div>
       <div class="element">Pressure<div class="value">{{pressure}} hPa</div></div>
    </div>
  </div>
  </div>

</template>

<script>
export default {
  data(){
    return{
      visible:false,
      isday:true,
      citysearch:"",
      city:"",
      country:"",
      temp:"",
      mintemp:"",
      maxtemp:"",
      description:"",
      main:"",
      feelslike:"",
      sunrise:"",
      humidity:"",
      wind:"",
      pressure:"",
      clouds:"",
      timezone:"Change to UTC",
      sunrise_local:"",
      sunset_local:"",
      sunrise_utc:"",
      sunset_utc:"",
      islocal:true,
    }
  },
  methods: {
    citySearch: async function(){
      const key=process.env.VUE_APP_API_KEY;
      try{
      const baseURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citysearch}&appid=${key}&units=metric`;
      const response = await fetch(baseURL);
      const weatherdata = await response.json();
      console.log(weatherdata);
      this.city = weatherdata.name;
      this.country = weatherdata.sys.country;
      this.temp= Math.round(weatherdata.main.temp);
      this.mintemp= Math.round(weatherdata.main.temp_min);
      this.maxtemp= Math.round(weatherdata.main.temp_max);
      this.feelslike = weatherdata.main.feels_like
      this.description = weatherdata.weather[0].description;
      this.main = weatherdata.weather[0].main;
      this.sunrise_local = new Date((weatherdata.sys.sunrise+weatherdata.timezone) * 1000).toLocaleString("en-us", {timeStyle: "short", timeZone: "utc"});
      this.sunset_local = new Date((weatherdata.sys.sunset+weatherdata.timezone) * 1000).toLocaleString("en-us", {timeStyle: "short", timeZone: "utc"});
      this.sunrise_utc = new Date((weatherdata.sys.sunrise) * 1000).toLocaleString("en-us", {timeStyle: "short",});
      this.sunset_utc = new Date((weatherdata.sys.sunset) * 1000).toLocaleString("en-us", {timeStyle: "short",});
      this.humidity = weatherdata.main.humidity;
      this.pressure = weatherdata.main.pressure;
      this.wind = weatherdata.wind.speed;
      this.clouds = weatherdata.clouds.all;
      this.sunrise = this.sunrise_local;
      this.sunset = this.sunset_local;
      if(weatherdata.weather[0].icon.includes("n")){
        this.isday=false;
      }
      else{
        this.isday=true;
      }
      this.visible=true;
      }
      catch(err){
        this.visible=false;
        console.log(err);
      }
      this.citysearch="";
      this.islocal=true;
    },
    changetolocal(){
      this.sunrise = this.sunrise_local;
      this.sunset = this.sunset_local;
      this.islocal=true;
    },
    changetoyour(){
      this.sunrise = this.sunrise_utc;
      this.sunset = this.sunset_utc;
      this.islocal = false;
    }

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
input{
   width:25%;
   background-color: white;
   margin:auto;
   padding:8px;
   height:20px;
}
 .container{
   width:30%;
   margin:auto;
   border-radius: 30px;
   padding:8px;
   margin-top: 10px;
   padding-top:15px;
   position: relative;
   background-repeat: no-repeat;
   background-size: cover;
   background-color: white;
 }

 .button{
   background-color:rgb(10, 10, 82);
   padding:5px;
   width:45%;
   color:white;
   text-align: center;
   font-weight: bold;
   border-radius: 10px;
   margin:5px;
 }
 .button:hover{
   background-color: rgb(171, 171, 228);
   cursor: pointer;
 }
 .button1{
   background-color:rgb(188, 188, 211);
   padding:5px;
   width:45%;
   color:rgb(0, 0, 0);
   text-align: center;
   font-weight: bold;
   border-radius: 10px;
   margin:5px;
 }
 .button1:hover{
   background-color: rgb(171, 171, 228);
   cursor: pointer;
 }
 .city{
   font-size:30px;
 }
 .country{
   font-size:20px;
 }
 .temperature{
   font-size:60px;
   font-weight: bold;
 }
 .min-max{
   font-size:22px;
   display: flex;
   justify-content: center;
 }
 .item{
   padding:2px 20px;
 }
 .description{
   font-size:29px;
   font-weight:bold;
 }
 .feels-like{
   font-size: 19px;
   padding: 5px;
 }
 .details{
   display: grid;
   grid-template-columns: repeat(2,1fr);
   grid-auto-rows: minmax(50px);
 }
 .element{
   padding:7px;
 }
 .value{
   font-size:20px;
   font-weight:bold;
 }
 #main{
   min-height: 100vh;
   min-width:100%;
   display: flex;
   justify-content: center;
   flex-direction: column;
   background-repeat: no-repeat;
   background-size:cover;
 }
 .day{
    background-image:url("../assets/day.jpg");
 }
 .night{
   background-image:url("../assets/night.jpg");
 }
 .Thunderstorm{
   background-image:url("../assets/thunderstorm.jpeg");
   color:white;
 }
 .Drizzle{
    background-image: url("../assets/drizzle.jpg");
  }
  .Clouds{
    background-image:url("../assets/cloud.jpg");
  }
  .Smoke{
    background-image: url("../assets/smoke.png");
  }
  .Rain{
    background-image: url("../assets/rain.jpg");
  }
  .Snow{
    background-image: url("../assets/snow.jpg");
  }
  .Mist{
    background-image: url("../assets/mist.jpg");
    color:white;
  }
  .Haze{
    background-image: url("../assets/smoke.png");
  }
  .Dust{
    background-image: url("../assets/dust.jpg");
  }
  .Fog{
    background-image: url("../assets/fog.jpg");
    color:white;
  }
  .Sand{
    background-image: url("../assets/dust.jpg");
  }
  .Ash{
    background-image: url("../assets/smoke.png");
  }
  .Squall{
    background-image: url("../assets/squall.jpg");
  }
  .Tornado{
    background-image: url("../assets/tornado.jpg");
  }
  .Clear{
    background-image: url("../assets/clear.jpeg");
  }
</style>
