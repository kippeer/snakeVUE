<template>
  <div class="game" @click="flap">
    <BirdSprite :y="birdY" />
    <PipeObstacle v-for="(pipe, index) in pipes" :key="index" :height="pipe.topHeight" :x="pipe.x" :top="true" />
    <PipeObstacle v-for="(pipe, index) in pipes" :key="index + pipes.length" :height="pipe.bottomHeight" :x="pipe.x" />
    <ScoreDisplay :score="score" />
  </div>
</template>

<script>
import BirdSprite from './BirdSprite.vue';
import PipeObstacle from './PipeObstacle.vue';
import ScoreDisplay from './ScoreDisplay.vue';

export default {
  name: 'GameScreen',
  components: {
    BirdSprite,
    PipeObstacle,
    ScoreDisplay
  },
  data() {
    return {
      birdY: 100,
      birdVelocity: 0.5,
      gravity: 0.7,
      flapStrength: -15,
      pipes: [],
      pipeGap: 450, // Espaço fixo entre os tubos superior e inferior
      pipeSpeed: 7,
      pipeInterval: 2000, // Intervalo entre a criação de pares de tubos
      score: 0,
      gameInterval: null,
      addPipeInterval: null
    };
  },
  methods: {
    flap() {
      this.birdVelocity = this.flapStrength;
    },
    addPipe() {
      const gapPosition = Math.floor(Math.random() * (window.innerHeight - this.pipeGap));
      const topHeight = gapPosition;
      const bottomHeight = window.innerHeight - gapPosition - this.pipeGap;
      const pipeX = window.innerWidth;
      this.pipes.push({
        topHeight: topHeight,
        bottomHeight: bottomHeight,
        x: pipeX,
        passed: false
      });
    },
    movePipes() {
      this.pipes = this.pipes.map(pipe => {
        pipe.x -= this.pipeSpeed;
        return pipe;
      }).filter(pipe => pipe.x > -50);
    },
    checkCollision() {
      if (this.birdY > window.innerHeight || this.birdY < 0) {
        this.resetGame();
      }
      this.pipes.forEach(pipe => {
        const birdTop = this.birdY;
        const birdBottom = this.birdY + 34; // Altura do sprite do pássaro
        const birdRight = 30; // Largura do sprite do pássaro
        const birdLeft = 10; // Largura do sprite do pássaro

        const pipeTop = pipe.topHeight;
        const pipeBottom = window.innerHeight - pipe.bottomHeight;
        const pipeLeft = pipe.x;
        const pipeRight = pipe.x + 52; // Largura do tubo

        const birdCollidedWithPipe = birdBottom > pipeTop && birdTop < pipeBottom && birdRight > pipeLeft && birdLeft < pipeRight;

        if (birdCollidedWithPipe) {
          this.resetGame();
        }

        if (!pipe.passed && pipe.x < 50 && pipe.x + 52 > 30) {
          this.score++;
          pipe.passed = true;
        }
      });
    },
    resetGame() {
      clearInterval(this.gameInterval);
      clearInterval(this.addPipeInterval);
      this.birdY = 200;
      this.birdVelocity = 0;
      this.pipes = [];
      this.score = 0;
      this.startGame();
    },
    startGame() {
      this.gameInterval = setInterval(() => {
        this.birdVelocity += this.gravity;
        this.birdY += this.birdVelocity;
        this.checkCollision();
        this.movePipes();
      }, 21);

      this.addPipeInterval = setInterval(() => {
        this.addPipe();
      }, this.pipeInterval);
    }
  },
  mounted() {
    this.startGame();
  },
  beforeUnmount() {
    clearInterval(this.gameInterval);
    clearInterval(this.addPipeInterval);
  }
};
</script>

<style src="../styles/FlappyBird.css"></style>
