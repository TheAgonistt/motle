<script>
	import Board from './components/Board.svelte';
	import Dictionary from './dictionary';

	let wordLength = 5
	export let rows = [0, 1, 2, 3, 4, 5];
	export let columns = [0, 1, 2, 3, 4];

	const validWords = Dictionary.filter((word) => {
		return word.length === wordLength;
	});

	const currentWord = validWords[Math.floor(Math.random() * validWords.length)];
	const createGrid = rows
    .map((row) => {
			const fields = columns.map((column) => {
				return {
					state: null,
					ref: null,
				};
			});

			return {
				button: null,
				form: null,
				fields: fields,
				state: 'inactive',
			};
		})
	
	const createSettings = {
		wordle: currentWord,
		validWords: validWords,
	};

	console.log('validWords: ', validWords);
</script>

<main>
	<header class="c-Header">
		<div class="container">
			<div class="c-Header__row">
				<h1>NiceLogoAndName</h1>
				<button class="material-icons">settings</button>
			</div>
		</div>
	</header> 

	<Board
		wordle={currentWord.toLowerCase()}
		grid={createGrid}
		settings={createSettings}
	/>
</main>

<style>
	:global(:root) {
		--secondary-dark: #22223B;
		--secondary: #4A4E69;
		--primary: #9A8C98;
		--primary-light: #C9ADA7;
		--primary-lighter: #F2E9E4;
	}

	:global(body) {
		min-height: 100vh;
		background: var(--secondary-dark);
		color: var(--primary-lighter);
	}

	.c-Header {
		padding-block: 20px;
	}

	.c-Header__row {
		display: flex;
		justify-content: space-between;
	}

	.c-Header__row h1 {
		font-size: 1.3rem;
		margin: 0;
	}

	.c-Header__row button {
		padding: 0;
		margin: 0;
		border: none;
		background: transparent;
		font-size: 1.3rem;
		color: var(--primary-lighter);
	}
</style>