<template>
  <div v-if="days">
    <button @click="changeTable((value = 'fahrenheit'))">LIST &deg;F</button>
    <button @click="changeTable((value = 'celsius'))">LIST &deg;C</button>
    <button @click="changeTable((value = 'canvas'))">BARS</button>
  </div>
  <ListItem>
    <template #heading>API REST</template>
    <template #info>
      <h2 style="text-align: center; text-transform: uppercase">{{ location }}</h2>
      <div>
        <br />
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
        <div style="padding: 20px">
          <canvas style="padding: 20px" ref="canvas" v-if="canvas" id="canvas-element" @draw="drawChart" height="400" width="600"></canvas>
        </div>        
        <div style="display: flex;">
          <div style="display: inline-block; width: 20px; height: 20px; background-color: #ffa500;"></div><p style="display: inline-block;margin: 0px 10px;">Maximum Celsius</p>
          <div style="display: inline-block; width: 20px; height: 20px; background-color: #03ac13;"></div><p style="display: inline-block;margin: 0px 10px;">Minimum Celsius</p>
          <div style="display: inline-block; width: 20px; height: 20px; background-color: #FF0000;"></div><p style="display: inline-block;margin: 0px 10px;">Maximum Fahrenheit</p>
          <div style="display: inline-block; width: 20px; height: 20px; background-color: #0000ff;"></div><p style="display: inline-block;margin: 0px 10px;">Minimum Fahrenheit</p>    
        </div>
      </div>
    </template>
  </ListItem>
</template>

<script lang="ts">
import ListItem from './ListItem.vue'

