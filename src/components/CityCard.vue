<template>
  <div
    class="city-card-wrapper"
  >
    <div class="">
      <h2 class="">{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
    </div>

    <div class="temp">
      <p class="">
        {{ Math.round(city.weather.main.temp / 33.8) }}&deg;
      </p>
      <div class="">
        <span class="">
          H:
          {{ Math.round(city.weather.main.temp_max / 33.8) }}&deg;
        </span>
        <span class="">
          L:
          {{ Math.round(city.weather.main.temp_min / 33.8) }}&deg;
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {},
  },
});
</script>
<style>
.city-card-wrapper {
  margin-top: 20px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 50px;
  width: 100%;
}

</style>
