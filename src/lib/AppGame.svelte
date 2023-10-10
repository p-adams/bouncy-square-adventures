<script lang="ts">
  import { onMount } from "svelte";
  const [width, height] = [700, 400];
  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;
  let velocityY = 0; // Vertical velocity
  const gravity = 1; // Gravity value
  $: [chX, chY] = [10, height - 10];

  $: platforms = [
    { x: 50, y: height / 2 + 120, width: 150, height: 20 },
  ] as Array<{
    x: number;
    y: number;
    width: number;
    height: number;
  }>;

  $: character = {
    x: chX,
    y: chY,
    height: 10,
    width: 10,
    speed: 10,
  };

  function createPlatforms(count = 1) {
    platforms.push({
      x: width / 2 - 50,
      y: height / 2 + 50,
      width: 150,
      height: 20,
    });
  }

  function drawPlatform() {
    for (const { x, y, width, height } of platforms) {
      ctx.beginPath();
      ctx.rect(x, y, width, height);
      ctx.fillStyle = "green";
      ctx.fill();
      ctx.closePath();
    }
  }

  function drawCharacter() {
    ctx.beginPath();
    ctx.rect(character.x, character.y, character.width, character.height);
    ctx.fillStyle = "red";
    ctx.fill();
    ctx.closePath();
    velocityY += gravity;

    // Update the character's vertical position based on velocity
    chY += velocityY;

    // Ensure the character doesn't go below the ground level
    if (chY > height - character.height) {
      chY = height - character.height;
      velocityY = 0; // Reset the vertical velocity
    }
  }

  function handleKeyPress(key: string) {
    switch (key) {
      case "d":
        chX += character.speed;
        break;
      case "a":
        chX -= character.speed;
        break;
      case " ":
        // Check if the character is on the ground (not already jumping)
        if (chY === height - character.height) {
          // Apply an upward velocity to initiate the jump
          velocityY = -15; // Adjust the jump strength as needed
          console.log("JUMP");
        }
        console.log("JUMP");
        break;
      default:
        console.log(`${key} key not supported`);
        break;
    }
  }
  // Check if the character is colliding with any platform
  function isCollidingWithPlatform() {
    for (const platform of platforms) {
      if (
        character.x + character.width > platform.x &&
        character.x < platform.x + platform.width &&
        character.y + character.height > platform.y &&
        character.y < platform.y + platform.height
      ) {
        return true;
      }
    }
    return false;
  }

  function render() {
    ctx.clearRect(0, 0, width, height);
    drawCharacter();
    drawPlatform();
    requestAnimationFrame(render);
  }
  onMount(() => {
    canvas.focus();
    ctx = canvas.getContext("2d")!;
    createPlatforms();
    render();
  });
</script>

<canvas
  tabindex="0"
  bind:this={canvas}
  {width}
  {height}
  on:keypress={(e) => handleKeyPress(e.key)}
/>

<style>
  canvas {
    outline: 1px solid;
  }
</style>
