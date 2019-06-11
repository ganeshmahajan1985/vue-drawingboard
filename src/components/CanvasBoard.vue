<template>
  <div class="canvasContainer">
    <div class="canvasContainer_layout">
      <div class="canvascontainer_buttons">
        <div class="canvascontainer_buttons__pencileraser">
          <button @click="Pencil">Pencil</button>
          <button @click="Eraser">Eraser</button>
        </div>
        <div class="canvascontainer_buttons__redoundo">
          <button @click="Undo">Undo</button>
          <button @click="Redo">Redo</button>
        </div>
      </div>
      <canvas
        class="contextCanvas"
        width="700"
        height="500"
        ref="canvas"
        id="drawing-pad"
      >This is a drawing pad for Incubit</canvas>
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
      points: []
    };
  },
  mounted() {
    const vm = this;
    vm.canvas = vm.$refs.canvas;
    vm.context = vm.canvas.getContext("2d");
    vm.canvas.addEventListener("mousedown", vm.mousedown);
    vm.canvas.addEventListener("mousemove", vm.mousemove);
    document.addEventListener("mouseup", vm.mouseup);
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
        y
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
        vm.context.lineCap = "round";
        vm.context.strokeStyle = "rgba(0,0,0,1)";
        vm.context.stroke();
        vm.startX = x;
        vm.startY = y;
        vm.points.push({
          x,
          y
        });
      }
    },
    mouseup(e) {
      const vm = this;
      vm.isDrawing = false;
      if (vm.points.length > 0) {
        localStorage.setItem("points", JSON.stringify(vm.points));
      }
    },
    Pencil() {
      return true;
    },
    Eraser() {
      return true;
    },
    Undo() {
      return true;
    },
    Redo() {
      return true;
    }
  }
};
</script>
<style scoped lang="scss" >
.canvasContainer {
  text-align: center;
  display: flex;
  flex-direction: column;
  .canvasContainer_layout {
    width: 700px;
    align-self: center;

    .canvascontainer_buttons {
      display: flex;
      button {
        height: 34px;
        width: 100px;
        margin: 3px;
        border-radius: 12px;
        outline: 0;
      }
      .canvascontainer_buttons__pencileraser {
        display: flex;
        button {
          background-color: blueviolet;
          color: white;
        }
      }
      .canvascontainer_buttons__redoundo {
        display: flex;
        margin-left: auto;
      }
    }
  }
  .contextCanvas {
    background-color: lightcyan;
  }
}
</style>

