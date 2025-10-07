<script>
	let lanes = ["To Do", "Doing", "Done", "Archiv"];

	let issues = [
		{ title: "Projekt starten", description: "Grundstruktur aufbauen", lane: "To Do" },
		{ title: "HTML erstellen", description: "Dialog und Layout", lane: "Doing" },
		{ title: "Design verbessern", description: "Farben & Layout", lane: "Done" },
		{ title: "Alte Tasks speichern", description: "Archivierte Elemente", lane: "Archiv" }
	];
</script>

<style>
	@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap');

	main {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 1.8rem;
		padding: 2rem;
		min-height: 100vh;
		background: linear-gradient(135deg, #e8eef3, #f3f6fa);
		font-family: 'Inter', sans-serif;
	}

	header {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: 1rem;
		background: #ffffff;
		box-shadow: 0 3px 10px rgba(0, 0, 0, 0.05);
	}

	button {
		background: #0077ff;
		color: white;
		font-weight: 600;
		border: none;
		padding: 0.8rem 1.4rem;
		border-radius: 10px;
		cursor: pointer;
		font-size: 1rem;
		transition: background 0.25s ease;
	}

	button:hover {
		background: #005fd1;
	}

	.lane {
		background: white;
		border-radius: 18px;
		padding: 1.3rem;
		box-shadow: 0 4px 14px rgba(0, 0, 0, 0.08);
		display: flex;
		flex-direction: column;
		transition: 0.25s ease;
	}

	.lane:hover {
		transform: translateY(-6px);
		box-shadow: 0 8px 18px rgba(0, 0, 0, 0.12);
	}

	h2 {
		text-align: center;
		color: #005fe0;
		margin-bottom: 1.2rem;
		font-size: 1.4rem;
		font-weight: 600;
		border-bottom: 2px solid #0077ff25;
		padding-bottom: 0.6rem;
		letter-spacing: 0.5px;
	}

	.issue {
		background: #f9fbff;
		border-left: 5px solid #0077ff;
		border-radius: 12px;
		padding: 1rem;
		margin-bottom: 1rem;
		box-shadow: 0 3px 8px rgba(0, 0, 0, 0.05);
		transition: all 0.2s ease;
	}

	.issue:hover {
		transform: scale(1.02);
		background: #ffffff;
	}

	.issue strong {
		display: block;
		color: #222;
		font-size: 1rem;
		margin-bottom: 0.3rem;
		font-weight: 600;
	}

	.issue p {
		font-size: 0.92rem;
		color: #555;
		line-height: 1.4;
		margin: 0;
	}
</style>

<header>
	<button>+ Neues Issue</button>
</header>

<main>
	{#each lanes as lane}
		<div class="lane">
			<h2>{lane}</h2>
			{#each issues.filter(issue => issue.lane === lane) as issue}
				<div class="issue">
					<strong>{issue.title}</strong>
					<p>{issue.description}</p>
				</div>
			{/each}
		</div>
	{/each}
</main>

