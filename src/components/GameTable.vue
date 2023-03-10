<script>
  export default {
    name: 'GameTable',
    props: {
      onPlay: {
        type: Boolean,
        required: true,
      },
    },
    data: () => ({
      items: [],
      isLoading: false,
      isInitialized: false,
      snakeItems: [],
      snakeSize: 6,
      lastPressedKey: 'up',
      startInterval: null,
    }),
    computed: {
      headOfSnake() {
        return this.items.find(i => i.isHead === true);
      },
      currentFood() {
        return this.items.find(i => i.isFood === true);
      },
    },
    watch: {
      onPlay(newVal) {
        if (newVal) this.snakeRun();
        else this.snakeStop();
      },
    },
    created() {
      window.addEventListener('keyup', (e) => {
        if (this.onPlay) {
          if (e.which === 37) this.keyPressed('left', 'keyListener');
          if (e.which === 38) this.keyPressed('up', 'keyListener');
          if (e.which === 39) this.keyPressed('right', 'keyListener');
          if (e.which === 40) this.keyPressed('down', 'keyListener');
        } 
      });
    },
    mounted() {
      this.generateSwitches();
      this.generateFood();
    },
    beforeDestroy () {
      clearInterval(this.startInterval);
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
            isFood: false,
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
            }
          }

          this.items.push(tempItem);
        }

        // This sorting is important for last element 
        this.snakeItems.push(this.items[350]);
        this.snakeItems.push(this.items[375]);
        this.snakeItems.push(this.items[400]);
        this.snakeItems.push(this.items[425]);
        this.snakeItems.push(this.items[450]);
        this.snakeItems.push(this.items[475]);

        this.isLoading = false;
        this.isInitialized = true;
      },
      generateFood() {
        const randomRow = Math.floor(Math.random() * 20);
        const randomCol = Math.floor(Math.random() * 24) + 1; // Shouldn't create first col. Its starting column

        const foundedItem = this.items.find(i => i.row === randomRow && i.col === randomCol);

        if (foundedItem.status === true) {
          this.generateFood();
        } else {
          if (this.currentFood) this.currentFood.isFood = false;
          foundedItem.isFood = true;
          foundedItem.status = true;
        }
      },
      keyPressed(pressedBtn, from) {
        if (this.headOfSnake && this.currentFood
        && this.headOfSnake.row === this.currentFood.row
        && this.headOfSnake.col === this.currentFood.col) {
          this.snakeSize += 1;
          this.$emit('increaseScore');
          this.generateFood();
        }

        if (from === 'interval') {
          this.setHeadOfSnake(this.findNewHeadOfSnake(pressedBtn));
          this.lastPressedKey = pressedBtn;
        } else if (((this.lastPressedKey === 'up' || this.lastPressedKey === 'down')&& pressedBtn !== 'down' && pressedBtn !== 'up')
        || ((this.lastPressedKey === 'left' || this.lastPressedKey === 'right') && pressedBtn !== 'left' && pressedBtn !== 'right')) {
          this.setHeadOfSnake(this.findNewHeadOfSnake(pressedBtn));
          this.lastPressedKey = pressedBtn;
        }
      },
      findNewHeadOfSnake(pressedBtn) {
        const oldVal = this.headOfSnake;

        switch (pressedBtn) {
          case 'left':
            return this.items
          .find(i => (i.row === oldVal.row) && (i.col === oldVal.col - 1));
          case 'up':
            return this.items
          .find(i => (i.row === oldVal.row - 1) && (i.col === oldVal.col));
          case 'right':
            return this.items
          .find(i => (i.row === oldVal.row) && (i.col === oldVal.col + 1));
          case 'down':
            return this.items
          .find(i => (i.row === oldVal.row + 1) && (i.col === oldVal.col));
          default: break;
        } 
      },
      setHeadOfSnake(newHeadOfSnake) {
        if (newHeadOfSnake && !(this.snakeItems.find(si => si.swNo === newHeadOfSnake.swNo))) {
          this.headOfSnake.isHead = false;
          newHeadOfSnake.status = true;
          newHeadOfSnake.isHead = true;
          this.snakeItems.unshift(newHeadOfSnake);
  
          if (this.snakeSize < this.snakeItems.length - 1) {
            this.snakeItems[this.snakeItems.length - 1].status = false;
            this.snakeItems.pop();
          }
        } else {
          this.failError();
        }
      },
      snakeRun() {
        const vm = this;

        this.startInterval = setInterval(function(){
            vm.keyPressed(vm.lastPressedKey, 'interval');
        }, 175);
      },
      snakeStop() {
        clearInterval(this.startInterval);
      },
      failError() {
        this.$emit('failError');
      },
    },
  }
</script>

<template>
  <v-container>
    <v-layout row class="game-table">
      <v-progress-linear
        v-if="isLoading || !isInitialized"
        indeterminate
        color="yellow darken-2"
      />

      <template v-else>
        <v-switch
          v-for="item in items" :key="item.swNo"
          v-model="item.status"
          :color="item.isHead ? 'green' : item.isFood ? 'red' : 'primary'"
          hide-details
        />
      </template>
    </v-layout>
  </v-container>
</template>

<style scope lang="scss">
  .game-table {
    margin: auto !important;
    width: 1150px;
  }
</style>