<script>
    import { onMount } from 'svelte';
    import IssueCard from './IssueCard.svelte';
  
    let lanes = ["To Do", "Doing", "Done", "Archiv"];
    let issues = []; // empty initially
  
    // Dialog form fields
    let dialog;
    let newTitle = '';
    let newDesc = '';
    let newSP = 1;
    let newPriority = 'Low';
    let newLane = 'To Do';
  
    // Only access localStorage on the browser
    onMount(() => {
      const stored = localStorage.getItem('issues');
      if (stored) {
        issues = JSON.parse(stored);
      } else {
        // Default issues
        issues = [
          { title: "Projekt starten", description: "Grundstruktur aufbauen", lane: "To Do", storyPoints: 3, priority: "High", creationDate: new Date().toLocaleDateString(), dueDate: '' },
          { title: "HTML erstellen", description: "Dialog und Layout", lane: "Doing", storyPoints: 5, priority: "Medium", creationDate: new Date().toLocaleDateString(), dueDate: '' },
          { title: "Design verbessern", description: "Farben & Layout", lane: "Done", storyPoints: 2, priority: "Low", creationDate: new Date().toLocaleDateString(), dueDate: '' },
          { title: "Alte Tasks speichern", description: "Archivierte Elemente", lane: "Archiv", storyPoints: 1, priority: "Low", creationDate: new Date().toLocaleDateString(), dueDate: '' }
        ];
        localStorage.setItem('issues', JSON.stringify(issues));
      }
    });
  
    function saveIssues() {
      localStorage.setItem('issues', JSON.stringify(issues));
    }
  
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
        dueDate: ''
      };
      issues = [...issues, newIssue];
      saveIssues();
  
      // reset form
      newTitle = '';
      newDesc = '';
      newSP = 1;
      newPriority = 'Low';
      newLane = 'To Do';
  
      closeDialog();
    }
  
    function getStoryPoints(lane) {
      return issues
        .filter(issue => issue.lane === lane)
        .reduce((sum, issue) => sum + Number(issue.storyPoints), 0);
    }
  </script>
  
  <header>
    <h1>Kanban Board</h1>
    <button on:click={openDialog}>+ Neues Issue</button>
  </header>
  
  <main>
    {#each lanes as lane}
      <div class="lane">
        <h2>{lane} ({getStoryPoints(lane)} SP)</h2>
        {#each issues.filter(issue => issue.lane === lane) as issue}
          <IssueCard {issue} />
        {/each}
      </div>
    {/each}
  </main>
  
  <dialog bind:this={dialog}>
    <h2>Neues Issue</h2>
    <form on:submit|preventDefault={addIssue}>
      <label>Titel:</label>
      <input type="text" bind:value={newTitle} required />
  
      <label>Beschreibung:</label>
      <textarea bind:value={newDesc} required></textarea>
  
      <label>Story Points:</label>
      <input type="number" min="1" bind:value={newSP} />
  
      <label>Priorität:</label>
      <select bind:value={newPriority}>
        <option>Low</option>
        <option>Medium</option>
        <option>High</option>
      </select>
  
      <label>Lane:</label>
      <select bind:value={newLane}>
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
      background: #fff;
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
  