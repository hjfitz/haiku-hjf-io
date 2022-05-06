<template>
	<section class="haiku-input" style="width: 400px">
		<div class="window">
			<div class="title-bar">
				<div class="title-bar-text">Haiku Validator</div>
				<div class="title-bar-controls">
					<button aria-label="Minimize"></button>
					<button aria-label="Maximize"></button>
					<button aria-label="Close"></button>
				</div>
			</div>
			<div class="window-body">
				<div class="field-row-stacked" style="width: 100%">
					<label for="text20">Enter your haiku</label>
					<textarea bind:value={haiku} rows="8" cols={30}/>
				</div>
			</div>
		</div>
	</section>

	<section class="haiku-results">
		<div class="window" style="width: 300px">
			<div class="title-bar">
				<div class="title-bar-text">Haiku Validation Results</div>
				<div class="title-bar-controls">
					<button aria-label="Minimize"></button>
					<button aria-label="Maximize"></button>
					<button aria-label="Close"></button>
				</div>
			</div>
			<div class="window-body">
				{#if displayResults}
					<ol>
						{#each lines as line, idx}
							<li>{line.text} - valid: {line.valid}</li>
						{/each}
					</ol>
				{:else}
					<div>
						<p>Enter your haiku to continue</p>
					</div>
				{/if}
			</div>
			<div class="status-bar">
				<p class="status-bar-field">Press F1 for help</p>
				{#if displayResults}
					<p class="status-bar-field">Haiku valid: {isValidHaiku}</p>
				{:else}
					<p class="status-bar-field">Slide 1</p>
				{/if}
				<p class="status-bar-field">CPU Usage: 14%</p>
			</div>
		</div>

	</section>
</template>

<script lang="ts">
	// todo: add types
	import pronouncing from 'pronouncing/build/pronouncing-browser.js'

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

	let displayResults = false
	let isValidHaiku = false

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

		displayResults = lines.reduce((acc: boolean, cur) => !!cur.text && acc, true)
		isValidHaiku = lines.reduce((acc: boolean, cur) => cur.valid && acc, true)
	}

	$: haiku, recalculateValidity()
</script>
