<template>
  <div class="city-card-wrapper">
    <!-- Banner -->
    <div v-if="route.query.preview" class="">
      <p>
        You are currently previewing this city, click the "Add to favorite" icon
        to start tracking this city.
      </p>
    </div>
    <!-- Weather Overview -->
    <div class="weather-overview">
      <div>
        <h1 class="title">{{ route.params.city }}</h1>
        <p class="date">
          {{
            new Date(weatherData.currentTime).toLocaleDateString("en-us", {
              weekday: "short",
              day: "2-digit",
              month: "long",
            })
          }}
          <br />
          {{
            new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
              timeStyle: "short",
            })
          }}
        </p>
      </div>
      <div>
        <p class="current-temp">
          {{ Math.round(weatherData.current.temp / 33.8) }}&deg;
        </p>
        <p>
          Feels like
          {{ Math.round(weatherData.current.feels_like / 33.8) }} &deg;
        </p>
      </div>
      <div class="description-wrapper">
        <img
          class="weather-image"
          :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
          alt=""
        />
        <p class="">
          {{ weatherData.current.weather[0].description }}
        </p>
      </div>
    </div>

    <div class="hr"></div>

    <!-- Hourly Weather -->
    <div class="hourly-weather">
      <div class="">
        <h2 class="ta-c">Hourly Weather</h2>
        <div class="hourly-weather__data">
          <div
            v-for="hourData in weatherData.hourly"
            :key="hourData.dt"
            class="hourly-weather-data__item"
          >
            <p class="hourly-date">
              {{
                new Date(hourData.currentTime).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              class="weather-image"
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt=""
            />
            <div
              :style="{ height: Math.round(hourData.temp / 2) + 'px' }"
              class="graph"
            ></div>
            <p class="hourly_temp">
              {{ Math.round(hourData.temp / 33.8) }}&deg;
            </p>
          </div>
        </div>
      </div>
    </div>

    <hr class="hr" />

    <!-- Weekly Weather -->
    <div class="weekly__wrapper">
      <div>
        <h2 class="ta-c">7 Day Forecast</h2>
        <div class="weekly__data">
          <div
            v-for="day in weatherData.daily"
            :key="day.dt"
            class="weekly__data-item"
          >
            <p class="">
              {{
                new Date(day.dt * 1000).toLocaleDateString("en-us", {
                  weekday: "long",
                })
              }}
            </p>
            <img
              class="weather-image"
              :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
              alt=""
            />
            <div class="aw-temp">
              <p class="ta-c">Average temperature:</p>
              <p class="ta-c mt-5">
                {{
                  (Math.round(day.temp.max / 33.8) +
                    Math.round(day.temp.min / 33.8)) /
                  2
                }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="my-10" @click="removeCity">
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=imperial`
    );

    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.current.dt * 1000 + localOffset;
    weatherData.data.currentTime =
      utc + 1000 * weatherData.data.timezone_offset;

    // cal hourly weather offset
    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
    });

    return weatherData.data;
  } catch (err) {
    console.log(err);
  }
};
const weatherData = await getWeatherData();

const router = useRouter();
const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({
    name: "home",
  });
};
</script>
<style scoped>
.my-10 {
  margin: 10px 0;
}
.mt-5 {
  margin-top: 5px;
}
.weekly__wrapper {
  overflow: hidden;
  width: 975px;
  padding: 10px;
}
.weekly__data {
  display: flex;
  gap: 5px;
  overflow-x: scroll;
  overflow-y: hidden;
  padding: 10px;
  align-items: baseline;
}
.weekly__data-item {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.hr {
  background-color: #fff;
  height: 1px;
  border: #fff;
}
.city-card-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.weather-overview {
  display: flex;
  align-items: flex-start;
  gap: 15px;
  margin-top: 10px;
  padding-bottom: 20px;
}
.title {
  font-size: 30px;
}
.date {
  margin-top: 5px;
}
.current-temp {
  font-size: 30px;
  margin-bottom: 5px;
}
.weather-image {
  max-width: 50px;
}
.description-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.graph {
  background-color: #e7f5dc;
  width: 5px;
  margin-top: 5px;
}
.hourly-weather {
  overflow: hidden;
  width: 975px;
}
.hourly-weather__data {
  display: flex;
  overflow-x: scroll;
  overflow-y: hidden;
  align-items: baseline;
  gap: 5px;
  padding: 10px;
}
.hourly-date {
  white-space: nowrap;
}
.ta-c {
  text-align: center;
}
.hourly-weather-data__item {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
.hourly_temp {
  margin-top: 5px;
  font-size: 15px;
}
@media screen and (max-width: 1000px) {
  .hourly-weather,
  .weekly__wrapper {
    width: 768px;
  }
}
@media screen and (max-width: 790px) {
  .hourly-weather,
  .weekly__wrapper {
    width: 475px;
  }
}
@media screen and (max-width: 500px) {
  .hourly-weather,
  .weekly__wrapper {
    width: 375px;
  }
}
@media screen and (max-width: 400px) {
  .hourly-weather,
  .weekly__wrapper {
    width: 300px;
  }
}
</style>
