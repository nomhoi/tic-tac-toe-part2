<script>
	import { onMount } from 'svelte';
	import { history, status } from './stores.js';

	export let width  = 3;
	export let height = 3;
	export let cellWidth  = 34;
	export let cellHeight = 34;
	export let colorStroke = "#999";
	let boardWidth  = 1 + (width * cellWidth);
	let boardHeight = 1 + (height * cellHeight);
	let canvas;

    let state = {
      squares: Array(9).fill(''),
      xIsNext: true,
	};

	history.push(state);

	function handleClick(event) {
		let x = Math.trunc((event.offsetX + 0.5) / cellWidth);
		let y = Math.trunc((event.offsetY + 0.5) / cellHeight);
		let i = y * width + x;

		const state = $history.currentState();
		if ($status == 1 || state.squares[i])
			return;
		
		const squares = state.squares.slice();
		squares[i] = state.xIsNext ? 'X' : 'O';
		let newState = {
			squares: squares,
			xIsNext: !state.xIsNext,
		};
		history.push(newState);
	}

	onMount(() => {
		const ctx = canvas.getContext('2d');

		function draw() {
			ctx.clearRect(0, 0, boardWidth, boardHeight);

			// draw board
			ctx.beginPath();

			// vertical lines
			for (let x = 0; x <= boardWidth; x += cellWidth) {
				ctx.moveTo(0.5 + x, 0);
				ctx.lineTo(0.5 + x, boardHeight);
			}

			// horizontal lines
			for (let y = 0; y <= boardHeight; y += cellHeight) {
				ctx.moveTo(0, 0.5 + y);
				ctx.lineTo(boardWidth, 0.5 +  y);
			}

			// draw the board
			ctx.strokeStyle = colorStroke;
			ctx.stroke();

			ctx.closePath();

			// draw cells
			ctx.beginPath();

			ctx.font = "bold 22px Century Gothic";
			let d = 8;
			let k = 0;
			for (let i = 0; i < height; i+=1) {
				for (let j = 0; j < width; j+=1) {
					ctx.fillText($history.currentState().squares[k], j * cellWidth + d + 1, (i + 1) * cellHeight - d);
					k++;
				}
			}

			ctx.closePath();

			requestAnimationFrame(draw);
		}

		draw();
	});

</script>

<canvas
				bind:this={canvas}
				width={boardWidth}
				height={boardHeight}
				on:click={handleClick}
				></canvas>