interface Day {
  datetime: string
  tempmax: number
  tempmin: number
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
      isInitialized: false,
      canvas: true,
      celsius: false,
      fahrenheit: false,
      value: ''
    }
  },
  mounted() {
    this.drawChart();
  },
  watch: {
    days: {
      handler() {
        this.drawChart()
      },
      deep: true
    }
  },
  methods: {
    drawChart() {
      const canvas = this.$refs.canvas as HTMLCanvasElement
      const ctx = canvas.getContext('2d')
      if (!canvas) {
        console.error('Canvas element not found')
        return
      }
      if (!ctx) {
        console.error('Canvas context not found')
        return
      }
      const canvasWidth = canvas.width
      const canvasHeight = canvas.height
      ctx.clearRect(0, 0, canvasWidth, canvasHeight);
      const numDays = this.days.length
      const dayWidth = canvasWidth / numDays
      const maxValue = 65 // Valor máximo para el eje Y
      const gridLabelOffset = 5 // Desplazamiento de las etiquetas de la cuadrícula en el eje X
      const gridStep = 25 // Espacio entre cada línea de la cuadrícula
      const numGridLines = 6 // Número de líneas en la cuadrícula      
      
      if (!this.isInitialized) {
        ctx.translate(15, -20)
        this.isInitialized = true
      }
      
      // Dibujar las líneas de las temperaturas máximas fahrenheit
      ctx.beginPath()
      ctx.strokeStyle = '#ff0000' // Red for max temperature
      this.days.forEach((item, index) => {
        const x = index * 40 + 20
        // const yMax = canvasHeight - (item.tempmax * canvasHeight * 0.8) / maxValue // TODO REVISION NO PINTA BIEN 
        const yMax = 290 - (item.tempmax) // TODO REVISION NO PINTA BIEN 
        ctx.lineTo(x, yMax)
        ctx.arc(x, yMax, 3, 0, 2 * Math.PI)
      })
      ctx.stroke()

      // Dibujar las líneas de las temperaturas mínimas fahrenheit
      ctx.beginPath()
      ctx.strokeStyle = '#0000ff' // Blue for min temperature
      this.days.forEach((item, index) => {
        const x = index * 40 + 20
        // const yMin = canvasHeight - (item.tempmin * canvasHeight * 0.8) / maxValue // TODO REVISION NO PINTA BIEN 
        const yMin = 300 - (item.tempmin) // TODO REVISION NO PINTA BIEN 
        ctx.lineTo(x, yMin)        
        ctx.arc(x, yMin, 3, 0, 2 * Math.PI)
      })
      ctx.stroke()

      // Dibujar las líneas de las temperaturas máximas Celsius
      ctx.beginPath()
      ctx.strokeStyle = '#ffa500' // Red for max temperature
      this.days.forEach((item, index) => {
        const x = index * 40 + 20
        // const yMax = canvasHeight - (((item.tempmax - 32) / 1.8) * canvasHeight * 0.98) / maxValue // TODO REVISION NO PINTA BIEN 
        const yMax = 350 - (((item.tempmax - 32) / 1.8)) // TODO REVISION NO PINTA BIEN 
        ctx.lineTo(x, yMax)
        ctx.arc(x, yMax, 3, 0, 2 * Math.PI)
      })
      ctx.stroke()

      // Dibujar las líneas de las temperaturas mínimas Celsius
      ctx.beginPath()
      ctx.strokeStyle = '#03ac13' // Blue for min temperature
      this.days.forEach((item, index) => {
        const x = index * 40 + 20
        // const yMin = canvasHeight - (((item.tempmin - 32) / 1.8) * canvasHeight * 0.98) / maxValue // TODO REVISION NO PINTA BIEN 
        const yMin = 370 - (((item.tempmin - 32) / 1.8)) // TODO REVISION NO PINTA BIEN 
        console.log(yMin)
        ctx.lineTo(x, yMin)        
        ctx.arc(x, yMin, 3, 0, 2 * Math.PI)
      })
      ctx.stroke()

      // Dibujar las líneas de la grafica 
      ctx.beginPath()
      ctx.strokeStyle = '#aaa'
      for (let i = 0; i <= numGridLines; i++) {
        const y = numGridLines * 25
        ctx.moveTo(0, y)
        ctx.lineTo(numGridLines, y)
      }
      ctx.stroke()
        
      // Dibujar las etiquetas de la cuadrícula en el eje Y
      ctx.fillStyle = '#000'
      ctx.textAlign = 'right'
      ctx.textBaseline = 'middle'
      ctx.font = '10px sans-serif'
      for (let i = 0; i <= numGridLines; i++) {
        const y = canvasHeight - 20 - i * (canvasHeight - 20) / numGridLines
        const label = i * gridStep
        ctx.fillText(label.toString(), gridLabelOffset - 2, y + 3)
      }

      // Dibujar las etiquetas de la cuadrícula eje x
      ctx.fillStyle = '#000'
      ctx.textAlign = 'center'
      ctx.textBaseline = 'middle'
      ctx.font = '9px sans-serif'
      for (let i = 1; i <= numDays; i++) {
        const x = i * dayWidth - dayWidth / 2 + gridLabelOffset // Agregar el desplazamiento a la posición X
        const y = canvasHeight - 5
        ctx.fillText(`Day ${i}`, x, y)
      }    

      // Dibujar las líneas del eje X e Y
      ctx.beginPath()
      ctx.moveTo(0, canvasHeight - 20)
      ctx.lineTo(canvasWidth, canvasHeight - 20)
      ctx.stroke()
      ctx.beginPath()
      ctx.moveTo(10, 0)
      ctx.lineTo(10, canvasHeight - 20)
      ctx.stroke()
    },
    changeTable(value: any) {
      const canvasElement = document.getElementById('canvas-element');
      if (value === 'celsius') {
        this.fahrenheit = false
        this.celsius = true
        canvasElement ? canvasElement.style.display = 'none' : null;
      } else if (value === 'fahrenheit') {
        this.fahrenheit = true
        this.celsius = false
        canvasElement ? canvasElement.style.display = 'none' : null;
      } else {
        this.drawChart()
        this.fahrenheit = false
        this.celsius = false
        canvasElement ? canvasElement.style.display = 'block' : null;
      }
    },
    parseDate(fecha: any) {
      const partesFecha = fecha.split('-')
      const fechaIso = partesFecha.reverse().join('-')
      return fechaIso
    },
    parseFahrenheit(tempFahrenheit: any) {
      const result = (tempFahrenheit - 32) / 1.8
      const tempCelsius = Number(result.toFixed(2))
      return tempCelsius
    }
  }
}
</script>

<style scoped>
table {
  border-collapse: collapse;
  border: 1px solid black;
  width: 100%;
}
th,
td {
  padding: 8px;
  text-align: left;
  border: 1px solid black;
}
th {
  background-color: #f2f2f2;
}
td:nth-child(2),
td:nth-child(3) {
  text-align: center;
}
th {
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
