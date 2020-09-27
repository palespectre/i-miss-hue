<template>
  <div class="hard">
    <div class="choseColors">
      <input @change="changeColor1($event.target.value)" type="color" id="color1" name="color1">
      <label for="color1">{{ colors.color1 }}</label>
      <input @change="changeColor2($event.target.value)" type="color" id="color2" name="color2">
      <label for="color2">{{ colors.color2 }}</label>
      <input @change="changeColor3($event.target.value)" type="color" id="color3" name="color3">
      <label for="color3">{{ colors.color3 }}</label>
      <input @change="changeColor4($event.target.value)" type="color" id="color4" name="color4">
      <label for="color4">{{ colors.color4 }}</label>
    </div>
    <Puzzle :colorsChosen="colors" />
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
      colors: {
        color1: 'blue',
        color2: 'green',
        color3: 'purple',
        color4: 'red',
      }
    }
  },
  methods: {
    changeColor1(value) {
      this.colors.color1 = value;
    },
    changeColor2(value) {
      this.colors.color2 = value;
    },
    changeColor3(value) {
      this.colors.color3 = value;
    },
    changeColor4(value) {
      this.colors.color4 = value;
    }
  }
}
</script>
<style scoped lang="scss">
.hard {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>