<template>
   <div
      id="app"
      :style="{ backgroundImage: `url(${getBg})` }"
   >
      <audio
         ref="audio"
         :src="`${publicPath}${getSound}`"
         preload
         loop
         id="audio"
         muted
      ></audio>
      <main>
         <div class="search-box">
            <input
               type="text"
               class="search-bar"
               placeholder="Search..."
               v-model="query"
               @keypress.enter="fetchWeather"
            />
            <button
               class="btn"
               @click="fetchWeather"
            ></button>
         </div>

         <div
            class="error"
            v-show="error"
         >
            No such country found
         </div>
         <div
            class="weather-wrap"
            v-if="weather !== null"
         >
            <div class="location-box">
               <div class="location">
                  {{ weather.name }}, {{ weather.sys.country }}
               </div>
               <div class="date">{{ dateNow }}</div>
            </div>

            <div class="weather-box">
               <div class="temp">
                  {{ Math.round(weather.main.temp) }}°
               </div>
               <div class="weather">
                  {{ weather.weather[0].main }}
               </div>
            </div>
         </div>
         <button
            class="btn-muted"
            @click="muted = !muted"
         >
            Muted
         </button>
      </main>
   </div>
</template>

<script>
import axios from "axios";

export default {
   data() {
      return {
         api_key: "b89e0a4a6e00114441b58c1192197291",
         url_base: "https://api.openweathermap.org/data/2.5/",
         query: "",
         weather: null,
         error: false,
         publicPath: process.env.BASE_URL,
         muted: false,
         timezone: null,
      };
   },
   methods: {
      async fetchWeather() {
         let audio = this.$refs.audio;
         audio.pause();

         if (this.query !== "") {
            this.error = false;
            try {
               const data = await axios.get(
                  `${this.url_base}weather?q=${this.query}&units=metric&appid=${this.api_key}`
               );

               this.weather = data.data;
               this.timezone = data.data.timezone / 3600;
               console.log(this.weather);
               audio.play();
            } catch (error) {
               this.weather = null;
               this.error = true;
            }
         }
      },
   },

   computed: {
      getSound() {
         setTimeout(() => {
            if (this.weather !== null) {
               switch (this.weather.weather[0].main) {
                  case "Rain":
                     return "Rain.mp3";
                  case "Clear":
                     return "Clear.mp3";
                  case "Clouds":
                     return "Clouds.mp3";
               }
            } else return "Clouds.mp3";
         }, 1000);
      },
      getBg() {
         if (this.weather === null) {
            return "https://raw.githubusercontent.com/TylerPottsDev/weather-vue/master/src/assets/cold-bg.jpg";
         } else
            switch (this.weather.weather[0].main) {
               case "Rain":
                  return "https://media.freestocktextures.com/cache/e1/3d/e13dbdfceb33473e2e1be208dd67d89d.jpg";
               case "Clear":
                  return "https://media.istockphoto.com/photos/sun-on-blue-sky-with-clouds-picture-id824800468?k=20&m=824800468&s=612x612&w=0&h=Av3vLFHV48dsNZCQEg9A3Ga5qq1L2tdrsJ7fbNrqVKg=";
               case "Clouds":
                  return "https://c1.wallpaperflare.com/preview/748/977/304/storm-sky-cloudy-weather.jpg";

               default:
                  return "https://raw.githubusercontent.com/TylerPottsDev/weather-vue/master/src/assets/cold-bg.jpg";
            }
      },
      dateNow() {
         const d = new Date();
         let t = this.timezone;

         const months = [
            "January",
            "February",
            "March",
            "April",
            "May",
            "June",
            "July",
            "August",
            "September",
            "October",
            "November",
            "December",
         ];
         const days = [
            "Sunday",
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday",
            "Saturday",
         ];

         let day = days[d.getUTCDay(t)] + " ";
         let date = d.getUTCDate(t) + " ";
         let month = months[d.getUTCMonth(t)] + " ";
         let year = d.getUTCFullYear(t) + " ";

         return day + date + month + year;
      },
   },
   watch: {
      muted() {
         let audio = this.$refs.audio;
         this.muted === true ? audio.pause() : audio.play();
      },
   },
};
</script>

<style lang="scss">
* {
   margin: 0;
   padding: 0;
   box-sizing: border-box;
}
body {
   font-family: "monserrat" sans-serif;
}
#app {
   background-size: cover;
   background-position: bottom;
   transition: 0.4s;
}

main {
   min-height: 100vh;
   padding: 25px;

   background-image: linear-gradient(
      to bottom,
      rgba(0, 0, 0, 0.25),
      rgba(0, 0, 0, 0.75)
   );
}
.search-box {
   width: 100%;
   margin-top: 30px;
   position: relative;
   .btn {
      position: absolute;
      border: none;
      outline: none;
      right: 0;
      top: 0;
      width: 50px;
      height: 100%;
      background: transparent;
      background-image: url("https://cdn2.iconfinder.com/data/icons/ios-7-icons/50/search-512.png");
      background-size: cover;
      background-position: center center;
      cursor: pointer;
   }
   .btn:active {
      transform: scale(0.95);
   }
}

.search-box .search-bar {
   display: block;
   width: 100%;
   padding: 15px;

   color: #313131;
   font-size: 20px;

   appearance: none;
   border: none;
   outline: none;
   background: none;
   box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
   background-color: rgba(255, 255, 255, 0.5);
   border-radius: 0px 16px 0px 16px;
   transition: 0.4s;
}
.search-box .search-bar:focus {
   box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
   background-color: rgba(255, 255, 255, 0.85);
   border-radius: 16px 0px 16px 0px;
}

.location-box {
   .location {
      color: #fff;
      font-size: 32px;
      font-weight: 500;
      text-align: center;
      text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
   }
   .date {
      color: #fff;
      font-size: 20px;
      font-weight: 300;
      text-align: center;
      font-style: italic;
   }
}
.weather-box {
   text-align: center;
   .temp {
      display: inline-block;
      padding: 10px 25px;
      color: #fff;
      font-size: 102px;
      font-weight: 900;
      text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
      background-color: rgba(255, 255, 255, 0.25);
      border-radius: 16px;
      margin: 30px 0;

      box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
   }
   .weather {
      color: #fff;
      font-size: 48px;
      font-weight: 700;
      font-style: italic;
      text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
   }
}
.error {
   margin-top: 10px;
   color: rgb(17, 58, 104);
   font-size: 18px;
   text-align: center;
}
.btn-muted {
   padding: 10px 25px;
   color: #fff;
   font-size: 20px;
   font-weight: 900;
   text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
   background-color: rgba(255, 255, 255, 0.25);
   border-radius: 16px;
   margin: 30px 0;
   position: absolute;
   bottom: 0;
   left: 20px;
   box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
