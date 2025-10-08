<script>
    import IssueCard from './IssueCard.svelte';
  
    let lanes = ["To Do", "Doing", "Done", "Archiv"];
    let issues = [
      { title: "Projekt starten", description: "Grundstruktur aufbauen", lane: "To Do", storyPoints: 3, priority: "High" },
      { title: "HTML erstellen", description: "Dialog und Layout", lane: "Doing", storyPoints: 5, priority: "Medium" },
      { title: "Design verbessern", description: "Farben & Layout", lane: "Done", storyPoints: 2, priority: "Low" },
      { title: "Alte Tasks speichern", description: "Archivierte Elemente", lane: "Archiv", storyPoints: 1, priority: "Low" }
    ];
  
    let dialog;
  
    // Form Inputs
    let newTitle = '';
    let newDesc = '';
    let newSP = 1;
    let newPriority = 'Low';
    let newLane = 'To Do';
  
    function openDialog() {
      dialog.showModal();
    }
  
    function closeDialog() {
      dialog.close();
    }
  
    function addIssue() {
      const newIssue = {
        title: newTitle,
        description: newDesc,
        lane: newLane,
        storyPoints: newSP,
        priority: newPriority,
        creationDate: new Date().toLocaleDateString(),
        dueDate: '' // noch leer, kommt später
      };
      issues = [...issues, newIssue];
  
      // Reset form
      newTitle = '';
      newDesc = '';
      newSP = 1;
      newPriority = 'Low';
      newLane = 'To Do';
  
      closeDialog();
    }
  </script>
  
  <header>
    <h1>Kanban Board</h1>
    <button on:click={openDialog}>+ Neues Issue</button>
  </header>
  
  <main>
    {#each lanes as lane}
      <div class="lane">
        <h2>{lane}</h2>
        {#each issues.filter(issue => issue.lane === lane) as issue}
          <IssueCard {issue} />
        {/each}
      </div>
    {/each}
  </main>
  
  <dialog bind:this={dialog}>
    <h2>Neues Issue</h2>
    <form on:submit|preventDefault={addIssue}>
      <label for="title">Titel:</label>
      <input id="title" type="text" bind:value={newTitle} placeholder="Titel eingeben" required />
      
      <label for="desc">Beschreibung:</label>
      <textarea id="desc" bind:value={newDesc} placeholder="Beschreibung eingeben" required></textarea>
      
      <label for="sp">Story Points:</label>
      <input id="sp" type="number" min="1" bind:value={newSP} />
      
      <label for="prio">Priorität:</label>
      <select id="prio" bind:value={newPriority}>
        <option>Low</option>
        <option>Medium</option>
        <option>High</option>
      </select>
  
      <label for="lane">Lane:</label>
      <select id="lane" bind:value={newLane}>
        <option>To Do</option>
        <option>Doing</option>
        <option>Done</option>
        <option>Archiv</option>
      </select>
      
      <div class="actions">
        <button type="button" on:click={closeDialog}>Abbrechen</button>
        <button type="submit">Speichern</button>
      </div>
    </form>
  </dialog>
  
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap');
  
    body { font-family: 'Inter', sans-serif; }
  
    header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 1rem 2rem;
      background: #ffffff;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    }
  
    header h1 { font-size: 1.5rem; color: #005fe0; }
  
    header button {
      background: #0077ff; color: white; border: none;
      border-radius: 8px; padding: 0.5rem 1rem; font-size: 1rem; cursor: pointer;
      transition: 0.2s;
    }
  
    header button:hover { background: #005fe0; }
  
    main {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 1.8rem;
      padding: 2rem;
      min-height: 100vh;
      background: linear-gradient(135deg, #e8eef3, #f3f6fa);
    }
  
    .lane {
      background: white;
      border-radius: 18px;
      padding: 1.3rem;
      box-shadow: 0 4px 14px rgba(0,0,0,0.08);
      display: flex;
      flex-direction: column;
      transition: 0.25s ease;
    }
  
    .lane:hover {
      transform: translateY(-6px);
      box-shadow: 0 8px 18px rgba(0,0,0,0.12);
    }
  
    .lane h2 {
      text-align: center;
      color: #005fe0;
      margin-bottom: 1.2rem;
      font-size: 1.4rem;
      font-weight: 600;
      border-bottom: 2px solid #0077ff25;
      padding-bottom: 0.6rem;
      letter-spacing: 0.5px;
    }
  
    dialog {
      border: none;
      border-radius: 16px;
      padding: 1.5rem;
      width: 350px;
      box-shadow: 0 8px 20px rgba(0,0,0,0.2);
      font-family: 'Inter', sans-serif;
    }
  
    dialog::backdrop { background: rgba(0,0,0,0.3); }
  
    form { display: flex; flex-direction: column; gap: 0.6rem; }
  
    label { font-weight: 600; font-size: 0.9rem; color: #333; }
  
    input, textarea, select {
      border: 1px solid #ccc;
      border-radius: 6px;
      padding: 0.5rem;
      font-size: 0.95rem;
    }
  
    .actions { display: flex; justify-content: flex-end; gap: 0.5rem; margin-top: 1rem; }
  
    .actions button {
      border: none; border-radius: 6px; padding: 0.5rem 1rem; font-weight: 600; cursor: pointer;
    }
  
    .actions button[type="button"] { background: #ccc; color: #333; }
    .actions button[type="submit"] { background: #0077ff; color: white; }
  </style>
  