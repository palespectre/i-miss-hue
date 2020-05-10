<template>
    <div class="column">
        <div v-for="tint in getTints(color)" class="tint" :style="{ backgroundColor: tint }"></div>
    </div>
</template>

<script>
const Values = require('values.js');
const puzzle = document.querySelector('.puzzle');
import { Sortable, Swap } from 'sortablejs';

export default {
  name: 'Column',
  props: ['color'],
  data() {
    return {}
  },
  methods: {
    getTints (c) {
      let value = new Values(c);
      let tints = value.all(12);
      tints.splice(0, 3);
      tints.splice(8, 10);
      tints.reverse();

      return tints.map(tint => '#' + tint.hex.toString());
    }
  },
  mounted() {
    Sortable.create(document.querySelector('.column'), {
      swap: true,
      swapClass: "highlighted",
      animation: 150
    });
  }
}
</script>

<style scoped lang="scss">
.tint {
    width: 5rem;
    height: 5rem;
    cursor: grab;
}
</style>