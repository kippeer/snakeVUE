<template>
  <div class="bird" :style="birdStyle"></div>
</template>

<script>
export default {
  name: 'BirdSprite',
  props: {
    y: Number
  },
  computed: {
    birdStyle() {
      return {
        top: `${this.y}px`
      };
    }
  }
};
</script>

<style scoped>
.bird {
  position: absolute;
  width: 50px;
  height: 50px;
  background: yellow;
  border-radius: 50%;
}
</style>
