<template>
  <div id="app">
    <div class="header">
      <h1>Jogo da Cobrinha</h1>
    </div>
    <div class="game-container">
      <div class="sidebar">
        <div class="decoration"></div>
        <div class="decoration"></div>
        <div class="decoration"></div>
      </div>
      <div class="game-board">
        <canvas ref="gameCanvas" width="800" height="600"></canvas>
        
      </div>
      <div class="sidebar">
        <div class="decoration"></div>
        <div class="decoration"></div>
        <div class="decoration"></div>
      </div>
    </div>

    <!-- Estilo do placar -->
    <div class="scoreboard">
      <div class="score-label">Pontuação:</div>
      <div class="score-value">{{ score }}</div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      snake: [
        { x: 10, y: 10 },
        { x: 10, y: 11 },
        { x: 10, y: 12 }
      ],
      direction: 'UP',
      apple: { x: 15, y: 15 },
      score: 0,
      cellSize: 20,
      intervalId: null
    };
  },
  mounted() {
    this.intervalId = setInterval(this.gameLoop, 100);
    window.addEventListener('keydown', this.changeDirection);
  },
  beforeDestroy() {
    clearInterval(this.intervalId);
    window.removeEventListener('keydown', this.changeDirection);
  },
  methods: {
    gameLoop() {
      this.moveSnake();
      this.checkCollision();
      this.checkApple();
      this.drawGame();
    },
    moveSnake() {
      let head = { ...this.snake[0] };

      switch (this.direction) {
        case 'UP':
          head.y -= 1;
          break;
        case 'DOWN':
          head.y += 1;
          break;
        case 'LEFT':
          head.x -= 1;
          break;
        case 'RIGHT':
          head.x += 1;
          break;
      }

      this.snake.unshift(head);
      if (!this.checkAppleCollision()) {
        this.snake.pop();
      }
    },
    changeDirection(event) {
      const key = event.keyCode;
      const directions = {
        37: 'LEFT',
        38: 'UP',
        39: 'RIGHT',
        40: 'DOWN'
      };
      const newDirection = directions[key];
      const oppositeDirections = {
        'UP': 'DOWN',
        'DOWN': 'UP',
        'LEFT': 'RIGHT',
        'RIGHT': 'LEFT'
      };

      if (newDirection && newDirection !== oppositeDirections[this.direction]) {
        this.direction = newDirection;
      }
    },
    checkCollision() {
      const head = this.snake[0];
      if (
        head.x < 0 ||
        head.x >= this.$refs.gameCanvas.width / this.cellSize ||
        head.y < 0 ||
        head.y >= this.$refs.gameCanvas.height / this.cellSize ||
        this.snake.slice(1).some(segment => segment.x === head.x && segment.y === head.y)
      ) {
        alert('Game Over');
        this.resetGame();
      }
    },
    checkApple() {
      if (this.checkAppleCollision()) {
        this.score += 10;
        this.apple = this.placeApple();
      }
    },
    checkAppleCollision() {
      const head = this.snake[0];
      return head.x === this.apple.x && head.y === this.apple.y;
    },
    placeApple() {
      const width = this.$refs.gameCanvas.width / this.cellSize;
      const height = this.$refs.gameCanvas.height / this.cellSize;
      let newApple;
      do {
        newApple = {
          x: Math.floor(Math.random() * width),
          y: Math.floor(Math.random() * height)
        };
      } while (this.snake.some(segment => segment.x === newApple.x && segment.y === newApple.y));
      return newApple;
    },
    drawGame() {
      const context = this.$refs.gameCanvas.getContext('2d');
      context.clearRect(0, 0, this.$refs.gameCanvas.width, this.$refs.gameCanvas.height);

      // Draw snake
      context.fillStyle = 'green';
      this.snake.forEach(segment => {
        context.fillRect(segment.x * this.cellSize, segment.y * this.cellSize, this.cellSize, this.cellSize);
      });

      // Draw apple
      context.fillStyle = 'red';
      context.fillRect(this.apple.x * this.cellSize, this.apple.y * this.cellSize, this.cellSize, this.cellSize);
    },
    resetGame() {
      this.snake = [
        { x: 10, y: 10 },
        { x: 10, y: 11 },
        { x: 10, y: 12 }
      ];
      this.direction = 'UP';
      this.apple = this.placeApple();
      this.score = 0;
    }
  }
};
</script>

<style scoped src="./App.css"></style>

<!-- Estilo do placar -->
