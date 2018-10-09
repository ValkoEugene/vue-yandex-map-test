<template>
  <div class="row mb-3">
    <div class="col-md-4 mb-3">
      <input
        type="text"
        class="form-control"
        placeholder="Пункт A"
        v-model.trim="pointA"
        :disabled="loading"
      >
    </div>

    <div class="col-md-4 mb-3">
      <input
        type="text"
        class="form-control"
        placeholder="Пункт Б"
        v-model.trim="pointB"
        :disabled="loading"
      >
    </div>

    <div class="col-md-2 col-sm-6 col-6">
      <button
        type="button"
        class="btn btn-primary w-100"
        :disabled="loading || !havePoints"
        @click="createRoute"
      >
        GO!
      </button>
    </div>

    <div class="col-md-2 col-sm-6 col-6">
      <button
        type="button"
        class="btn btn-primary w-100"
        :disabled="loading || !havePoints"
        @click="clear"
      >
        Clear
      </button>
    </div>
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
    pointA: '',

    // Точка B
    pointB: ''
  }),
  computed: {
    // Флаг наличия точек маршрута
    havePoints() {
      return !!this.pointA && !!this.pointB
    }
  },
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
