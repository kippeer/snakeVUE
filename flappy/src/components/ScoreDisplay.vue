<template>
  <div class="score">{{ score }}</div>
</template>

<script>
export default {
  props: {
    score: Number
  }
};
</script>

<style scoped>
.score {
  position: absolute;
  top: 20px;
  left: 20px;
  font-size: 24px;
  font-weight: bold;
}
</style>
