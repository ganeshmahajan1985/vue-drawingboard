<template>
  <div class='canvasContainer'>
    <div class='canvasContainer_layout'>
      <div class='canvascontainer_buttons'>
        <div class='canvascontainer_buttons__pencileraser'>
          <button @click='Pencil'>Pencil</button>
          <button @click='Eraser'>Eraser</button>
        </div>
        <div class='canvascontainer_buttons__redoundo'>
          <button @click='Undo'>Undo</button>
          <button @click='Redo'>Redo</button>
        </div>
      </div>
      <canvas
        width='700'
        height='500'
        ref='canvas'
        id='drawing-pad'
         v-on:mousedown="onMouseDown"
          v-on:mouseup="onMouseUp"
          v-on:mousemove="onMouseMove"

      >This is a drawing pad for Incubit</canvas>
       <div ref="cursor" class="cursor" :style="cursorStyle"></div>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      isDrawing: false,
      startX: 0,
      startY: 0,
      points: [],
      canvasTop: 0,
      canvasLeft: 0,
      lineWidth: 10,
      isEraser: false,
      selectedColor: '#000000',
      colors: [
        '#000000',
        '#FFDD57',
        '#4286f4',
        '#23d160',
        '#FF8600',
        '#ff3860',
      ],
    };
  },
  mounted() {
    const vm = this;
    vm.canvas = vm.$refs.canvas;
    vm.context = vm.canvas.getContext('2d');
    // vm.canvas.addEventListener('mousedown', vm.mousedown);
    // vm.canvas.addEventListener('mousemove', vm.mousemove);
    // document.addEventListener('mouseup', vm.mouseup);
  },
  computed: {
    cursorStyle() {
      return {
        width: `${this.lineWidth}px`,
        height: `${this.lineWidth}px`,
        background: this.selectedColor,
      };
    },
  },
  methods: {
    draw(e) {
      if (!this.isDrawing) {
        return;
      }

      const {canvas, ctx} = this.getCanvas();

      ctx.lineJoin = 'round';
      ctx.lineCap = 'round';
      ctx.strokeStyle = this.selectedColor;
      ctx.lineWidth = this.lineWidth;

      ctx.beginPath();

      // start from
      ctx.moveTo(this.startX, this.startY);

      // go to
      ctx.lineTo(e.offsetX, e.offsetY);
      ctx.stroke();

      // 1. Update start point to where mouse is unpressed
      //    Otherwise it'd be always 0,0
      [this.startX, this.startY] = [e.offsetX, e.offsetY];
    },
    onMouseDown(e) {
      this.isDrawing = true;
      // 2. set the drawing point to where the mouse down is pressed
      [this.startX, this.startY] = [e.offsetX, e.offsetY];
    },
    onMouseMove(e) {
      this.draw(e);
      // debugger
      const x = e.x + this.canvasLeft;
      const y = e.y + this.canvasTop;
      const cursor = this.$refs.cursor;

      cursor.style.transform = `translate(${x}px, ${y}px)`;
    },
    onMouseUp(e) {
      const vm = this;
      vm.isDrawing = false;
      if (vm.points.length > 0) {
        localStorage.setItem('points', JSON.stringify(vm.points));
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
    },
    getCanvas() {
      const canvas = this.$refs.canvas;
      const ctx = canvas.getContext('2d');
      return { canvas, ctx };
    },
  },
};
</script>
<style scoped lang='scss' >
.canvasContainer {
  text-align: center;
  display: flex;
  flex-direction: column;
canvas {
  background: var(--color-white);
  cursor: none;
  border: 1px solid black;
}

  canvas:hover + .cursor {
  opacity: 1;
}
canvas:active + .cursor {
  border-color: transparent;
}
.cursor {
  position: fixed;
  top: 0;
  left: 0;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 3px solid rgb(200, 200, 200);
  padding: 2px;
  pointer-events: none;
  user-select: none;
  opacity: 0;
  transition: opacity 100ms;
}

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

