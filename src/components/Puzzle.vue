<template>
    <div class="puzzle-wrapper">
        <button @click="shuffleTints()">Shuffle</button>
        <button @click="originalOrder = getOriginalOrder()">Go !</button>
        <draggable v-model="paintUpdated" :move="handleMove" @end="handleDragEnd" :options="{animation:500}" class="puzzle">
            <div v-for="(tint, index) in items" :key="tint.id" class="tint" :data-id="index" :style="{ backgroundColor: tint }"></div>
        </draggable>
        <p><span id="swapNumber">{{ move }}</span> coup{{ move > 1 ? 's' : '' }}</p>
    </div>
</template>

<script>
import Column from '@/components/Column.vue';
import draggable from 'vuedraggable';
import Vue from 'vue';
import VueLodash from 'vue-lodash';
import lodash from 'lodash';

Vue.use(VueLodash, { name: 'custom' , lodash: lodash });
const chroma = require('chroma-js');

export default {
  name: 'Puzzle',
  data() {
    return {
      move: 0,
      items: this.paint(),
      originalOrder: []
    }
  },
  props: 
    [
        'colorsChosen'
    ],
  components: {
      draggable,
  },
  computed: {
      paintUpdated() {
          return this.items = this.paint();
      }
  },
  methods: {
    incrementMove() {
        this.move++;
    },
    paint() {
        let colorsChosenToArray = Object.values(this.colorsChosen);
        let palette = this.getPalette(...colorsChosenToArray);
        let colors0 = [], colors1 = [], colors2 = [], colors3 = [], colors4 = [], colors5 = [], colors6 = [], colors7 = [], colors8 = [];
        let colors = [colors0, colors1, colors2, colors3, colors4, colors5, colors6, colors7, colors8];
        palette.forEach(function(color, index) {
            for (let i=0; i<6; i++) {
                colors[index].push(chroma(color).brighten(0.4 * i).hex());
            }
        });

        return this.items = this.reuniteTints(colors);
    },
    handleMove(e) {
        let colorDragged = e.dragged;
        const { index, futureIndex } = e.draggedContext;
        this.movingIndex = index;
        this.futureIndex = futureIndex;
        return false; // disable sort
    },
    handleDragEnd() {
        let movingElement = document.querySelector(`[data-id="${this.movingIndex}"]`);
        let futureElement = document.querySelector(`[data-id="${this.futureIndex}"]`);
        // prevent element from moving
        if (getComputedStyle(futureElement, null).display == 'flex') {
            return false;
        }
        this.futureItem = this.items[this.futureIndex];
        this.movingItem = this.items[this.movingIndex];
        const _items = Object.assign([], this.items);
        _items[this.futureIndex] = this.movingItem;
        _items[this.movingIndex] = this.futureItem;
        this.items = _items;

        if (_.isEqual(_items, this.originalOrder)) {
            alert('YEEESSS');
        }
        console.log(this.originalOrder);
        this.incrementMove();
    },
    getPalette(...colors) {
        return chroma.scale([...colors]).mode('lch').colors(9);
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
    getOriginalOrder() {
        const tints = this.getAllTints();
        const originalArray = [];
        Array.prototype.forEach.call(tints, function(tint) {
            originalArray.push(chroma(tint.style.backgroundColor).hex());
        });
        return originalArray;
    },
    getMoveableTints() {
        return Array.from(this.getAllTints()).filter(e => {
            return e.dataset.id > 7 && e.dataset.id < this.getAllTints().length - 9;
        });
    },
    getTintsIds() {
        return Array.from(this.getAllTints()).map(e => e.dataset.id);
    },
    shuffleTints() {
        var list = document.querySelector('.puzzle');
        for (var i = this.getMoveableTints().length; i >= 0; i--) {
            list.insertBefore(this.getMoveableTints()[Math.random() * i | 1], list.children[this.getAllTints().length-9]);
        }
    },
  },
}
</script>

<style scoped lang="scss">
.puzzle {
    display: flex;
    align-items: flex-start;
    flex-wrap: wrap;
    height: 36rem;
    width: 56rem;

    .tint {
        width: 6rem;
        height: 6rem;
        cursor: grab;
        background-color: blue;
        position: relative;
        border: 1px solid transparent;
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

        &.sortable-ghost:not(.sortable-chosen) {
            width: 5rem;
            height: 5rem;
        }

        &.sortable-ghost {
            border: 1px solid white;
        }
    }
}
</style>
