<script lang="ts">
	import { json } from '@sveltejs/kit';

	let characterName = '';
	let pageNumber = 1;
	const getCharacterList = async (character: string | void) => {
		let url = character
			? `https://rickandmortyapi.com/api/character/?name=${character}&page=${pageNumber}`
			: `https://rickandmortyapi.com/api/character/?page=${pageNumber}`;
		var response = await fetch(url);
		var result = await response.json();

		if (response.ok) {
			console.log(result);
			return result;
		} else {
			throw new Error(result);
		}
	};

	let characterList = getCharacterList();
</script>

<svelte:head>
	<title>Rick & Morty Characters</title>
	<meta name="robots" content="noindex nofollow" />
	<html lang="en" />
</svelte:head>
<div class="navbar bg-base-100">
	<div class="flex-1">
		<a class="btn btn-ghost normal-case text-xl" href="/characters">R&M API</a>
	</div>
	<div class="flex-none gap-2">
		<div class="form-control">
			<input
				type="text"
				placeholder="Search"
				class="input input-bordered"
				bind:value={characterName}
				on:change={() => (characterList = getCharacterList(characterName))}
			/>
		</div>
	</div>
</div>
<div class="container my-12 mx-auto px-4 md:px-12">
	<div class="flex flex-wrap">
		{#await characterList}
			<progress class="progress w-56" />
		{:then characters}
			{#each characters.results as character}
				<div class="card w-45 bg-base-100 shadow-xl mx-1 lg:mx-4 my-3">
					<figure>
						<img class="rounded" src={character.image} alt={character.name} />
					</figure>
					<div class="card-body">
						<p class="card-title">
							{character.name}
						</p>
						<br />
						<div class="card-actions">
							<div class="badge badge-outline">{character.species}</div>
							<div
								class="badge badge-outline {character.status === 'Alive'
									? 'badge-primary'
									: character.status === 'Dead'
									? 'badge-secondary'
									: ''}"
							>
								{character.status}
							</div>
							<!-- <div class="badge badge-outline">{character.origin.name}</div> -->
						</div>
					</div>
				</div>
			{/each}
		{:catch}
			<div class="alert alert-error shadow-lg">
				<div>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="stroke-current flex-shrink-0 h-6 w-6"
						fill="none"
						viewBox="0 0 24 24"
						><path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z"
						/></svg
					>
					<span>Error! Failed to retrieve data from the Rick and Morty API.</span>
				</div>
			</div>
		{/await}
	</div>
</div>
