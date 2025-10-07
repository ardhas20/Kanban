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
	main {
		display: grid;
		grid-template-columns: repeat(4, 1fr);
		gap: 1.5rem;
		padding: 2rem;
		min-height: 100vh;
		background: linear-gradient(135deg, #eef2f3, #d9e2ec);
		font-family: 'Poppins', sans-serif;
	}

	.lane {
		background: white;
		border-radius: 15px;
		padding: 1rem;
		box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
		display: flex;
		flex-direction: column;
		transition: 0.3s;
	}
	.lane:hover {
		transform: translateY(-4px);
		box-shadow: 0 6px 15px rgba(0, 0, 0, 0.15);
	}

	h2 {
		text-align: center;
		color: #0077ff;
		margin-bottom: 1rem;
		font-size: 1.3rem;
		border-bottom: 2px solid #0077ff20;
		padding-bottom: 0.5rem;
	}

	.issue {
		background: #f8f9fc;
		border-left: 4px solid #0077ff;
		border-radius: 10px;
		padding: 0.8rem;
		margin-bottom: 0.8rem;
		box-shadow: 0 2px 6px rgba(0, 0, 0, 0.05);
	}

	.issue strong {
		display: block;
		color: #333;
		margin-bottom: 0.2rem;
	}

	.issue p {
		font-size: 0.9rem;
		color: #666;
		margin: 0;
	}
</style>

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
