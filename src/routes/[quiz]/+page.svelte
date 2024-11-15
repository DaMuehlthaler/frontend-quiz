<script>
	import { tweened } from 'svelte/motion';
	import { cubicOut } from 'svelte/easing';
	import { page } from '$app/stores';
	import ListItem from '../../lib/components/ListItem.Svelte';

	const progress = tweened(0, {
		duration: 400,
		easing: cubicOut
	});

	let { data } = $props();
	let currentQuestion = $state(0);
	let selectedAnswer = $state('');
	let score = $state(0);
	let submitted = $state(false);
	let showErrorAlert = $state(false); // Zustand für den Fehler-Alert

	function handleSubmit() {
		if (selectedAnswer === '') {
			showErrorAlert = true; // Zeige den Fehler-Alert an
			setTimeout(() => {
				showErrorAlert = false; // Fehler-Alert nach 3 Sekunden verstecken
			}, 3000);
			return; // Verhindere, dass die Frage beantwortet wird, wenn keine Antwort ausgewählt wurde
		}

		if (selectedAnswer === data.questions[currentQuestion].answer) {
			score++;
		}
		submitted = true;
		data.questions[currentQuestion].selectedAnswer = selectedAnswer;
	}

	function nextQuestion() {
		currentQuestion++;
		submitted = false;
		selectedAnswer = '';
		progress.set((currentQuestion + 1) / data.questions.length);
	}
</script>

{#if showErrorAlert}
	<!-- Fehler-Alert oben auf der Seite -->
	<div role="alert" class="alert alert-error fixed top-0 left-0 right-0 z-50 p-4 mb-4">
		<svg
			xmlns="http://www.w3.org/2000/svg"
			class="h-6 w-6 shrink-0 stroke-current"
			fill="none"
			viewBox="0 0 24 24"
		>
			<path
				stroke-linecap="round"
				stroke-linejoin="round"
				stroke-width="2"
				d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z"
			/>
		</svg>
		<span>Achtung: Keine Antwort ausgewählt!</span>
	</div>
{/if}

{#if currentQuestion < data.questions.length}
	{#each data.questions as question, index (question.question)}
		{#if index === currentQuestion}
			<p class="my-3">Question {index + 1} of {data.questions.length}</p>

			<progress class="progress progress-primary w-56" value={$progress} max="1"></progress>

			<h1 class="my-3">{question.question}</h1>
			{#each question.options as option, index (option)}
				{#key submitted}
					<div class="my-3">
						<ListItem
							title={option}
							{index}
							correctAnswer={question.selectedAnswer && question.answer === option}
							incorrectlySelected={question.selectedAnswer === option && question.answer !== option}
							focusAction={() => (selectedAnswer = option)}
							disabled={submitted}
						></ListItem>
					</div>
				{/key}
			{/each}
		{/if}
	{/each}

	<div class="flex flex-col items-center">
		<button class="btn btn-primary" onclick={handleSubmit}>Submit</button>
		{#if submitted}
			<button class="btn btn-primary" onclick={nextQuestion}>Next Question</button>
		{/if}
	</div>
{:else}
	<h2>GameOver!</h2>
	<p>You have reached {score} of {data.questions.length} points</p>
	<button class="btn btn-primary" onclick={window.location.reload()}>Play Again</button>
{/if}

<style>
	progress {
		display: block;
		width: 100%;
	}
</style>
