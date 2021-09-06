<script>
import { onMount } from "svelte";

let breeds = [];
let selected = "any";
let imgCnt = 0;

let promise = getBreed();

onMount(async () => {
	const promise = await getDogs();
	for (let breed in promise.message) {
		breeds.push(breed);
	}
	breeds = breeds;
});

async function getDogs() {
	const res = await fetch("https://dog.ceo/api/breeds/list/all");
	const json = await res.json();
	return json;
}

async function getBreed() {
	let url;

	if (selected == "any") {
		url = `https://dog.ceo/api/breeds/image/random/50`;
	} else {
		url = `https://dog.ceo/api/breed/${selected}/images`;
	}
	const res = await fetch(url);
	const json = await res.json();

	return json;
}

function handleClick() {
	imgCnt = 0;
	promise = getBreed();
}
</script>

<label for="dogs">Choose a dog breed:</label>
<select bind:value={selected} on:change={handleClick} id="dogs">
	<option value="any" selected>Any</option>
	{#each breeds as breed}
		<option value={breed}>{breed}</option>
	{/each}
</select>

{#await promise then breeds}
	<div class="main" class:show={imgCnt == breeds.message.length}>
		{#each breeds.message as breed}
			<img src={breed} alt="doggy" on:load={() => ++imgCnt} />
		{/each}
	</div>
{/await}

<style>
.main {
	opacity: 0;
	transition: opacity 1s;
	margin-top: 2rem;
	line-height: 0;
	-webkit-column-count: 5;
	-webkit-column-gap: 0px;
	-moz-column-count: 5;
	-moz-column-gap: 0px;
	column-count: 5;
	column-gap: 0px;
}

.show {
	opacity: 1 !important;
}

@media only screen and (max-width: 1024px) {
	.main {
		column-count: 4;
	}
}

@media only screen and (max-width: 768px) {
	.main {
		column-count: 3;
	}
}

@media only screen and (max-width: 512px) {
	.main {
		column-count: 1;
	}
}

img {
	width: 100%;
	height: auto;
}
</style>
