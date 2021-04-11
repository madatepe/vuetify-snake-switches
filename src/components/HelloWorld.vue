<script>
/* eslint-disable */
  export default {
    name: 'HelloWorld',
    data: () => ({
      items: [],
      isLoading: false,
      isInitialized: false,
      snakeSize: 5,
      lastPressedBtn: 'up',
    }),
    created() {
      window.addEventListener('keyup', (e) => {
        if (e.which === 37) this.keyPressed('left');
        if (e.which === 38) this.keyPressed('up');
        if (e.which === 39) this.keyPressed('right');
        if (e.which === 40) this.keyPressed('down');
      });
    },
    mounted() {
      console.log('mounted');
      this.generateSwitches();
    },
    computed: {
      headOfSnake() {
        return this.items.find(i => i.isHead === true);
      },
      footOfSnake() {
        return this.items.find(i => i.isFoot === true);
      }, 
    },
    methods: {
      generateSwitches() {
        this.isLoading = true;

        for(let i = 1; i <= 500; i++) {
          const tempItem = {
            swNo: i,
            col: i % 25,
            row: (Math.floor(i / 25)) + 1,
            status: false,
            isHead: false,
            isFoot: false,
          };

          // This condition for rows last elements
          if ((i % 25) === 0) {
            tempItem.row = this.items[tempItem.swNo - 2].row;
            tempItem.col = this.items[tempItem.swNo - 2].col + 1;
          }

          if (tempItem.col === 1) {
            if (tempItem.row > 14 && tempItem.row <= 20) {
              tempItem.status = true;

              if (tempItem.row === 15) tempItem.isHead = true;
              if (tempItem.row === 20) tempItem.isFoot = true;
            }
          }

          this.items.push(tempItem);
        }

        this.isLoading = false;
        this.isInitialized = true;
      },
      keyPressed(pressedBtn) {
        this.setHeadOfSnake(this.findNewHeadOrFootOfSnake(pressedBtn, 'head'));
        this.setFootOfSnake(this.findNewHeadOrFootOfSnake(pressedBtn, 'foot'));
      },
      findNewHeadOrFootOfSnake(pressedBtn, type) {
        this.lastPressedBtn = pressedBtn;
        const oldVal = type === 'head' ? this.headOfSnake : this.footOfSnake;

        switch (pressedBtn) {
          case 'left':
            break;
          case 'up':
            return this.items
          .find(i => (i.row === oldVal.row - 1) && (i.col === oldVal.col));
          case 'right':
            break;
          case 'down':
            return this.items
          .find(i => (i.row === oldVal.row + 1) && (i.col === oldVal.col));
          default: break;
        } 
      },
      setHeadOfSnake(newHeadOfSnake) {
        this.headOfSnake.isHead = false;
        newHeadOfSnake.status = true;
        newHeadOfSnake.isHead = true;
      },
      setFootOfSnake(newFootOfSnake) {
        /* Note: If you change isFoot props first, computed will re-render.
        So second line this.footOfSnake will undefined */
        this.footOfSnake.status = false;
        this.footOfSnake.isFoot = false;
        newFootOfSnake.status = true;
        newFootOfSnake.isFoot = true;
      },
      asdf(item) {
        console.log(item);
      },
    },
  }
</script>

<template>
  <v-container>
    <v-layout row wrap>
        <!-- {{ items.map((i) => { if(i.status === true) return i.swNo || 'a'; }) }} -->
        <v-progress-linear
          v-if="isLoading || !isInitialized"
          indeterminate
          color="yellow darken-2"
        />

      <template v-else>
        <v-switch
          v-for="item in items" :key="item.swNo"
          v-model="item.status"
          :color="item.isHead ? 'green' : 'primary'"
          hide-details
          @change="asdf(item)"
        />
          <!-- :label="item.swNo.toString()" -->
      </template>
    </v-layout>
  </v-container>
</template>
