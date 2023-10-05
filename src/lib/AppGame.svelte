<script lang="ts">
  import { onMount } from "svelte";

  const [width, height] = [700, 400];
  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;
  $: [chX, chY] = [10, height - 10];
  $: character = {
    x: chX,
    y: chY,
    height: 10,
    width: 10,
    speed: 10,
    draw: () => {
      ctx.beginPath();
      ctx.rect(character.x, character.y, character.width, character.height);
      ctx.fillStyle = "red";
      ctx.fill();
      ctx.closePath();
    },
    move: (e: any) => {
      handleKeyPress(e);
    },
  };

  function handleKeyPress(
    e: KeyboardEvent & {
      currentTarget: EventTarget & HTMLCanvasElement;
    }
  ) {
    const key = e.key;
    switch (key) {
      case "d":
        chX += character.speed;
        break;
      case "a":
        chX -= character.speed;
        break;
      case " ":
        // JUMP
        console.log("JUMP");
        break;
      default:
        console.log(`${key} key not supported`);
        break;
    }
  }

  function render() {
    ctx.clearRect(0, 0, width, height);
    character.draw();
    requestAnimationFrame(render);
  }
  onMount(() => {
    canvas.focus();
    ctx = canvas.getContext("2d")!;
    render();
  });
</script>

<canvas
  tabindex="0"
  bind:this={canvas}
  {width}
  {height}
  on:keypress={(e) => character.move(e)}
/>

<style>
  canvas {
    outline: 1px solid;
  }
</style>
