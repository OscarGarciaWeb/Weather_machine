<script setup lang="ts">
  import { RouterLink, RouterView } from 'vue-router'
  import InitPage from './components/InitPage.vue'
  import listitemsright from './components/ListItemsRight.vue'
</script>

<template>
  <header>
    <img alt="Logo" class="logo" src="/favicon.png" width="125" height="125" />
    <div class="wrapper">
      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
      </nav>
      <InitPage msg="Weather Machine" />
      <input v-model="address" type="text" placeholder="Write City" class="inputText" />
      <button @click="initialData(address)" class="buttonSearch">Search Data</button>
      <p v-if="error" class="errorMessage">The city not exist, Type it again</p>
    </div>
  </header>
  <RouterView>
    <div v-if="days.length > 0">
      <listitemsright :days="days" :location="location" />
    </div>
  </RouterView>
</template>

<script lang="ts">
export default {
  components: { listitemsright },
  data() {
    return {
      address: '',
      data: {},
      location: '',
      days: [] as { datetime: any, tempmax: any, tempmin: any }[],
      YOUR_API_KEY: '7X2XTKNUEJ6P6PN27CAG9VRES',
      error: null,
    }
  },
  methods: {
    async initialData(address: any) {
      fetch(
        `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${address}?unitGroup=us&key=${this.YOUR_API_KEY}&contentType=json`,
        {
          method: 'GET',
          headers: {}
        }
      )
      .then((response) => {
        if (response.ok) {
          this.error = null;
          return response.json();
        } else {
          throw new Error('Error en la solicitud');
        }
      })
      .then((data) => {
        this.processWeatherData(data)
      })
      .catch((err) => {
        this.error = err;
        this.days = [];
      })
    },
    parseDate(fecha: any) {
      const partesFecha = fecha.split('-');
      return partesFecha.reverse().join('-');
    },
    processWeatherData(response: any) {
      this.days = [];
      this.address = '';
      let location = response.resolvedAddress;
      this.location = location;
      let dataDays = response.days;
      for (let i = 0; i < dataDays.length; i++) {
        this.days.push({
          datetime: dataDays[i].datetime,
          tempmax: dataDays[i].tempmax,
          tempmin: dataDays[i].tempmin
        })
      }
    }
  }
}
</script>

<style scoped>
header {
  line-height: 1.5;
  max-height: 100vh;
}
.logo {
  display: block;
  margin: 0 auto 2rem;
}
nav {
  width: 100%;
  font-size: 12px;
  text-align: center;
  margin-top: 2rem;
}
nav a.router-link-exact-active {
  color: purple;
  font-weight: bolder;
}
nav a.router-link-exact-active:hover {
  background-color: transparent;
}
nav a {
  display: inline-block;
  padding: 0 1rem;
  border-left: 1px solid var(--color-border);
}
nav a:first-of-type {
  border: 0;
}

table {
  border-collapse: collapse;
  width: 100%;
}
th,
td {
  padding: 8px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}
th {
  background-color: #f2f2f2;
}
td:nth-child(2),
td:nth-child(3) {
  text-align: center;
}

th:nth-child(2),
th:nth-child(3) {
  text-align: center;
}
.inputText{
  padding: 10px;
  margin: 10px;
  border: 1px solid black;
  border-radius: 20px;
}

.buttonSearch{
  padding: 10px;
  margin: 8px;
  color: white;
  font-weight: bolder;
  font-size: 1.1em;
  border: 1px solid black;
  border-radius: 10px;
  background-color: rgb(89, 194, 232);
}
.errorMessage{
  color: red;
  font-weight: bolder;
  font-size: 1.1em;
  margin: 0 auto;
}
@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }
  .logo {
    margin: 0 2rem 0 0;
  }
  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
  nav {
    text-align: left;
    margin-left: -1rem;
    font-size: 1rem;
    padding: 1rem 0;
    margin-top: 1rem;
  }
}
</style>
