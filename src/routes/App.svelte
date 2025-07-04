<script>
	//Binded values
	let rate = $state(15.49);
  let starttime = $state("05:00");
  let endtime = $state("17:00");

	//Handles updating current time to the second
  let now = $state(new Date());
	$effect(() => {
    const interval = setInterval(() => now = new Date().getTime(), 1000);
		return () => clearInterval(interval);
  })

	//determines a timestamp for today with start and end hours/mins
	function hhmmToTimestamp(t){
		if(!t) return;
		let d = new Date();
		let [hours, mins] = t.split(":").map(Number)
		d.setHours(hours);
		d.setMinutes(mins);
		return d.getTime();
	}
	
	let startstamp = $derived(hhmmToTimestamp(starttime))
	let endstamp = $derived(hhmmToTimestamp(endtime));

	//determines the length of the shift and the progress through throught the shift
	let shiftlength = $derived((startstamp && endstamp) ? endstamp - startstamp : 0)
	let progress = $derived(shiftlength ? Math.round((now-startstamp)*100/shiftlength * 100)/100 : 0)

	//calculates money
  let totalmoney = $derived(Math.round(rate * Math.round(shiftlength/1000/60/60*4)/4 * 100)/100)
	let currentmoney = $derived(Math.round(totalmoney * progress)/100)
</script>

<!-- Input Field -->
<input type="number" bind:value={rate} id="rate" step="0.01" />
<label for="rate">Hourly Rate</label>
<br />
<input type="time" bind:value={starttime} id="start-time" />
<label for="start-time">Shift Start Time</label>
<br />
<input type="time" bind:value={endtime} id="end-time" />
<label for="end-time">Shift End Time</label>

<div class="progress-screen">
  <div class="progress-info">
    <span class="start">$0</span>
    <span class="current">${currentmoney}</span>
    <span class="end">${totalmoney}</span>
  </div>
  <progress value={progress} max="100" id="progressbar"></progress>
</div>

<style>
    html, body {
      margin: 0;
      padding: 0;
      height: 100%;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: linear-gradient(135deg, #0f2027, #203a43, #2c5364);
      color: #fff;
      overflow: hidden;
    }

    .progress-screen {
      height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: center;
      padding: 0 2rem;
      box-sizing: border-box;
    }

    .progress-info {
      display: flex;
      justify-content: space-between;
      align-items: center;
      position: relative;
      font-size: 2rem;
      margin-bottom: 2rem;
      color: #ccc;
    }

    .progress-info .current {
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      color: #00e6e6;
      font-weight: bold;
    }

    progress {
      width: 100%;
      height: 25px;
      appearance: none;
      border-radius: 20px;
      overflow: hidden;
      background-color: rgba(255, 255, 255, 0.1);
      box-shadow: 0 0 10px rgba(0,0,0,0.5) inset;
      backdrop-filter: blur(10px);
    }

    /* Webkit (Chrome, Safari) */
    progress::-webkit-progress-bar {
      background-color: rgba(255, 255, 255, 0.1);
    }

    progress::-webkit-progress-value {
      background: linear-gradient(to right, #00f2fe, #4facfe);
      border-radius: 20px;
      transition: width 0.4s ease-in-out;
    }

    /* Firefox */
    progress::-moz-progress-bar {
      background: linear-gradient(to right, #00f2fe, #4facfe);
      border-radius: 20px;
    }

    /* Optional neon glow */
    progress::-webkit-progress-value {
      box-shadow: 0 0 10px #00f2fe;
    }
</style>
