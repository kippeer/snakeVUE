<template>
  <div class="pipe" :style="pipeStyle"></div>
</template>

<script>
export default {
  name: 'PipeObstacle',
  props: {
    height: Number,
    x: Number,
    top: {
      type: Boolean,
      default: false
    }
  },
  computed: {
    pipeStyle() {
      return {
        height: `${this.height}px`,
        width: '50px',
        left: `${this.x}px`,
        position: 'absolute',
        bottom: this.top ? 'auto' : '0',
        top: this.top ? '0' : 'auto'
      };
    }
  }
};
</script>

<style scoped>
.pipe {
  background: green;
  
}
</style>
