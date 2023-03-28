<template>
  <WelcomeItem>
    <template #icon><IconTooling /></template>
    <template #heading>API REST</template>
    <template #info>
      <div>
        <h2 style="text-align: center; text-transform: uppercase;">{{ location }}</h2>
        <br>
        <table>
          <thead>
            <tr>
              <th>Date</th>
              <th>Max Temperature</th>
              <th>Min Temperature</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(day, index) in days" :key="index">
              <td style="width: 200px">{{ parseDate(day.datetime) }}</td>
              <td style="width: 200px">{{ parseFahrenheit(day.tempmax) }} &deg;C - {{ day.tempmax }} &deg;F</td>
              <td style="width: 200px">{{ parseFahrenheit(day.tempmin) }} &deg;C - {{ day.tempmin }} &deg;F</td>
            </tr>
          </tbody>
        </table>
      </div>
    </template>
  </WelcomeItem>
</template>

<script lang="ts">
import WelcomeItem from './ListItem.vue'
import IconTooling from './icons/IconTooling.vue'

interface Day {
  datetime: string;
  tempmax: number;
  tempmin: number;
}

export default {
  components: { WelcomeItem, IconTooling },
  props: {
    days: {
      type: Array as () => Day[],
      required: true
    },
    location: {
      type: String,
      default: ''
    }
  },  
  methods: {
    parseDate(fecha: any){
      const partesFecha = fecha.split("-");
      const fechaIso = partesFecha.reverse().join("-");
      return fechaIso;
    },    
    parseFahrenheit(tempFahrenheit: any){
      const result = (tempFahrenheit - 32)/1.8 ;
      const tempCelsius = Number(result.toFixed(2));
      return tempCelsius;
    },
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
th{
  font-weight: bolder;
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
