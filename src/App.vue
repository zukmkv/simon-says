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
        <button @click="start" :ref="ids[4]" class="start">Start</button>
      </div>
      <div class="array">{{sequence}}</div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      ids: ['0', '1', '2', '3', 'start'],
      isStarted: false,
      isBlocked: false,
      sequence: [],
      timer: 1000,
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
        this.$refs['start'].classList.add('not-availaible');
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
          alert('you lost');
        }
        this.settingsNullify();
      }
    },
    blink: function(ref) {
      const obj = Object.keys(this.$refs)[ref];
      this.$refs[ref].classList.add('blinked');
      setTimeout(() => {
        this.$refs[ref].classList.remove('blinked');
      }, 200);
    },
    getRandom: function(){
      return Math.floor(Math.random() * 4);
    },
    addRandom: function(array){
      array.push(this.getRandom());
    },
    settingsNullify: function(){
        this.currentIndex = 0;
        this.isStarted = false;
        this.isBlocked = false;
        this.sequence = [];
    },
  },
}
</script>

<style lang="scss">
  $op: .6;

  .container {
    width: 300px;
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

  .start {
    height: 24px;
  }

  .not-availaible {
    text-decoration: line-through;
  }
  
</style>
