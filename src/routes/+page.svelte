<script>
    import { onMount } from 'svelte';
  
    const targetDate = new Date('May 4, 2025 20:00:00').getTime();
  
    let days = '00';
    let hours = '00';
    let minutes = '00';
    let seconds = '00';
    let isPast = false;
  
    onMount(() => {
        const updateTimer = () => {
            const now = new Date().getTime();
            const distance = targetDate - now;
    
            if (distance < 0) {
                isPast = true;
                clearInterval(timer);
            } else {
                days = Math.floor(distance / (1000 * 60 * 60 * 24)).toString().padStart(2, '0');
                hours = Math.floor((distance % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60)).toString().padStart(2, '0');
                minutes = Math.floor((distance % (1000 * 60 * 60)) / (1000 * 60)).toString().padStart(2, '0');
                seconds = Math.floor((distance % (1000 * 60)) / 1000).toString().padStart(2, '0');
            }
        };
  
      updateTimer();
      const timer = setInterval(updateTimer, 1000);
  
      return () => clearInterval(timer);
    });
</script>

<style>
    /* Base styles (applied to all screen sizes) */
    .parent {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
    }
    .container {
        display: flex;
        flex-direction: column;
        justify-content: center; /* vertical centering */
        align-items: center;     /* horizontal centering */
        height: 100vh;           /* full viewport height */
        width: 100vw;            /* full viewport width */
        text-align: center;
    }
    h1 {
        font-family: 'ThierryLeonie', serif;
        margin: 0; /* remove default spacing */
        color: #f4deb2;
        font-size: 6vw;
    }
    .time {
        font-family: 'Courier Prime', monospace;
        font-weight: bolder;
        margin: 0;
        color: #f4deb2;
        font-size: 4vw;
    }
</style>

<div class="parent">
  <div class="container">
    <h1>tcha tcha</h1>
    {#if isPast}
      <h1>🟢 OPEN</h1>
    {:else}
      <h1 class="time">{days}d {hours}h {minutes}m {seconds}s</h1>
    {/if}
  </div>
</div>

