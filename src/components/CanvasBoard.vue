<template>
  <div>
    <canvas
      ref='canvas'
      id='drawing-pad'
      width='300'
      height='300'
    >This is a drawing pad for Incubit</canvas>
    <div>
      <button @click='resetCanvas'>Reset Canvas</button>
      <button @click='saveImage'>Save Image</button>
      <button @click='replay'>Replay</button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      canvas: null,
      context: null,
      isDrawing: false,
      startX: 0,
      startY: 0,
      points: [],
    };
  },
  mounted() {
    const vm = this;
    vm.canvas = vm.$refs.canvas;
    vm.context = vm.canvas.getContext('2d');
    vm.canvas.addEventListener('mousedown', vm.mousedown);
    vm.canvas.addEventListener('mousemove', vm.mousemove);
    document.addEventListener('mouseup', vm.mouseup);
  },
  methods: {
    mousedown(e) {
      const vm = this;
      const rect = vm.canvas.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;
      vm.isDrawing = true;
      vm.startX = x;
      vm.startY = y;
      vm.points.push({
        x,
        y,
      });
    },
    mousemove(e) {
      const vm = this;
      const rect = vm.canvas.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;
      if (vm.isDrawing) {
        vm.context.beginPath();
        vm.context.moveTo(vm.startX, vm.startY);
        vm.context.lineTo(x, y);
        vm.context.lineWidth = 1;
        vm.context.lineCap = 'round';
        vm.context.strokeStyle = 'rgba(0,0,0,1)';
        vm.context.stroke();
        vm.startX = x;
        vm.startY = y;
        vm.points.push({
          x,
          y,
        });
      }
    },
    mouseup(e) {
      const vm = this;
      vm.isDrawing = false;
      if (vm.points.length > 0) {
        localStorage.setItem('points', JSON.stringify(vm.points));
      }
    },
  },
};
</script>

