<script lang="ts">
  import { onMount } from "svelte";

  const [width, height] = [800, 400];
  let canvas: HTMLCanvasElement;
  let ctx: CanvasRenderingContext2D;
  let character = {
    x: 50,
    y: height - 50,
    width: 30,
    height: 30,
    velocityY: 0,
    isJumping: false,
    speed: 15,
  };
  let platforms = [
    { x: 50, y: height - 20, width: 150, height: 10 },
    { x: 250, y: height - 50, width: 150, height: 10 },
    // Add more platforms as needed
  ];

  const gravity = 0.5;
  const jumpStrength = -15;

  function drawCharacter() {
    ctx.fillStyle = "red";
    ctx.fillRect(character.x, character.y, character.width, character.height);
  }

  function drawPlatforms() {
    ctx.fillStyle = "green";
    platforms.forEach((platform) => {
      ctx.fillRect(platform.x, platform.y, platform.width, platform.height);
    });
  }

  function jump() {
    if (!character.isJumping) {
      character.isJumping = true;
      character.velocityY = jumpStrength;
    }
  }

  function updateCharacterPosition() {
    character.y += character.velocityY;
    character.velocityY += gravity;

    if (character.y > height - character.height) {
      character.y = height - character.height;
      character.isJumping = false;
    }
  }

  function checkCollisions() {
    platforms.forEach((platform) => {
      if (
        character.x + character.width > platform.x &&
        character.x < platform.x + platform.width &&
        character.y + character.height >= platform.y &&
        character.y < platform.y + platform.height
      ) {
        if (character.velocityY > 0) {
          character.y = platform.y - character.height;
          character.isJumping = false;
          character.velocityY = 0;
        }
      }
    });
  }

  function handleKeyPress(event: KeyboardEvent) {
    switch (event.key) {
      case " ":
        if (!character.isJumping) {
          jump();
        }
        break;
      case "d":
        character.x += character.speed;
        break;
      case "a":
        character.x -= character.speed;
        break;
      default:
        break;
    }
  }

  function render() {
    ctx.clearRect(0, 0, width, height);
    drawPlatforms();
    drawCharacter();
    updateCharacterPosition();
    checkCollisions();
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
  on:keydown={handleKeyPress}
/>

<style>
  canvas {
    border: 1px solid #000;
  }
</style>
