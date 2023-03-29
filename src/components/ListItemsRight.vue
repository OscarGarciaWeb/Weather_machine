<template>
      <div v-if="days">
        <button @click="changeTable(value = 'fahrenheit')">LIST &deg;F</button>      
        <button @click="changeTable(value = 'celsius')">LIST &deg;C</button>      
        <button @click="changeTable(value = 'canvas')">BARS</button>      
      </div>
  <ListItem>
    <template #heading>API REST</template>
    <template #info>
        <h2 style="text-align: center; text-transform: uppercase;">{{ location }}</h2>
      <div>
        <br>
        <table v-if="days">
          <thead>
            <tr v-if="fahrenheit === true || celsius === true">
              <th>Date</th>
              <th>Max Temperature</th>
              <th>Min Temperature</th>
            </tr>
          </thead>          
          <tbody v-if="celsius === true">
            <tr v-for="(day, index) in days" :key="index">
              <td style="width: 200px">{{ parseDate(day.datetime) }}</td>
              <td style="width: 200px">{{ parseFahrenheit(day.tempmax) }} &deg;C</td>
              <td style="width: 200px">{{ parseFahrenheit(day.tempmin) }} &deg;C</td>
            </tr>
          </tbody>
          <tbody v-if="fahrenheit === true">
            <tr v-for="(day, index) in days" :key="index">
              <td style="width: 200px">{{ parseDate(day.datetime) }}</td>
              <td style="width: 200px">{{ day.tempmax }} &deg;F</td>
              <td style="width: 200px">{{ day.tempmin }} &deg;F</td>
            </tr>
          </tbody>       
        </table>   
          <canvas ref="canvas" v-if="canvas" @draw="drawChart"></canvas>
          <!-- <canvas v-if="canvas === true" id="my-canvas" @draw="drawChart"></canvas> -->
      </div>
    </template>
  </ListItem>
</template>

<script lang="ts">
import ListItem from './ListItem.vue'

interface Day {
  datetime: string;
  tempmax: number;
  tempmin: number;
}

export default {
  components: { ListItem },
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
  data() {
    return {
      canvas: true,
      celsius: false,
      fahrenheit: false,
      value: '',
    }
  },  
  mounted() {
    this.$nextTick(() => {
      setTimeout(() => {
        this.drawChart();
      }, 3000);
    });
  },
  methods: {
    drawChart() {
      const canvas = this.$refs.canvas as HTMLCanvasElement;
      // const canvas = document.getElementById('my-canvas') as HTMLCanvasElement;
      console.log('canvas', canvas)
      const ctx = canvas.getContext("2d");
          if (!canvas) {
        console.error('Canvas element not found');
        return;
      }

      if (!ctx) {console.error('No se puede obtener el contexto del canvas');return;}

      const canvasWidth = canvas.width;
      const canvasHeight = canvas.height;

      // Draw x-axis
      ctx.beginPath();
      ctx.moveTo(0, canvasHeight - 20);
      ctx.lineTo(canvasWidth, canvasHeight - 20);
      ctx.stroke();

      // Draw y-axis
      ctx.beginPath();
      ctx.moveTo(10, 0);
      ctx.lineTo(10, canvasHeight - 20);
      ctx.stroke();

      const maxValue = Math.max(...this.days.map(item => item.tempmax));

      this.days.forEach((item, index) => {
        const x = index * 60 + 10;
        const yMax = canvasHeight - item.tempmax * canvasHeight / maxValue - 20;
        const yMin = canvasHeight - item.tempmin * canvasHeight / maxValue - 20;
        ctx.fillStyle = "#ff0000"; // Red for max temperature
        ctx.fillRect(x, yMax, 15, item.tempmax * canvas.height / maxValue);
        ctx.fillStyle = "#0000ff"; // Blue for min temperature
        ctx.fillRect(x, yMin, 15, item.tempmin * canvas.height / maxValue);
      });
    },
    changeTable(value: any){
      if (value === 'celsius') {
        this.fahrenheit = false;
        this.celsius = true;
        this.canvas = false;
      }
      else if(value === 'fahrenheit'){
        this.fahrenheit = true;
        this.celsius = false;
        this.canvas = false;
      }
      else{
        this.drawChart();
        this.fahrenheit = false;
        this.celsius = false;
        this.canvas = true;
      }
    },
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
table {
  border-collapse: collapse;
  border: 1px solid black;
  width: 100%;
}
th, td {
  padding: 8px;
  text-align: left;
  border: 1px solid black;
}
th {
  background-color: #f2f2f2;
}
td:nth-child(2), td:nth-child(3) {
  text-align: center;
}
th{
  font-weight: bolder;
}
th:nth-child(2), th:nth-child(3) {
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
