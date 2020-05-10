<template>
  <div class="easy">
    <div class="choseColors">
      <input @change="changeColor1($event.target.value)" type="color" id="color1" name="color1">
      <label for="color1">{{ color1 }}</label>
      <input @change="changeColor2($event.target.value)" type="color" id="color2" name="color2">
      <label for="color2">{{ color2 }}</label>
    </div>
    <Puzzle :color1="color1" :color2="color2"/>
  </div>
</template>

<script>
import Puzzle from '@/components/Puzzle.vue';

export default {
  name: 'Home',
  components: {
    Puzzle,
  },
  data() {
    return {
      color1: 'blue',
      color2: 'green',
    }
  },
  methods: {
    changeColor1(value) {
      this.color1 = value;
    },
    changeColor2(value) {
      this.color2 = value;
    }
  }
}
</script>
<style scoped lang="scss">
.easy {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>