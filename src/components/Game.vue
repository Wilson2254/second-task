<template>
  <div class="main">

    <div>Саймон говорит</div>

    <!-- Вывожу сообщение если игра закончена -->
    <div v-if="gameOver" class="loseGame">Вы проиграли</div>

    <!-- Текущий рануд -->
    <div>Раунд: {{this.rounds}} </div>

     <!-- Цветные поля -->
    <div class="fields">

      <!-- На каждом поле стоят несколько классов -->
      <!-- Первый класс отвечает за расположение и цвет (он есть всегда) -->
      <!-- Второй класс подствечивает последнее загоревшееся поле (послдений элемент массива) -->
      <!-- Третий класс запрещает пользователю кликать на поля, пока они проигрываются -->

      <!-- Первое поле -->
      <div
        :class="{one_field: true, field_choose:this.queue[queue.length-1] == 1, stop_click: isPlay}"
        @click.prevent="changeField"
      ></div>

      <!-- Второе поле -->
      <div
        :class="{two_field: true, field_choose:this.queue[queue.length-1] == 2, stop_click: isPlay}"
        @click="changeField"
      ></div>

      <!-- Третье поле -->
      <div
        :class="{three_field: true, field_choose:this.queue[queue.length-1] == 3, stop_click: isPlay}"
        @click="changeField"
      ></div>

      <!-- Четвертое поле -->
      <div
        :class="{four_field: true, field_choose:this.queue[queue.length-1] == 4, stop_click: isPlay}"
        @click="changeField"
      ></div>

    </div>

    <!-- Блок выбора/показа сложности и кнопка, которая заускает игру -->
    <div class="controls">

      <!-- Старт игры, класс запрещает с ней взаимодействовать, пока идет игра -->
      <a class="button8" @click="startGame" :class="{isGameNow: isGame}">Старт</a>

      <!-- Показ текущей сложности (вычисляемое свойство, которое число переводит в строку) -->
      <div>Режим: {{getDiff}}</div>

      <!-- Форма с кнопками для выбора сложности, класс запрещает с ней взаимодействовать, пока идет игра -->
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
      rounds: 0, //Текущий раунд
      mode: 1, //Сложность игры
      queue: [], //Массив, в котором находится порядок номеров полей
      supportQueue: [Math.floor(Math.random() * (4 - 1 + 1)) + 1], //Вспомогательный массив для генерации следущего рандомного числа и для повторного проигрывания номеров полей
      isGame: false, //Отключает кнопку Старт и выбор уровней сложности, пока идет игра
      isPlay: false, //Отключает поля, когда они проигрываются
      sounds: { //Звуки для проигрывания полей
        one: require("../sounds/1.mp3"),
        two: require("../sounds/2.mp3"),
        three: require("../sounds/3.mp3"),
        four: require("../sounds/4.mp3"),
      },
      gameOver: false //Поле для отображние поражения
    };
  },
  methods: {
    
    //Функция вызывающаяся по кнопке Старт
    startGame() {
      this.gameOver = false; //Отключаем отображние поражения
      this.isGame = true; //Отключаем возможность кликать по кнопке Старт и по кнопкам выбора сложности
      this.isPlay = true; //Отключаем возможность кликать по полям, пока они проигрываются
      this.rounds++; //Новый раунд
      setTimeout(() => this.playGame(), this.mode == 1 ? 1500  : this.mode == 2 ? 1000  : 400); //Вызываю функции раунда, в котором первое поле подсвечиваю с задержкой (в зависимости от сложности)
    },

    // Функция раунда, в котором загораются поля
    playGame() {
      let firstElem = this.supportQueue[0]; //Запоминаю первый элемент (потом вставлю его в начало массива, так как он затиратся)
      const some = this; //Создаю текущий this так как контекст у анонимной функции будет другим
      // Запускаю цикл, в котором буду подсвечивать по очереди из массива поля rounds раз
      for (let i = 0; i < some.rounds; i++) { 
        // Делаю промис, для создания анимации проигрывания
        new Promise(function (resolve) {
          setTimeout(
            () => resolve(some.queue.push(some.supportQueue.shift())), // Через промежуток времени достаю из вспомогательного массива первый элемент и кладу его в основной
            // Благодаря этому поля подсвечиваются в зависимости от номера, который последний находится в массиве
            some.mode == 1 ? 1500 * i : some.mode == 2 ? 1000 * i : 400 * i //Задержка вывода следующего цвета зависит от сложности
          );
        })

          // Блок для проигрыванию звука поля и для мигания поля
          .then(function () {
            // Проигрываю звук поля
            if (some.queue[i] == 1) new Audio(some.sounds.one).play();
            if (some.queue[i] == 2) new Audio(some.sounds.two).play();
            if (some.queue[i] == 3) new Audio(some.sounds.three).play();
            if (some.queue[i] == 4) new Audio(some.sounds.four).play();
            // Вызываю таймер в который пушу любое значение кроме 1-4 (у меня trash)
            // Это для того, чтобы если будет повтор того же поля, можно было увидеть как оно загорается повторно
            setTimeout(
              () => some.queue.push("trash"),
              some.mode == 1 ? 700 : some.mode == 2 ? 400 : 200
            );
            // Как только цикл прошел, я ставлю в начало первый элемент, который запомнил вначале (так как первый раз он затирается моим trash)
            if (i == some.rounds - 1) {
              some.queue.splice(0, 0, firstElem); //Вставляю в начало элемент
              some.isPlay = false; //Теперь пользователь может протыкать порядок полей
            }
          })
          
          // Блок для создании анимации первого элемента, а также для удаления trash
          .then(function () {
            if (some.queue.length == 1) {
              setTimeout(
                () => some.queue.splice(some.queue.indexOf("trash"), 1), //Создаю эффект "мигания" для первого элемента
                some.mode == 1 ? 650 : some.mode == 2 ? 350 : 150 //Задержка мигания зависит от сложности
              );
            } else setTimeout(() => some.queue.splice(some.queue.indexOf("trash"), 1)); //Каждый раз удаляю trash из массива, чтобы он не копился
          });
      }
    },

    // Проклик полей
    changeField(e) {
      var el = e.target;

      // Сморим началась ли игра
      if (this.isGame) {

        if (el.className == "one_field") {
          new Audio(this.sounds.one).play(); // Проигрываем характерный звук при нажатии на поле
          this.supportQueue.push(this.queue.shift()); // Вытаскиваю первый элемент из основго массива и калду в дополнительный
          this.supportQueue[this.supportQueue.length - 1] == 1 //Вызываю функции в зависимости от правильности нажатия на поле
            ? this.nextColor()
            : this.loseGame();
        }

        //То же самое
        if (el.className == "two_field") {
          new Audio(this.sounds.two).play();
          this.supportQueue.push(this.queue.shift());
          this.supportQueue[this.supportQueue.length - 1] == 2
            ? this.nextColor()
            : this.loseGame();
        }

        //То же самое
        if (el.className == "three_field") {
          new Audio(this.sounds.three).play();
          this.supportQueue.push(this.queue.shift());

          this.supportQueue[this.supportQueue.length - 1] == 3
            ? this.nextColor()
            : this.loseGame();
        }

        //То же самое
        if (el.className == "four_field") {
          new Audio(this.sounds.four).play();
          this.supportQueue.push(this.queue.shift());
          this.supportQueue[this.supportQueue.length - 1] == 4
            ? this.nextColor()
            : this.loseGame();
        }
        
      }
    },

    // Функция оконачания нажатий
    nextColor() {
      if (this.queue[0] == "trash") { //Если дошли до trash (он последний был в основном массиве)
        this.supportQueue.push(Math.floor(Math.random() * (4 - 1 + 1)) + 1); //Генерю случаное значение, чтобы потом его добавить уже к проигранным полям
        this.queue.shift(); //Удаляю trash
        this.startGame();  //Запускаю проигрышь полей заново
      }
    },

    // Функция проигрыша
    loseGame() {
      // Очищаю все массивы и генерю случайное значение для первого поля
      this.supportQueue = [];
      this.queue = [];
      this.supportQueue.push(Math.floor(Math.random() * (4 - 1 + 1)) + 1);
      this.rounds = 0;
      this.isGame = false;
      this.isPlay = false;
      this.gameOver = true;
    },
  },

  // В зависимости от номера сложности вывожу его название
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

/* Расположение полей */
.one_field,
.two_field,
.three_field,
.four_field {
  flex: 1 1 calc(33.33%); 
  margin: 1px;
  box-sizing: border-box; 
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

/* Запрет на клик */
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

/* Подсетвка при клике */
.one_field:active,
.two_field:active,
.three_field:active,
.four_field:active,
.field_choose {
  opacity: 1;
}

/* При наведении */
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

/* Кнопка старт */
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