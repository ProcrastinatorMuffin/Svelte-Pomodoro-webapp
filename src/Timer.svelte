<script>
  import { onDestroy } from "svelte";
let time = 30 * 60; // Default start time.
let workInterval = 30 * 60; // Default work interval.
let breakInterval = 10 * 60; // Default break interval.
let intervalId;
let isBreak = false; 
const presets = [30, 40, 50];
let isRunning = false;

const setIntervals = (work, breakTime) => {
    workInterval = work * 60;
    breakInterval = breakTime * 60;
    resetTimer();
}

const startPauseTimer = () => {
    isRunning = !isRunning;
    
    if (isRunning) {
        if (intervalId) {
            clearInterval(intervalId);
        }

        // Notify that the timer has started
        showNotification("Timer Started!", "Your timer has started.");

        intervalId = setInterval(() => {
            time -= 1;

            if (time === 60 && isBreak) { // 1 minute before break ends
                showNotification("Break Almost Over!", "You have 1 minute left in your break.");
            }

            if (time <= 0) {
                isRunning = false;
                if (isBreak) {
                    // Notify that a new work section has started
                    showNotification("Work Time!", "Your break is over. Time to work again.");
                    time = workInterval;
                    isBreak = false;
                } else {
                    // Notify that the work section has ended
                    showNotification("Take a Break!", "Your work session has ended. Time for a break.");
                    time = breakInterval;
                    isBreak = true;
                }
                
                startPauseTimer();
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

const showNotification = (title, body) => {
    if (Notification.permission !== "granted") {
        Notification.requestPermission();
    } else {
        new Notification(title, {
            body: body,
        });
    }
};

onDestroy(() => {
    if (intervalId) {
        clearInterval(intervalId);
    }
});

</script>

<style>

.main-container {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: space-between;
    height: 100vh;
}

.clock-container,
.clock-container * {
    box-sizing: border-box;
}

.clock-container {
    background: #1D1F20;
    border-radius: 1.25rem;
    border: 3px solid rgba(255, 69, 0, 0.20);
    width: 50rem;
    height: 25rem;
    flex-shrink: 0;
    box-shadow: 40px 40px 20px 5px rgba(0, 0, 0, 0.25);
    margin-bottom: 1rem;
}

.clock {
    font-family: 'Orbitron', sans-serif;
    text-align: center;
    color: rgba(255, 69, 0, 0.50);
    font-size: 10.9375rem;
    display: flex;
    justify-content: center;
    align-items: center;
}

.digit {
    box-sizing: border-box;
    width: 10rem; /* Increased width from 10rem */
    height: 21.875rem;
    flex-shrink: 0;
    background: #151515;
    border-radius: 0.625rem;
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 0 0.4rem; /* Added horizontal margin for spacing between digits */
}

.start-button, .reset-button {
    display: flex;
    justify-content: center;
    align-items: center;
    background: #1d1f20;
    border-radius: 20px;
    border: 3px solid rgba(255, 69, 0, 0.2);
    width: 24.375rem;
    height: 9.375rem;
    box-shadow: 20px 20px 20px 5px rgba(0, 0, 0, 0.25);
}

.start-text, .reset-text {
    color: rgba(255, 69, 0, 0.5);
    font: 400 3rem "Orbitron", sans-serif;
}

.button-group {
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 50rem;
    gap: 2.375rem;
    margin-bottom: 2.5rem;
}

.start-button:hover, .reset-button:hover {
    background-color: rgba(255, 69, 0, 0.5);
    color: #1D1F20;
    box-shadow: 20px 20px 20px 5px rgba(255, 69, 0, 0.5);
    border: 3px solid #1D1F20;
}

.start-button:hover .start-text, .reset-button:hover .reset-text {
    color: #1D1F20;
}

.time-interval-buttons {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 25px;
    width: 50rem;
    margin-top: 2.5rem;
    height: 10rem;
    flex-shrink: 0;
    background: #1d1f20;
    border-radius: 20px;
    border: 3px solid rgba(255, 69, 0, 0.2);
    /* box-shadow: 20px 20px 10px 2.5px rgba(0, 0, 0, 0.25); */
    position: relative;
    padding: 25px 15px;
}

._30-10-button, ._40-10-button, ._50-20-button {
    margin: 0 0.3rem;
    border: none;
    background: #151515;
    border-radius: 10px;
    width: 14.5rem;
    height: 8rem;
    /* box-shadow: 5px 5px 5px 0px rgba(0, 0, 0, 0.25); */
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background-color 0.2s;
    position: relative;
}

._30-10-text, ._40-10-text, ._50-20-text {
    color: rgba(255, 69, 0, 0.5);
    font: 400 3rem "Orbitron", sans-serif;
    width: 100%;
    text-align: center;
    position: absolute; 
    top: 50%; 
    left: 50%; 
    transform: translate(-50%, -50%); 
    width: auto; /* Ensuring exact center alignment */
}

._30-10-button:hover, ._40-10-button:hover, ._50-20-button:hover {
    background-color: rgba(255, 69, 0, 0.5);
    color: #1D1F20;
    border: 3px solid #1D1F20; /* Adding border on hover */
    box-shadow: 0px 0px 10px 5px rgba(255, 69, 0, 0.5); /* Reduced shadow size on hover */

}

._30-10-button:hover ._30-10-text, 
._40-10-button:hover ._40-10-text, 
._50-20-button:hover ._50-20-text {
    color: #1D1F20;
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
      <button class="start-button" on:click={startPauseTimer}>
        <div class="start-button-body"></div>
        <div class="start-text">{isRunning ? 'Pause' : 'Start'}</div>
      </button>
      <button class="reset-button" on:click={resetTimer}>
        <div class="reset-button-body"></div>
        <div class="reset-text">Reset</div>
      </button>
    </div>

</div>


  