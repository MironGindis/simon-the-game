<template>
  <div class="wrapper">
    <audio ref="sound1" src="https://s3.amazonaws.com/freecodecamp/simonSound1.mp3"></audio>
    <audio ref="sound2" src="https://s3.amazonaws.com/freecodecamp/simonSound2.mp3"></audio>
    <audio ref="sound3" src="https://s3.amazonaws.com/freecodecamp/simonSound3.mp3"></audio>
    <audio ref="sound4" src="https://s3.amazonaws.com/freecodecamp/simonSound4.mp3"></audio>
		<h1>Simon Says</h1>
			<div class="game-board">
				<div class="simon">
					<ul>
						<li @click='input(1)' class="tile red" :class="{highlight: hlRed}"></li>
						<li @click='input(2)' class="tile blue" :class="{highlight: hlBlue}"></li>
						<li @click='input(3)' class="tile yellow" :class="{highlight: hlYellow}"></li>
						<li @click='input(4)' class="tile green" :class="{highlight: hlGreen}"></li>
					</ul>
				</div>
			</div>
			<div class="game-info">
				<h2 >Round: {{count}}</h2>
				<button @click='startGame'>Start</button>
				<p v-if="isLost">Game Over! You lost  after {{count}} rounds!</p>
        <p v-if="isWon">Congratulations! You are winner!</p>
			</div>
			<div class="game-options">
				<h2>Game Options:</h2>
				<input type="radio" value='1500' v-model="checkboxValue" :disabled='isGameStarted' checked>Easy<br>
				<input type="radio" value=1000 v-model="checkboxValue" :disabled='isGameStarted'>Normal<br>
				<input type="radio" value=400 v-model="checkboxValue" :disabled='isGameStarted'>Hard<br>
			</div>
	</div>
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {  
        started: false,  
        count: 0,    
        series: [],      
        userInput: [],
        hlRed: false,
        hlGreen: false,
        hlYellow: false,
        hlBlue: false,
        allowInput: false,
        isWon: false,
        winCount : 10,
        isLost : false,
        isGameStarted : false,
        delay : 1500,
        checkboxValue : 1500,
      }
    },

    methods: {

      startGame() {
        this.isGameStarted = true;
        this.reset();
        this.generateSeries();
      },

      input(tone) {
        if(!this.allowInput) {
          return;
        }
        this.userInput.push(tone);
        this.playTone(tone);
      },

      clearHighlight(tone) {
        this[tone] = false;
      },

      playTone (tone) {
        switch (tone) {
          case 1:
            this.$refs.sound1.pause();
            this.$refs.sound1.currentTime = 0;
            this.$refs.sound1.play();
            this.hlRed = true;
            setTimeout(()=>this.clearHighlight('hlRed'), this.delay-100);
            
            break;
          case 2:
            this.$refs.sound2.pause();
            this.$refs.sound2.currentTime = 0;
            this.$refs.sound2.play();
            this.hlBlue = true;
            setTimeout(()=>this.clearHighlight('hlBlue'), this.delay-100);
            
            break;
          case 3:
            this.$refs.sound3.pause();
            this.$refs.sound3.currentTime = 0;
            this.$refs.sound3.play();
            this.hlYellow = true;
            setTimeout(()=>this.clearHighlight('hlYellow'), this.delay-100);
            break;
          case 4:
            this.$refs.sound4.pause();
            this.$refs.sound4.currentTime = 0;
            this.$refs.sound4.play();
            this.hlGreen = true;
            setTimeout(()=>this.clearHighlight('hlGreen'), this.delay-100);
            break;
        }
      },
      playSeries() {
      this.allowInput = false;
      let self = this
      let NewDelay = self.delay;
      this.series.forEach(function(tone, idx, arr) {
        if (idx === arr.length-1){
          setTimeout(function(){
            self.playTone(tone);
            self.allowInput = true;
          },NewDelay)
        } else {
          setTimeout(function() { self.playTone(tone) }, NewDelay)
        NewDelay += self.delay;
        }
      })
    },


      randomTone() {
      return Math.floor(Math.random() * 4) + 1;
    },

    generateSeries() {
      this.count ++;
      if (this.count > this.winCount ) {
        this.isWon = true;
        this.isGameStarted = false;
        return;
      }
      this.series.push(this.randomTone());
    },

    comparisonInputAndSeries() {
      if (this.userInput.length === 0) {
        return;
      }
      if (this.userInput[this.userInput.length-1] !== this.series[this.userInput.length-1]) {
        this.isLost = true;
        this.allowInput = false;
        this.isGameStarted = false;
        return;
      } 
      if (this.count === this.userInput.length){
        this.userInput = [];
        this.generateSeries()
      }
      
    },

    reset() {
      this.count = 0;
      this.series = [];
      this.userInput = [];
      this.allowInput = false;
      this.hlGreen = false;
      this.hlRed = false;
      this.hlYellow = false;
      this.hlBlue = false;
      this.isLost = false;
    },
    },

    watch : {
      series : {
        handler() {
          this.playSeries();
        },
        deep: true,
      },

    userInput : {
      handler() {
        this.comparisonInputAndSeries()
      },
      deep: true,
    },

    checkboxValue() {
      this.delay = this.checkboxValue/1;
    }

    }

  }
</script>

 
<style src='./app.css'>

</style>
