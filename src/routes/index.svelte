<template>
	<section class="haiku-input" style="width: 300px">
		<Window title="Haiku Validator">
			<div class="field-row-stacked" style="width: 100%">
				<label for="text20">Enter your haiku</label>
				<textarea bind:value={haiku} rows="8" cols={30}/>
			</div>
		</Window>
		</section>

	<section class="haiku-results" style="width: 300px">
		<Window title="Validation Results">
				{#if displayResults}
					<ol>
						{#each lines as line}
							<li>
								<p>Evaluation: {line.text} - valid: {line.valid}</p>
								<p>Syllables: {line.syllableCount.join(', ')}</p>
							</li>
						{/each}
					</ol>
				{:else}
					<div>
						<p>Enter your haiku to continue</p>
					</div>
				{/if}
			<div class="status-bar">
				<p class="status-bar-field">Press F1 for help</p>
				<p class="status-bar-field">Haiku valid: {isValidHaiku}</p>
				<p class="status-bar-field">CPU Usage: 14%</p>
			</div>
		</Window>

	</section>
</template>

<script lang="ts">
	import Window from '../components/Window.svelte'
	import {from} from 'rxjs'
	// todo: add types
	import pronouncing from 'pronouncing/build/pronouncing-browser.js'

	interface HaikuLine {
		text: string
		valid: boolean
		syllables: number 
		syllableCount: number[]
	}

	function haikuLineFactory(syllables: number = 5): HaikuLine {
		return {
			text: '',
			valid: false,
			syllables,
			syllableCount: [],
		}
	}

	// textarea input
	let haiku = '';

	let lines = [haikuLineFactory(), haikuLineFactory(7), haikuLineFactory()]

	let displayResults = false
	let isValidHaiku = false

	function getSyllableCountForWord(word: string): number {
		return pronouncing.syllableCount(
			pronouncing.phonesForWord(word)[0]
		)
	}

	function syllableCount(line: string): number[] {
		return line.split(' ')
			.filter(Boolean)
			.map(getSyllableCountForWord)
	}


	function recalculateValidity() {
		haiku
			.split('\n')
			.filter(Boolean)
			.forEach((line, idx) => lines[idx].text = line)

		lines.forEach(line => {
			const syllables = syllableCount(line.text)
			line.valid = syllables.length === line.syllables
			line.syllableCount = syllables
		})

		lines = lines

		displayResults = lines.reduce((acc: boolean, cur) => !!cur.text && acc, true)
		isValidHaiku = lines.reduce((acc: boolean, cur) => cur.valid && acc, true)
	}

	$: haiku, recalculateValidity()
</script>
