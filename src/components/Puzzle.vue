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
      reuniteTints(arrays) {
        let array0 = [], array1 = [], array2 = [], array3 = [], array4 = [], array5 = [], array6 = [], array7 = [], array8 = [];
        let tints = [array0, array1, array2, array3, array4, array5, array6, array7, array8];

        for (let array of arrays) {
            for (let j=0; j<array.length; j++) {
                tints[j].push(array[j]);
            }
        };

          return tints.flat();
      },
      getAllTints() {
        return document.querySelectorAll('.tint');
      },
      getMoveableTints() {
          return Array.from(this.getAllTints()).filter(e => {
              return e.dataset.id >= 9 && e.dataset.id < this.getAllTints().length - 9;
            });
      },
      paint() {
          let palette = this.getPalette(this.color1, this.color2);
          let colors0 = [], colors1 = [], colors2 = [], colors3 = [], colors4 = [], colors5 = [], colors6 = [], colors7 = [], colors8 = [];
          let colors = [colors0, colors1, colors2, colors3, colors4, colors5, colors6, colors7, colors8];
          palette.forEach(function(color, index) {
              for (let i=0; i<6; i++) {
                  colors[index].push(chroma(color).brighten(0.4 * i).hex());
              }
          });

          return this.reuniteTints(colors);
      },
      getTintsIds() {
          return Array.from(this.getAllTints()).map(e => e.dataset.id);
      },
      shuffleTints() {
        var list = document.querySelector('.puzzle');
        for (var i = this.getMoveableTints().length; i >= 0; i--) {
            console.log(this.getMoveableTints().length+9);
            list.insertBefore(this.getMoveableTints()[Math.random() * i | 1], list.children[this.getAllTints().length-9]);
        }
      }
  },
  mounted() {
    Sortable.mount(new Swap());

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
    align-items: flex-start;
    flex-wrap: wrap;
    height: 36rem;
    width: 54rem;

    .tint {
        width: 6rem;
        height: 6rem;
        cursor: grab;
        background-color: blue;
        position: relative;
        transition: background-color 0.3s;

        &:nth-of-type(-n+9), &:nth-last-child(-n+9) {
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
}
</style>
