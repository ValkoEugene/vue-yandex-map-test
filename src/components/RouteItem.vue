<template>
  <p>
    <em>
      <span v-if="!haveError">
        {{ `${route.date} ${route.pointA} - ${route.pointB} = ${route.distanceKm} км` }}
      </span>

      <span v-else class="text-danger">
        {{ `${route.date} ${route.error.message}` }}
      </span>
    </em>
  </p>
</template>

<script>
export default {
  name: 'RouteItem',
  props: {
    // Информация о маршруте
    route: {
      type: Object,
      required: true
    }
  },
  computed: {
    // Флаг наличия ошибки при построении маршрута
    haveError() {
      return !!this.route.error && !!this.route.error.message
    }
  }
}
</script>

