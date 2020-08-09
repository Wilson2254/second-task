<template>
  <div class="main">
    <h1>Раунд: {{this.rounds}}</h1>
    <div class="fields">
      <div
        :class="{one_field:true, field_choose:this.queue[queue.length-1] == 1}"
        @click.prevent="changeField"
      ></div>
      <div
        :class="{two_field:true,field_choose:this.queue[queue.length-1] == 2}"
        @click="changeField"
      ></div>
      <div
        :class="{three_field:true, field_choose:this.queue[queue.length-1] == 3}"
        @click="changeField"
      ></div>
      <div
        :class="{four_field:true, field_choose:this.queue[queue.length-1] == 4}"
        @click="changeField"
      ></div>
    </div>
    <div class="controls">
      <a class="button8" @click="startGame" :class="{stop_click: isGame}">Старт</a>
      <h2>Режим: {{this.mode}}</h2>
      <div class="mode">
        <form>
          <input type="radio" value="Легкий" name="modeChoose" v-model="mode" />
          <label for="lightMode">Легкий</label>

          <input type="radio" value="Средний" name="modeChoose" v-model="mode" />
          <label for="mediumMode">Средний</label>

          <input type="radio" value="Сложный" name="modeChoose" v-model="mode" />
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
      mode: "Легкий",
      queue: [],
      isGame: false,
      isPlay: false,
    };
  },
  methods: {
    startGame() {
      this.isGame = true;
      this.rounds++;
      this.playGame();
    },
    playGame(min = 1, max = 4) {
      let firstElem;
      let track = new Audio();
      track.pause();
      const some = this;
      for (let i = 0; i < 5; i++) {
        new Promise(function (resolve) {
          setTimeout(
            () =>
              resolve(
                some.queue.push(
                  Math.floor(Math.random() * (max - min + 1)) + min
                )
              ),
            1400 * i
          );
        })
          .then(function () {
            setTimeout(() => some.queue.push("trash"), 500);
            console.log(some.queue);
            if (i == 4) {
              some.queue.splice(0, 0, firstElem);
            }
          })
          .then(function () {
            if (some.queue.length == 1) {
              firstElem = some.queue[0];
              setTimeout(
                () => some.queue.splice(some.queue.indexOf("trash"), 1),
                450
              );
            } else setTimeout(() => some.queue.splice(some.queue.indexOf("trash"), 1));
          })
          .then(function () {
            track = new Audio(
              "http://soundbible.com/mp3/Air Plane Ding-SoundBible.com-496729130.mp3"
            );
            track.play();
          });
      }
    },
    changeField(e) {
      var el = e.target;
      console.log(el.className);
      // Просто смотрю что выбрал и меняю шаг
      if (el.className == "one_field") {
        let track = new Audio(
          "http://soundbible.com/mp3/Air Plane Ding-SoundBible.com-496729130.mp3"
        );
        track.play();
        console.log(this.queue);
      }
      if (el.className == "two_field") {
        let track = new Audio(
          "http://soundbible.com/mp3/Computer Error Alert-SoundBible.com-783113881.mp3"
        );
        track.play();
      }
      if (el.className == "three_field") {
        let track = new Audio(
          "http://soundbible.com/mp3/Electronic_Chime-KevanGC-495939803.mp3"
        );
        track.play();
      }
      if (el.className == "four_field") {
        let track = new Audio(
          "http://soundbible.com/mp3/Elevator Ding-SoundBible.com-685385892.mp3"
        );
        track.play();
      }
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

/* .stop_click {
  pointer-events: none;
} */

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