<script>
import GameTable from './components/GameTable';

export default {
  name: 'App',
  components: {
    GameTable,
  },
  data: () => ({
    onPlay: false,
    score: 0,
    gameTableVisible: true,
  }),
  created() {
    window.addEventListener('keyup', (e) => {
      if (e.which === 32) {
        this.onPlay = !this.onPlay;
      }
    });
  },
  methods: {
    increaseScore() {
      this.score += 5;
    },
    restartGame() {
      this.gameTableVisible = false,
      this.onPlay = false,
      this.score = 0;
      this.$nextTick(() => {
        this.gameTableVisible = true;
      });
    },
  },
};
</script>

<template>
  <v-app>
    <v-app-bar
      app dark color="primary"
    >
      <v-layout row wrap
                align-center
                justify-space-between>
        <v-flex shrink px-1>
          <v-btn
            @click="onPlay = true"
            color="success"
            x-large depressed dark
            :disabled="onPlay"
          >
            <v-icon x-large>mdi-play</v-icon>
            PLAY
          </v-btn>
        </v-flex>

        <v-flex shrink px-3>
          <v-btn
            @click="onPlay = false"
            color="orange"
            large depressed dark
            :disabled="!onPlay"
          >
            <v-icon x-large>mdi-pause</v-icon>
            PAUSE
          </v-btn>
        </v-flex>

        <v-flex shrink>
          <v-btn
            @click="restartGame"
            color="blue-grey darken-1"
            large depressed dark
          >
            <v-icon x-large>mdi-restart</v-icon>
            RESTART
          </v-btn>
        </v-flex>

        <v-spacer />

        <v-flex shrink>
          You can press SPACE for {{ onPlay ? 'PAUSE' : 'PLAY' }}
        </v-flex>

        <v-spacer />

        <v-flex shrink>
          <v-chip
            class="ma-2"
            color="white"
            large outlined
          >
          <v-icon large color="amber" left>
            mdi-fire
          </v-icon>
            <h2>SCORE: {{ score }}</h2>
          </v-chip>
        </v-flex>
      </v-layout>
    </v-app-bar>

    <v-main>
      <GameTable
        v-if="gameTableVisible"
        :on-play="onPlay"
        @increaseScore="increaseScore"
      />
    </v-main>
  </v-app>
</template>
