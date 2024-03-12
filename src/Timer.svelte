<script>
    import { onDestroy } from "svelte";
    
    let time = 30 * 60; // Default start time.
    let workInterval = 30 * 60; // Default work interval.
    let breakInterval = 10 * 60; // Default break interval.
    let intervalId;
    let isBreak = false; 
    let isRunning = false;
    
    const presets = [30, 40, 50];
    
    const setIntervals = (work, breakTime) => {
            workInterval = work * 60;
            breakInterval = breakTime * 60;
            resetTimer();
    }
    
    const endTimer = () => {
    isRunning = false;
    if (isBreak) {
      time = workInterval;
      isBreak = false;
      // Notify that a new work section has started
      showNotification("Work Time!", "Your break is over. Time to work again.");
    } else {
      time = breakInterval;
      isBreak = true;
      // Notify that the work section has ended
      showNotification("Take a Break!", "Your work session has ended. Time for a break.");
    }

    // Start the next session immediately
    startPauseTimer();
  };
    
  const startTimer = () => {
    // Notify that the timer has started
    showNotification("Timer Started!", "Your timer has started.");
    startPauseTimer();
  };

  const startPauseTimer = () => {
    isRunning = !isRunning;

    if (isRunning) {
      if (intervalId) {
        clearInterval(intervalId);
      }

      intervalId = setInterval(() => {
        time -= 1;

        if (time === 60 && isBreak) { // 1 minute before break ends
          showNotification("Break Almost Over!", "You have 1 minute left in your break.");
        }

        if (time <= 0) {
          clearInterval(intervalId);
          endTimer();
        }
      }, 1000);
    } else {
      if (intervalId) {
        clearInterval(intervalId);
      }
    }
  };
    
    const resetTimer = () => {
            time = workInterval; // Resetting to the current work interval.
            isBreak = false;
            if (intervalId) {
                    clearInterval(intervalId);
                    isRunning = false;
            }
    };
    
    const showNotification = async (title, body) => {
        if (Notification.permission !== "granted") {
                await Notification.requestPermission();
        }
        if (Notification.permission === "granted") {
                const notification = new Notification(title, {
                        body: body,
                });
                const audio = new Audio("/radial.mp3");
                audio.play().catch(error => console.error('Error playing audio:', error));
        }
    };
    
    onDestroy(() => {
            if (intervalId) {
                    clearInterval(intervalId);
            }
    });
    
</script>

<style lang="scss">
$primary-color: #1D1F20;
$secondary-color: rgba(255, 69, 0, 0.5);
$border-color: rgba(255, 255, 255, 0.1);
$shadow-color: rgba(0, 0, 0, 0.25);
$hover-color: #1D1F20;
$font-family: 'Orbitron', sans-serif;

@mixin flex-center {
    display: flex;
    justify-content: center;
    align-items: center;
}

@mixin button-styles {
    @include flex-center;
    background: $primary-color;
    border-radius: 20px;
    border: 3px solid $border-color;
    box-shadow: 20px 20px 20px 5px $shadow-color;
    &:hover {
        background-color: $secondary-color;
        color: $hover-color;
        box-shadow: 20px 20px 20px 5px $secondary-color;
        border: 3px solid $border-color;
    }
}

.main-container {
    @include flex-center;
    flex-direction: column;
    justify-content: space-between;
    height: 100vh;
}

.clock-container,
.clock-container * {
    box-sizing: border-box;
}

.clock-container {
    background: $primary-color;
    border-radius: 1.25rem;
    border: 3px solid $border-color;
    width: 50rem;
    height: 25rem;
    flex-shrink: 0;
    box-shadow: 20px 20px 20px 5px $shadow-color;
    margin-bottom: 1rem;
}

.clock {
    font-family: $font-family;
    text-align: center;
    color: $secondary-color;
    font-size: 10.9375rem;
    @include flex-center;
}

.digit {
    box-sizing: border-box;
    width: 10rem;
    height: 21.875rem;
    flex-shrink: 0;
    background: #151515;
    border-radius: 0.625rem;
    @include flex-center;
    margin: 0 0.4rem;
}

.start-button, .reset-button {
    @include button-styles;
    width: 24.375rem;
    height: 9.375rem;
}

.start-text, .reset-text {
    color: $secondary-color;
    font: 400 3rem $font-family;
}

.button-group {
    @include flex-center;
    justify-content: space-between;
    width: 50rem;
    gap: 2.375rem;
    margin-bottom: 2.5rem;
}

.start-button:hover .start-text, .reset-button:hover .reset-text {
    color: $hover-color;
}

.time-interval-buttons {
    @include flex-center;
    justify-content: space-between;
    padding: 25px;
    width: 50rem;
    margin-top: 2.5rem;
    height: 10rem;
    flex-shrink: 0;
    background: $primary-color;
    border-radius: 20px;
    border: 3px solid $border-color;
    position: relative;
    padding: 25px 15px;
    box-shadow: 20px 20px 20px 5px $shadow-color;
}

._30-10-button, ._40-10-button, ._50-20-button {
    margin: 0 0.3rem;
    border: none;
    background: #151515;
    border-radius: 10px;
    width: 14.5rem;
    height: 8rem;
    cursor: pointer;
    @include flex-center;
    transition: background-color 0.2s;
    position: relative;
}

._30-10-text, ._40-10-text, ._50-20-text {
    color: $secondary-color;
    font: 400 3rem $font-family;
    width: 100%;
    text-align: center;
    position: absolute; 
    top: 50%; 
    left: 50%; 
    transform: translate(-50%, -50%); 
    width: auto;
}

._30-10-button:hover, ._40-10-button:hover, ._50-20-button:hover {
    background-color: $secondary-color;
    color: $hover-color;
    border: 3px solid $border-color;
    box-shadow: 0px 0px 10px 5px $secondary-color;
}

._30-10-button:hover ._30-10-text, 
._40-10-button:hover ._40-10-text, 
._50-20-button:hover ._50-20-text {
    color: $hover-color;
}
</style>
  

  <div class="main-container">

    <div class="time-interval-buttons">
      <button 
          class="_30-10-button" 
          on:click={() => setIntervals(30, 10)} 
          on:keydown={(e) => { if (e.key === 'Enter') setIntervals(30, 10); }}>
          <div class="_30-10-text">30/10</div>
      </button>
      <button 
          class="_40-10-button" 
          on:click={() => setIntervals(40, 10)} 
          on:keydown={(e) => { if (e.key === 'Enter') setIntervals(40, 10); }}>
          <div class="_40-10-text">40/10</div>
      </button>
      <button 
          class="_50-20-button" 
          on:click={() => setIntervals(50, 20)} 
          on:keydown={(e) => { if (e.key === 'Enter') setIntervals(50, 20); }}>
          <div class="_50-20-text">50/20</div>
      </button>
  </div>    
  
    <div class="clock-container">
        <p class="clock">
            <span class="digit">{Math.floor(time / 600) % 6}</span>
            <span class="digit">{Math.floor(time / 60) % 10}</span>
            <span>:</span>
            <span class="digit">{Math.floor((time % 60) / 10)}</span>
            <span class="digit">{time % 10}</span>
        </p>
    </div>
    
    <div class="button-group">
        <button class="start-button" on:click={isRunning ? startPauseTimer : startTimer}>
          <div class="start-button-body"></div>
          <div class="start-text">{isRunning ? 'Pause' : 'Start'}</div>
        </button>
        <button class="reset-button" on:click={resetTimer}>
          <div class="reset-button-body"></div>
          <div class="reset-text">Reset</div>
        </button>
      </div>

</div>


  