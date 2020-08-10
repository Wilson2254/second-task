<template>
  <div class="main">
    <div>Саймон говорит</div>
    <div v-if="gameOver" class="loseGame">Вы проиграли</div>
    <div>Раунд: {{this.rounds}} </div>
    <div class="fields">
      <div
        :class="{one_field:true, field_choose:this.queue[queue.length-1] == 1, stop_click: isPlay}"
        @click.prevent="changeField"
      ></div>
      <div
        :class="{two_field:true, field_choose:this.queue[queue.length-1] == 2, stop_click: isPlay}"
        @click="changeField"
      ></div>
      <div
        :class="{three_field:true, field_choose:this.queue[queue.length-1] == 3, stop_click: isPlay}"
        @click="changeField"
      ></div>
      <div
        :class="{four_field:true, field_choose:this.queue[queue.length-1] == 4, stop_click: isPlay}"
        @click="changeField"
      ></div>
    </div>
    <div class="controls">
      <a class="button8" @click="startGame" :class="{isGameNow: isGame}">Старт</a>
      <div>Режим: {{getDiff}}</div>
      <div class="mode">
        <form :class="{isGameNow: isGame}">
          <input type="radio" value="1" name="modeChoose" v-model="mode" />
          <label for="lightMode">Легкий</label>

          <input type="radio" value="2" name="modeChoose" v-model="mode" />
          <label for="mediumMode">Средний</label>

          <input type="radio" value="3" name="modeChoose" v-model="mode" />
          <label for="hardMode">Сложный</label>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rounds: 0,
      mode: 1,
      queue: [],
      supportQueue: [Math.floor(Math.random() * (4 - 1 + 1)) + 1],
      isGame: false,
      isPlay: false,
      sounds: {
        one: require("../sounds/1.mp3"),
        two: require("../sounds/2.mp3"),
        three: require("../sounds/3.mp3"),
        four: require("../sounds/4.mp3"),
      },
      gameOver: false
    };
  },
  methods: {
    
    startGame() {
      this.gameOver = false;
      this.isGame = true;
      this.isPlay = true;
      this.rounds++;
      setTimeout(() => this.playGame(), this.mode == 1 ? 1500  : this.mode == 2 ? 1000  : 400);
    },

    playGame() {
      let firstElem = this.supportQueue[0];
      const some = this;
      for (let i = 0; i < some.rounds; i++) {
        new Promise(function (resolve) {
          setTimeout(
            () => resolve(some.queue.push(some.supportQueue.shift())),
            some.mode == 1 ? 1500 * i : some.mode == 2 ? 1000 * i : 400 * i
          );
        })
          .then(function () {
            if (some.queue[i] == 1) new Audio(some.sounds.one).play();
            if (some.queue[i] == 2) new Audio(some.sounds.two).play();
            if (some.queue[i] == 3) new Audio(some.sounds.three).play();
            if (some.queue[i] == 4) new Audio(some.sounds.four).play();
            setTimeout(
              () => some.queue.push("trash"),
              some.mode == 1 ? 700 : some.mode == 2 ? 400 : 200
            );
            if (i == some.rounds - 1) {
              some.queue.splice(0, 0, firstElem);
              some.isPlay = false;
            }
          })
          .then(function () {
            if (some.queue.length == 1) {
              setTimeout(
                () => some.queue.splice(some.queue.indexOf("trash"), 1),
                some.mode == 1 ? 650 : some.mode == 2 ? 350 : 150
              );
            } else setTimeout(() => some.queue.splice(some.queue.indexOf("trash"), 1));
          });
      }
    },

    changeField(e) {
      var el = e.target;

      if (this.queue.length) {
        if (el.className == "one_field") {
          new Audio(this.sounds.one).play();
          this.supportQueue.push(this.queue.shift());
          this.supportQueue[this.supportQueue.length - 1] == 1
            ? this.nextColor()
            : this.loseGame();
        }

        if (el.className == "two_field") {
          new Audio(this.sounds.two).play();
          this.supportQueue.push(this.queue.shift());
          this.supportQueue[this.supportQueue.length - 1] == 2
            ? this.nextColor()
            : this.loseGame();
        }

        if (el.className == "three_field") {
          new Audio(this.sounds.three).play();
          this.supportQueue.push(this.queue.shift());

          this.supportQueue[this.supportQueue.length - 1] == 3
            ? this.nextColor()
            : this.loseGame();
        }

        if (el.className == "four_field") {
          new Audio(this.sounds.four).play();
          this.supportQueue.push(this.queue.shift());
          this.supportQueue[this.supportQueue.length - 1] == 4
            ? this.nextColor()
            : this.loseGame();
        }
        
      }
    },

    nextColor() {
      if (this.queue[0] == "trash") {
        this.supportQueue.push(Math.floor(Math.random() * (4 - 1 + 1)) + 1);
        this.queue.shift();
        this.startGame();
      }
    },

    loseGame() {
      this.supportQueue = [];
      this.queue = [];
      this.supportQueue.push(Math.floor(Math.random() * (4 - 1 + 1)) + 1);
      this.rounds = 0;
      this.isGame = false;
      this.isPlay = false;
      this.gameOver = true;
    },
  },
  computed: {
    getDiff: function () {
      if (this.mode == 1) return "Легкий";
      if (this.mode == 2) return "Средний";
      if (this.mode == 3) return "Сложный";
      return true;
    },
  },
};
</script>

<style scoped>
.main {
  display: flex;
  justify-content: center;
  flex-direction: column;
  align-items: center;
}

div{
  font-size: 20pt;
  margin-top: 10px;
}

.fields {
  display: flex;
  flex-wrap: wrap;
  width: 310px;
}

.one_field,
.two_field,
.three_field,
.four_field {
  flex: 1 1 calc(33.33%); /* отнимем margin и скажем растягиваться */
  margin: 1px;
  box-sizing: border-box; /* чтобы внутренний отступ не влиял когда там будет текст... */
  width: 150px;
  height: 150px;
  border: 1px solid;
}

.one_field {
  border-top-left-radius: 100%;
  background-color: #1e90ff;
  opacity: 0.6;
}

.two_field {
  border-top-right-radius: 100%;
  background-color: #ff5643;
  opacity: 0.6;
}

.three_field {
  border-bottom-left-radius: 100%;
  background-color: #feef33;
  opacity: 0.6;
}

.four_field {
  border-bottom-right-radius: 100%;
  background-color: #bede15;
  opacity: 0.6;
}

form{
  font-size: 14pt;
}

.stop_click {
  pointer-events: none;
}

.isGameNow{
  pointer-events: none;
  opacity: 0.4;
}

.loseGame{
  color: red;
  font-size: 20pt;
}

.one_field:active,
.two_field:active,
.three_field:active,
.four_field:active,
.field_choose {
  opacity: 1;
}

.one_field:hover,
.two_field:hover,
.three_field:hover,
.four_field:hover {
  cursor: pointer;
  border-color: gray;
}

.controls {
  margin-top: 20px;
  text-align: center;
}

a.button8 {
  display: inline-block;
  font-weight: 700;
  text-decoration: none;
  user-select: none;
  padding: 0.5em 2em;
  outline: none;
  border: 2px solid;
  border-radius: 1px;
  transition: 0.2s;
  cursor: pointer;
}
a.button8:hover {
  opacity: 0.8;
}
a.button8:active {
  background: gray;
  border-color: black;
  color: white;
}
</style>