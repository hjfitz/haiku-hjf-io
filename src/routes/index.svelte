<template>
	<h1>Welcome to SvelteKit</h1>
	<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>
	<section>
		<div>
			<textarea bind:value={haiku} />
		</div>
		<div>
			{#each lines as line}
				<p>{line.text} - valid: {line.valid}</p>
			{/each}
		</div>

	</section>
</template>

<script lang="ts">
	import pronouncing from 'pronouncing/build/pronouncing-browser'

	console.log(pronouncing)
	interface HaikuLine {
		text: string
		valid: boolean
		syllables: number 
	}

	function haikuLineFactory(syllables: number = 5): HaikuLine {
		return {
			text: '',
			valid: false,
			syllables,
		}
	}

	let haiku = '';

	let line1 = haikuLineFactory()
	let line2 = haikuLineFactory(7)
	let line3 = haikuLineFactory()

	let lines = [line1, line2, line3]

	function syllableCount(line: string): number {
		return line.split(' ')
			.filter(Boolean)
			.map(word => 
				 pronouncing.syllableCount(
					pronouncing.phonesForWord(word)[0]
				)
			).reduce((acc, cur) => acc + cur, 0)
	}

	function recalculateValidity() {
		haiku
			.split('\n')
			.filter(Boolean)
			.forEach((line, idx) => lines[idx].text = line)

		lines.forEach(line => {
			line.valid = syllableCount(line.text) === line.syllables
		})
		lines = lines
	}

	$: haiku, recalculateValidity()
</script>
