<script>
    import confetti from 'canvas-confetti';
    import { onMount } from 'svelte';

    const targetDate = new Date('April 29, 2025 10:00:00').getTime();

    let days = '00';
    let hours = '00';
    let minutes = '00';
    let seconds = '00';
    let isPast = false;
    let showTimer = false; // Controls the fade-in effect
    let showVideo = false; // Controls the visibility of the video background


    function launchConfetti() {
    const duration = 200000000;
    const animationEnd = Date.now() + duration;
    const defaults = { startVelocity: 30, spread: 360, ticks: 200, zIndex: 1000 };

    const interval = setInterval(() => {
        const timeLeft = animationEnd - Date.now();

        if (timeLeft <= 0) {
        return clearInterval(interval);
        }

        const particleCount = 50 * (timeLeft / duration);
        confetti({
        ...defaults,
        particleCount,
        origin: {
            x: Math.random(),
            y: Math.random() - 0.2
        }
        });
    }, 500);
    }

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

        // Show the video background after 0.5 seconds
        setTimeout(() => {
            showVideo = true;
        }, 1000);

        setTimeout(() => {
            showTimer = true;
        }, 5000);

        return () => clearInterval(timer);
    });

    const handleOpenClick = () => {
        showTimer = true; // Show the timer when "OPEN" is clicked
    };

    $: if (isPast) {
        showTimer = true;
        setTimeout(() => {
            showVideo = true;
        }, 1000);
        launchConfetti();
    }
    
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
    .logo {
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
        opacity: 0; /* Initially hidden */
        transition: opacity 1s ease-in; /* Smooth fade-in effect */
    }
    .time.show {
        opacity: 1; /* Fully visible when the class is added */
    }
    .open-button {
        font-family: 'ThierryLeonie', serif;
        font-size: 10vw;
        color: #f4deb2;
        background: none;
        border: none;
        cursor: pointer;
        text-align: center;
        padding: 0;
    }
    .background {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        background-color: #0f6f2b;
        z-index: -2;
    }
    .video-background {
        position: fixed;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        z-index: -1;
        overflow: hidden;
        opacity: 0;
        transition: opacity 0.5s ease-in;
    }
    .video-background.visible {
        opacity: 30%;
    }
    #bg-video {
    position: fixed;   /* sit on top of the page content */
    top: 0;
    left: 0;
    width: 100vw;      /* full viewport width */
    height: 100vh;     /* full viewport height */
    object-fit: cover; /* scales & crops to fill */
    z-index: -1;       /* send it behind any page content */
    }
    .content {
        position: relative;
        z-index: 1;
    }

    @media (max-width: 768px) {
        .logo {
            font-size: 10vw; /* increase size on mobile */
        }

        .time {
            font-size: 8vw; /* bigger countdown text on mobile */
        }

        .open-button {
            font-size: 14vw; /* even bigger OPEN button */
        }
    }
</style>

<div class="background"></div>

<div class="video-background {showVideo ? 'visible' : ''}">
  <video autoplay muted loop playsinline id='bg-video' src='milk.mov'></video>
</div>

<div class="parent content">
  <div class="container">
    <button class="logo open-button" on:click={handleOpenClick}>tcha tcha</button>
    {#if isPast}
      <h1 class="time show">OPEN</h1>
    {:else}
      <h1 class="time {showTimer ? 'show' : ''}">{days}d {hours}h {minutes}m {seconds}s</h1>
    {/if}
  </div>
</div>

