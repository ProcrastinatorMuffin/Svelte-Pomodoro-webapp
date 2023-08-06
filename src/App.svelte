<script>
	import { onMount } from 'svelte';
	import Timer from './Timer.svelte';
  
	onMount(() => {
	  if (Notification.permission !== "granted") {
		Notification.requestPermission();
	  }
	});
  </script>
  
  <style>
	main {
	  display: flex;
	  justify-content: center;
	  align-items: center;
	  height: 100vh;
	  background-color: #282a36; /* a soothing dark grey color */
	  color: #f8f8f2; /* a light off-white color */
	  font-family: 'Orbitron', sans-serif;
	}
  </style>
  
  <main>
	<Timer />
  </main>
  