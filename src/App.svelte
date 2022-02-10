<script>
	import Board from './components/Board.svelte';

	export let rows = [0, 1, 2, 3, 4, 5];
	export let columns = [0, 1, 2, 3, 4];

	// export let currentGuestIndex = 0;
	export let currentWord = 'dobby';
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

</script>

<main>
	<header>
		<div class="container">
			<h2>Wordles</h2>
		</div>
	</header> 

	<Board
		wordle={currentWord.toLowerCase()}
		rows={rows}
		columns={columns}
		grid={createGrid}
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
		background: var(--secondary-dark);
		color: var(--primary-lighter);
	}
</style>