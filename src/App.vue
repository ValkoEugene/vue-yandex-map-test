<template>
  <div id="app" class="container mt-3">
    <template v-if="!isYmapsLoaded">
      <h3><em>Загрузка...</em></h3>
      <div class="progress">
        <div class="progress-bar progress-bar-striped progress-bar-animated" role="progressbar" aria-valuenow="75" aria-valuemin="0" aria-valuemax="100" style="width: 75%"></div>
      </div>
    </template>

    
    <div v-else class="row">
      <div class="col-md-1 col-2">
        <button
          type="button"
          class="btn btn-success"
          :disabled="distanceCalculationAmount >= 10"
          @click="addDistanceCalculatComponent"
        >
          +
        </button>
      </div>

      <div class="col-md-11 col-10">
        <div v-for="number in distanceCalculationAmount" :key="'distanceCalculation' + number">
          <distance-calculation @addNewRoute="addNewRoute"/>
        </div>

        <h2>Журнал запросов</h2>

        <p v-for="(route, index) in routesReverse" :key="'route' + index">
          <em>
            <span v-if="!route.error">
              {{ `${route.date} ${route.pointA} - ${route.pointB} = ${route.distanceKm} км` }}
            </span>

            <span v-else class="text-danger">
              {{ `${route.date} ${route.error.message}` }}
            </span>
          </em>
        </p>
      </div>
    </div>
  </div>
</template>

<script>
import 'bootstrap/dist/css/bootstrap.css'
import DistanceCalculation from './components/DistanceCalculation.vue'

export default {
  name: 'app',
  components: {
    DistanceCalculation
  },
  data: () => ({
    // Флаг что скрипт янедкс карт загрузился
    isYmapsLoaded: false,

    // Маршруты
    routes: [],

    // Количество компонентов с расчётом маршрутов
    distanceCalculationAmount: 1
  }),
  computed: {
    // Маршруты начиная с последнего
    routesReverse() {
      return this.routes.slice().reverse()
    }
  },
  methods: {
    // Добавить компонент расчёта маршрутов
    addDistanceCalculatComponent() {
      if (this.distanceCalculationAmount < 10) {
        this.distanceCalculationAmount += 1
      }
    },

    // Добавить маршрут
    addNewRoute(route) {
      this.routes.push(route)
    },

    // Инициализация скрипта яндекс карт
    initYmapsScript() {
      const ymapsScript = window.document.createElement('script')
      ymapsScript.setAttribute('src', 'https://api-maps.yandex.ru/2.1/?lang=ru_RU')
      window.document.head.appendChild(ymapsScript)

      ymapsScript.onload = () => {
        this.isYmapsLoaded = true
      }
    }
  },
  created() {
    this.initYmapsScript()
  }
}
</script>