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
      <input v-model="address" type="text" placeholder="Write City" class="form-control mx-2" />
      <button @click="initialData(address)">Search Data</button>
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
        days: [],
        YOUR_API_KEY : '7X2XTKNUEJ6P6PN27CAG9VRES',
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
          .then((response) => response.json())
          .then((data) => {
            this.processWeatherData(data)
          })
          .catch((err) => {
            console.error(err);
            this.location = err; // TODO
          })
      },
      parseDate(fecha: any){
        const partesFecha = fecha.split("-");
        return partesFecha.reverse().join("-");
      },
      processWeatherData(response: any) {
        this.days = []
        this.address = ''
        let location = response.resolvedAddress
        this.location = location;
        let days = response.days
        for (let i = 0; i < days.length; i++) {
          this.days.push({  // TODO
            datetime: days[i].datetime,
            tempmax: days[i].tempmax,
            tempmin: days[i].tempmin
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
