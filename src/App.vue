<template>
  <div id="app">
    <h1>Simon Says</h1>
      <div class="container">
        <div class="square">
          <div @click="check" :ref="ids[0]" :id="ids[0]" class="btn blue"></div>
          <div @click="check" :ref="ids[1]" :id="ids[1]" class="btn red"></div>
          <div @click="check" :ref="ids[2]" :id="ids[2]" class="btn yellow"></div>
          <div @click="check" :ref="ids[3]" :id="ids[3]" class="btn green"></div>
        </div>
        <div class="menu">
          <h2 class="round">Round: {{round}}</h2>
          <button @click="start" :ref="ids[4]" class="start">Start</button>
          <div :ref="ids[5]" class="difficulty">
            <h3>Difficulty</h3>
            <input type="radio" id="easy" value="1500" v-model="timer">
            <label for="easy">Easy</label>
            <br>
            <input type="radio" id="normal" value="1000" v-model="timer">
            <label for="normal">Normal</label>
            <br>
            <input type="radio" id="hard" value="400" v-model="timer">
            <label for="hard">Hard</label>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      ids: ['0', '1', '2', '3', 'start', 'difficulty'],
      isStarted: false,
      isBlocked: false,
      sequence: [],
      timer: 1500,
      currentIndex: 0,
    }
  },
  methods: {
    start: function () {
      if (!this.isStarted) {
        this.settingsNullify();
        this.isStarted = true;
        this.isBlocked = true;
        this.continuePlay();
      } else if (!this.isBlocked){
        this.settingsNullify();
        this.start();
      }
    },
    continuePlay: function(){
      if (this.isBlocked){
        this.addRandom(this.sequence);
        if (this.sequence.length !== 1) {
          this.$refs['start'].classList.add('not-availaible');
        }
        this.sequence.forEach((el, index) => {
          setTimeout(() => {
            this.blink(el);
            if (index+1 === this.sequence.length) {
              this.isBlocked = false;
              this.$refs['start'].classList.remove('not-availaible');
            }
          }, index * this.timer);
        });
      }
    },
    check: function(e){
      this.playSound(e.target.id);
      if (!this.isBlocked && (e.target.id == this.sequence[this.currentIndex])) {
        if (this.currentIndex == (this.sequence.length-1)){
          this.currentIndex = 0;
          this.isBlocked = true;
          setTimeout(() => {
            this.continuePlay();
          }, 1000);
        } else {
          this.currentIndex++;
        }
      } else {
        if (this.isStarted){
          alert('You lost');
        }
        this.settingsNullify();
        this.$refs['start'].classList.remove('not-availaible');
      }
    },
    blink: function(ref) {
      const obj = Object.keys(this.$refs)[ref];
      this.$refs[ref].classList.add('blinked');
      this.playSound(ref);
      setTimeout(() => {
        this.$refs[ref].classList.remove('blinked');
      }, 200);
    },
    addRandom: function(array){
      array.push(Math.floor(Math.random() * 4));
    },
    settingsNullify: function(){
        this.currentIndex = 0;
        this.isStarted = false;
        this.isBlocked = false;
        this.sequence = [];
    },
    playSound: function(ref){
      let sound = new Audio(require(`./assets/sounds/${ref}.mp3`));
      sound.play();
    },
  },
  computed: {
    round: function() {
      return this.sequence.length;
    }
  },
}
</script>

<style lang="scss">
  $op: .6;

  .container {
    width: 350px;
    margin: auto;
    display: flex;
    justify-content: space-between;
  }
  .square {
    display: flex;
    flex-wrap: wrap;
    width: 200px;
    height: 200px;
  }

  h1 {
    text-align: center;
  }

  h3 {
    margin-bottom: 5px;
  }

  input {
    margin-left: 0;
  }

  .blinked {
    opacity: $op;
  }

  .btn {
    width: 100px;
    height: 100px;
    box-sizing: border-box;
    cursor: pointer;

    &:active {
      opacity: $op;
    }
  }
  .blue {
    background-color: #78bcff;
    border: 1px solid black;
  }
  .red {
    background-color: #ff9a8e;
    border-top: 1px solid black;
    border-right: 1px solid black;
    border-bottom: 1px solid black;
  }
  .yellow {
    background-color: #feef33;
    border-left: 1px solid black;
    border-bottom: 1px solid black;
    border-right: 1px solid black;
  }
  .green {
    background-color: #d8eb73;
    border-bottom: 1px solid black;
    border-right: 1px solid black;
  }

  .menu {
    display: flex;
    flex-direction: column;
  }

  .round {
    margin-top: 0;
    margin-bottom: 5px;
  }

  .difficulty {
    margin: 5px 0;
  }

  .start {
    height: 24px;
    background-color: gainsboro;
    border: 1px solid black;
    outline: 0;
    cursor: pointer;
    font-size: 13px;
    box-shadow: 0 0 2px lighten($color: black, $amount: 80%);

    &:active {
      padding: 0;
      box-shadow: 0 0;
    }
  }

  .not-availaible {
    text-decoration: line-through;
  }
  
</style>
