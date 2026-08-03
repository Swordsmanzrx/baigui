<script>
  import { onMount } from 'svelte';

  let stage = 'black'; // black -> text -> fade
  let visible = true;

  onMount(() => {
    setTimeout(() => { stage = 'text'; }, 500);
    setTimeout(() => { stage = 'fade'; }, 4000);
    setTimeout(() => { visible = false; }, 5000);
  });
</script>

{#if visible}
  <div class="overlay" class:fade-out={stage === 'fade'}>
    <div class="text-layer" class:show={stage === 'text' || stage === 'fade'}>
      <div class="vertical-text">
        <span>凡</span>
        <span>王</span>
        <span>之</span>
        <span>血</span>
        <span class="gap"></span>
        <span>必</span>
        <span>以</span>
        <span>剑</span>
        <span>终</span>
      </div>
    </div>
  </div>
{/if}

<style>
  .overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    background: #000;
    z-index: 99999;
    display: flex;
    align-items: center;
    justify-content: flex-start;
    opacity: 1;
    transition: opacity 1s ease;
  }

  .overlay.fade-out {
    opacity: 0;
    pointer-events: none;
  }

  .text-layer {
    padding-left: 3rem;
    opacity: 0;
    transition: opacity 1.5s ease;
  }

  .text-layer.show {
    opacity: 1;
  }

  .vertical-text {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.3rem;
  }

  .vertical-text span {
    font-family: "SimSun", "宋体", "Songti SC", serif;
    font-weight: normal;
    font-size: 3rem;
    color: #fff;
    text-shadow: 0 0 20px rgba(255, 255, 255, 0.3);
    opacity: 0;
    animation: charFade 0.8s ease forwards;
  }

  .vertical-text span:nth-child(1) { animation-delay: 0.2s; }
  .vertical-text span:nth-child(2) { animation-delay: 0.5s; }
  .vertical-text span:nth-child(3) { animation-delay: 0.8s; }
  .vertical-text span:nth-child(4) { animation-delay: 1.1s; }
  .vertical-text span.gap { animation-delay: 1.4s; visibility: hidden; height: 0.8rem; }
  .vertical-text span:nth-child(6) { animation-delay: 1.7s; }
  .vertical-text span:nth-child(7) { animation-delay: 2.0s; }
  .vertical-text span:nth-child(8) { animation-delay: 2.3s; }
  .vertical-text span:nth-child(9) { animation-delay: 2.6s; }

  @keyframes charFade {
    0% { opacity: 0; transform: translateY(-15px); }
    100% { opacity: 1; transform: translateY(0); }
  }
</style>