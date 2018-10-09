<template>
  <div>
    <input type="text" v-model.trim="pointA" :disabled="loading">

    <input type="text" v-model.trim="pointB" :disabled="loading">

    <button type="button" :disabled="loading" @click="createRoute">
      go
    </button>

    <button type="button" :disabled="loading" @click="clear">
      clear
    </button>
  </div>
</template>

<script>
import moment from '../../plugins/moment.js'

export default {
  name: 'DistanceCalculation',
  data: () => ({
    // Флаг выполнения запроса к API яндекс карт
    loading: false,

    // Точка A
    pointA: 'Краснодар',

    // Точка B
    pointB: 'Москва'
  }),
  methods: {
    // Отчистить маршруты
    clear() {
      this.pointA = ''
      this.pointB = ''
    },

    // Получить растояние между маршрутами
    getDistance({ pointA, pointB }) {
      return window.ymaps.route([pointA, pointB])
        .then(route => route.getLength())
        .catch(error => Promise.reject(error))
    },

    // Создать маршрут
    createRoute() {
      this.loading = true

      const newRoute = {
        pointA: this.pointA,
        pointB: this.pointB,
        date: moment().format('MM/DD [в] HH:mm')
      }

      this.getDistance(newRoute)
        .then(distance => {
          Object.assign(newRoute, { distance, haveError: false })
          this.$emit('addNewRoute', newRoute)
          this.loading = false
        })
        .catch(({ message }) => {
          Object.assign(newRoute, { message, haveError: true })
          this.$emit('addNewRoute', newRoute)
          this.loading = false
        })
    }
  }
}
</script>
