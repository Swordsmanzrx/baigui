<script>
  import { onMount } from 'svelte';

  export let texts = [];
  export let speed = 100;
  export let pauseTime = 3000;

  let displayText = '';
  let cursor = true;
  let currentIndex = 0;
  let charIndex = 0;
  let isDeleting = false;

  function type() {
    const currentText = texts[currentIndex];

    if (!isDeleting) {
      displayText = currentText.substring(0, charIndex + 1);
      charIndex++;

      if (charIndex === currentText.length) {
        isDeleting = true;
        setTimeout(type, pauseTime);
        return;
      }
    } else {
      displayText = currentText.substring(0, charIndex - 1);
      charIndex--;

      if (charIndex === 0) {
        isDeleting = false;
        currentIndex = (currentIndex + 1) % texts.length;
        setTimeout(type, 500);
        return;
      }
    }

    setTimeout(type, isDeleting ? speed / 2 : speed);
  }

  onMount(() => {
    type();
    setInterval(() => {
      cursor = !cursor;
    }, 530);
  });
</script>

<span class="typewriter">
  「{displayText}
  <span class="cursor" class:cursor-blink={cursor}>|</span>
  」
</span>

<style>
  .typewriter {
    font-family: inherit;
  }
  .cursor {
    color: var(--primary, #fff);
    font-weight: 100;
  }
  .cursor-blink {
    opacity: 0;
  }
</style>