<template>
  <header class="header">
    <nav
      class="nav"
    >
      <RouterLink :to="{ name: 'home' }">
        <div class="logo-wrapper">
          <img class="logo" :src="logo"/>
          <p class="">OT-WEATHER-APP</p>
        </div>
      </RouterLink>

      <div>
        <button
          class="add-button"
          @click="addCity"
          v-if="route.query"
        >Add to favorite</button>
      </div>

    </nav>
  </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from "vue-router";
import { uid } from "uid";
import { ref } from "vue";
import logo from '../assets/img/logo.png'

const savedCities = ref([]);
const route = useRoute();
const router = useRouter();
const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(
      localStorage.getItem("savedCities")
    );
  }

  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };

  savedCities.value.push(locationObj);
  localStorage.setItem(
    "savedCities",
    JSON.stringify(savedCities.value)
  );

  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
};

const modalActive = ref(null);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
};
</script>
<style>
.logo {
  max-width: 75px;
}
.logo-wrapper {
  display: flex;
  gap:10px;
  align-items: center;
}
.nav {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
a{
  text-decoration: none;
  color: inherit;
}
.header {
  margin: 20px 0 10px;
}
.add-button {
  background-color: transparent;
  border: solid 1px #e7f5dc;
  color:inherit;
  font-size: 15px;
  line-height: 15px;
  padding: 5px 10px;
  border-radius: 5px;
  
}
.add-button:hover {
  border: solid 1px #c0dfc2;
  background-color: #fff;
  color:#c0dfc2;
  transition-duration: 0.5s;
}
@media screen and (max-width: 375px) {
  .nav {
    flex-direction: column;
    gap: 10px;
  }
}
</style>
