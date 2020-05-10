<template>
    <div class="puzzle-wrapper">
        <button @click="shuffleTints()">Suffle</button>
        <div class="puzzle">
            <div v-for="(tint, index) in paint()" :key="tint.id" class="tint" :data-id="index" :style="{ backgroundColor: tint }"></div>
        </div>
    </div>
</template>

<script>
import Column from '@/components/Column.vue';
import { Sortable, Swap } from 'sortablejs';
import Vue from 'vue';
import VueLodash from 'vue-lodash';
import lodash from 'lodash';
 
Vue.use(VueLodash, { name: 'custom' , lodash: lodash });
const chroma = require('chroma-js');

export default {
  name: 'Puzzle',
  props: 
    [
        'color1',
        'color2'
    ],
  components: {
      
  },
  methods: {
      getPalette(c1, c2) {
          return chroma.scale([c1,c2]).mode('lch').colors(9);
      },
      paint() {
          let palette = this.getPalette(this.color1, this.color2);
          let colors = [];
          palette.forEach(function(color) {
              for (let i=0; i<6; i++) {
                  colors.push(chroma(color).brighten(0.4 * i).hex());
              }
          });

          return colors;
      },
      getAllTints() {
        return document.querySelectorAll('.tint');
      },
      getMoveableTints() {
          return Array.from(this.getAllTints()).filter(e => getComputedStyle(e, null).display == 'block');
      },
      getTintsIds() {
          return Array.from(this.getAllTints()).map(e => e.dataset.id);
      },
      addClassStatic() {
        let sortabledList = Array.from(this.getAllTints()).filter(el => el.dataset.id % 6 === 0);
        sortabledList.forEach(el => el.classList.add('static'));
      },
      shuffleTints() {
        var list = document.querySelector('.puzzle');
        for (var i = this.getMoveableTints().length; i >= 0; i--) {
            list.appendChild(this.getMoveableTints()[Math.random() * i | 0]);
        }
      }
  },
  mounted() {
    Sortable.mount(new Swap());
    this.addClassStatic();

    let answer = [];
    let count = this.getAllTints().length;
    for (let i=0; i<count; i++) {
        answer.push(i);
    };

    Sortable.create(document.querySelector('.puzzle'), {
      swap: true,
      swapClass: "highlighted",
      animation: 250,
      easing: "cubic-bezier(0.83, 0, 0.17, 1)",

      onMove: function (e) {
          if (getComputedStyle(e.related, null).display == 'flex') {
            return false;
          }
      },

      onEnd: function(evt) {
          var itemEl = evt.item;
          let playerMoves = [];
          let tints = document.querySelectorAll('.tint');
          tints.forEach(el => playerMoves.push(parseInt(el.dataset.id)));
          
          if (_.isEqual(playerMoves, answer)) {
              alert('braaaaavoooo');
          }
	  }
    });
  }
}
</script>

<style scoped lang="scss">
.puzzle {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    flex-wrap: wrap;
    height: 36rem;
    width: 54rem;
}
.tint {
    width: 6rem;
    height: 6rem;
    cursor: grab;
    background-color: blue;
    position: relative;
    transition: background-color 0.3s;

    &:nth-of-type(6n+1), &:nth-of-type(6n) {
        -webkit-user-select: none;
        -webkit-user-drag: none;
        -webkit-app-region: no-drag;
        cursor: default;
        display: flex;

        &:before {
            position: absolute;
            content: '.';
            color: white;
            left: 50%;
            top: 50%;
            transform: translateX(-50%) translateY(-50%);
        }
    }

    &.highlighted {

    }
}
</style>
