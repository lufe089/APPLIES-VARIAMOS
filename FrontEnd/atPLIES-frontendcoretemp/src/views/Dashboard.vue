<template>
  <div class="animated fadeIn">
    <p class="text-info">Hello World</p>
    <b-card-group columns class="card-columns cols-2">
      <b-card header="Bar Chart">
        <div class="chart-wrapper">
          <bar-example/>
        </div>
      </b-card>
    </b-card-group>
  </div>
</template>

<script>

import BarExample from '@/views/charts/barExample.vue'

export default {
  name: 'dashboard',
  components: {
    /* tag, component name */
    BarExample

  }
}
</script>
