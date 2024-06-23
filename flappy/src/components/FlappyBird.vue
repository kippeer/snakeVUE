<template>
  <div class="game" @click="flap">
    <div class="bird" :style="birdStyle"></div>
    <div v-for="(pipe, index) in pipes" :key="index" class="pipe" :style="pipe.style"></div>
    <div class="score">{{ score }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      birdY: 200,
      birdVelocity: 0,
      gravity: 0.6,
      flapStrength: -10,
      pipes: [],
      pipeWidth: 50,
      pipeGap: 150,
      pipeSpeed: 2,
      score: 0
    };
  },
  computed: {
    birdStyle() {
      return {
        top: `${this.birdY}px`
      };
    }
  },
  methods: {
    flap() {
      this.birdVelocity = this.flapStrength;
    },
    addPipe() {
      const pipeHeight = Math.floor(Math.random() * 200) + 50;
      this.pipes.push({
        height: pipeHeight,
        style: {
          height: `${pipeHeight}px`,
          width: `${this.pipeWidth}px`,
          left: `100%`
        },
        passed: false
      });
    },
    movePipes() {
      this.pipes = this.pipes.map(pipe => {
        pipe.style.left = `${parseFloat(pipe.style.left) - this.pipeSpeed}px`;
        return pipe;
      }).filter(pipe => parseFloat(pipe.style.left) > -this.pipeWidth);
    },
    checkCollision() {
      // Check for ground collision
      if (this.birdY > window.innerHeight) {
        this.resetGame();
      }
      // Check for pipe collision
      this.pipes.forEach(pipe => {
        const pipeLeft = parseFloat(pipe.style.left);
        if (pipeLeft < 50 && pipeLeft > 0 && this.birdY < pipe.height) {
          this.resetGame();
        }
        if (pipeLeft < 50 && pipeLeft > 0 && this.birdY > pipe.height + this.pipeGap) {
          this.resetGame();
        }
        if (!pipe.passed && pipeLeft < 50) {
          this.score++;
          pipe.passed = true;
        }
      });
    },
    resetGame() {
      this.birdY = 200;
      this.birdVelocity = 0;
      this.pipes = [];
      this.score = 0;
    }
  },
  mounted() {
    setInterval(() => {
      this.birdVelocity += this.gravity;
      this.birdY += this.birdVelocity;
      this.checkCollision();
      if (Math.random() < 0.02) {
        this.addPipe();
      }
      this.movePipes();
    }, 20);
  }
};
</script>

<style src="../styles/FlappyBird.css"></style>
