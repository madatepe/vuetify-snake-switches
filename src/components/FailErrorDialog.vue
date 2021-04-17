<script>
export default {
  name: 'FailErrorDialog',
  data: () => ({
    visible: false,
  }),
  mounted() {
    this.visible = true;
  },
  methods: {
    close() {
      this.visible = false;
      this.$emit('closed');
    },
  },
}
</script>

<template>
  <v-dialog v-model="visible"
            persistent
            max-width="300">

    <v-card>
      <v-card-title>
        ***************************
      </v-card-title>

      <v-card-text>
        <h1>GAME OVER</h1>
        <br>
        <h2>Your Score: <b>123</b> </h2>
      </v-card-text>

      <v-card-actions>
        <v-spacer />
        <v-btn
            @click.native="close"
            color="red darken-1"
            depressed dark
          >
            <v-icon>mdi-restart</v-icon>
            RESTART
          </v-btn>
        <v-spacer />
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
