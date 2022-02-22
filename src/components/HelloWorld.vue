<template>
  <div>
    <div class="container">
      <ul>
        <li class="red" @click="panelClicked($event, 'red.mp3')" ref="red"></li>
        <li
          class="blue"
          @click="panelClicked($event, 'blue.mp3')"
          ref="blue"
        ></li>
        <li
          class="yellow"
          @click="panelClicked($event, 'yellow.mp3')"
          ref="yellow"
        ></li>
        <li
          class="green"
          @click="panelClicked($event, 'green.mp3')"
          ref="green"
        ></li>
      </ul>
    </div>
    <div class="buttons">
      round:{{ sequence.length }}
      <div>
        <button :disabled="statrted" @click="start(1500)">легкий</button>
      </div>
      <div>
        <button :disabled="statrted" @click="start(1000)">нормальный</button>
      </div>
      <div>
        <button :disabled="statrted" @click="start(400)">сложный</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      sequence: [],
      sequenceTuGuess: [],
      canClick: false,
      time: 0,
      statrted: false,
    };
  },
  watch: {
    canClick(value) {
      value ? this.clickHandler("add") : this.clickHandler("remove");
    },
  },
  methods: {
    change() {
      console.log("canClick");
    },
    getRandomPanel() {
      const red = this.$refs.red;
      const blue = this.$refs.blue;
      const yellow = this.$refs.yellow;
      const green = this.$refs.green;
      const panels = [red, blue, yellow, green];
      return panels[parseInt(Math.random() * panels.length)];
    },
    async startFlashing() {
      this.canClick = false;
      for (const panel of this.sequence) {
        await this.flash(panel);
      }
      this.canClick = true;
      this.sequenceTuGuess = [...this.sequence];
    },
    flash(panel) {
      return new Promise((resolve) => {
        panel.classList.toggle("active");
        var audio = new Audio();
        audio.src = require(`@/assets/${panel.classList[0]}.mp3`);
        audio.autoplay = true;
        setTimeout(() => {
          panel.classList.toggle("active");
          setTimeout(() => {
            resolve();
          }, 250);
        }, this.time);
      });
    },
    start(time) {
      this.sequence = [this.getRandomPanel()];
      this.time = time;
      this.startFlashing();
      this.statrted = true;
    },
    clickHandler(a) {
      const red = this.$refs.red;
      const blue = this.$refs.blue;
      const yellow = this.$refs.yellow;
      const green = this.$refs.green;
      const panels = [red, blue, yellow, green];
      if (a == "add") {
        for (let i = 0; i < panels.length; i++) {
          panels[i].classList.add("on-click");
        }
      } else {
        for (let i = 0; i < panels.length; i++) {
          panels[i].classList.remove("on-click");
        }
      }
    },
    panelClicked(panelClicked, audioPath) {
      if (!this.canClick) return;
      var audio = new Audio();
      audio.src = require(`@/assets/${audioPath}`);
      audio.autoplay = true;
      const expectedPanel = this.sequenceTuGuess.shift();
      if (expectedPanel == panelClicked.path[0]) {
        if (this.sequenceTuGuess.length == 0) {
          this.sequence.push(this.getRandomPanel());
          return new Promise((resolve) => {
            this.canClick = false;
            setTimeout(() => {
              this.startFlashing();
              resolve();
            }, 1000);
          });
        }
      } else {
        alert("game over");
        this.sequence = [];
        this.canClick = false;
        this.statrted = false;
      }
    },
  },
};
</script>

<style>
.buttons {
  margin-left: 100px;
}
.container {
  margin-left: 30vw;
  margin-top: 10vw;
}
h1 {
  margin: 1em 0 2em;
  text-align: center;
}
.on-click:active {
  opacity: 1;
}

ul {
  list-style: none;
}

ul,
li {
  padding: 0;
  margin: 0;
}
.active {
  opacity: 1 !important;
}
.red,
.blue,
.yellow,
.green {
  height: 290px;
  -webkit-border-radius: 150px 150px 150px 150px;
  border-radius: 150px 150px 150px 150px;
  position: absolute;
}

.red:hover,
.blue:hover,
.yellow:hover,
.green:hover {
  border: 2px solid black;
}

.red {
  background: red;
  opacity: 0.3;
  clip: rect(0px, 300px, 150px, 150px);
  width: 296px;
}

.blue {
  background: blue;
  opacity: 0.3;
  clip: rect(0px, 150px, 150px, 0px);
  width: 300px;
}

.yellow {
  background: yellow;
  opacity: 0.3;
  clip: rect(150px, 150px, 300px, 0px);
  width: 300px;
}

.green {
  background: greenyellow;
  opacity: 0.3;
  clip: rect(150px, 300px, 300px, 150px);
  width: 296px;
}
</style>