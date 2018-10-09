<template>
  <div id="app">
    <template v-if="!isYmapsLoaded">
      Загрузка...
    </template>
    
    <template v-else>
      <button type="button" @click="addDistanceCalculatComponent">
        +
      </button>

      <p v-for="number in distanceCalculationAmount" :key="number">
        <distance-calculation @addNewRoute="addNewRoute"/>
      </p>

      <div>
        <h2>Журнал запросов</h2>

        <p v-for="(route, index) in routes" :key="index">
          <span v-if="!route.haveError">
            {{ `${route.date} ${route.pointA} - ${route.pointB} = ${convertMetersToKm(route.distance)} км` }}
          </span>

          <span v-else style="color: red;">
            {{ `${route.date} ${route.message}` }}
          </span>
        </p>
      </div>
    </template>
  </div>
</template>

<script>
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
  methods: {
    // Добавить компонент расчёта маршрутов
    addDistanceCalculatComponent() {
      if (this.distanceCalculationAmount < 10) {
        this.distanceCalculationAmount += 1
      }
    },

    // Добавить маршрут
    addNewRoute(route) {
      this.routes.unshift(route)
    },

    // Конвертировать метры в километры
    convertMetersToKm(value) {
      const km = Math.round(+value / 1000)
      return isNaN(km) ? '-' : km
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