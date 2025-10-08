<script>
    export let issue;
  
    const getPriorityColor = (priority) => {
      switch (priority) {
        case 'High': return '#ff4d4f'; // rot
        case 'Medium': return '#faad14'; // gelb
        case 'Low': return '#52c41a'; // grün
        default: return '#d9d9d9'; // grau
      }
    };
  </script>
  
  <div class="issue-card">
    <div class="header">
      <h3>{issue.title}</h3>
      <span class="priority" style="background-color: {getPriorityColor(issue.priority)}">
        {issue.priority}
      </span>
    </div>
    <p>{issue.description}</p>
    <div class="meta">
      <span>SP: {issue.storyPoints}</span>
      <span>Created: {issue.creationDate || '-'}</span>
      <span>Due: {issue.dueDate || '-'}</span>
    </div>
  </div>
  
  <style>
  .issue-card {
    background: #f9fbff;
    border-left: 5px solid #0077ff;
    border-radius: 12px;
    padding: 1rem;
    margin-bottom: 1rem;
    box-shadow: 0 3px 8px rgba(0,0,0,0.05);
    transition: all 0.2s ease;
    cursor: grab;
  }
  
  .issue-card:hover {
    transform: scale(1.02);
    background: #ffffff;
  }
  
  .issue-card .header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 0.5rem;
  }
  
  .issue-card .header h3 {
    font-size: 1rem;
    font-weight: 600;
    margin: 0;
  }
  
  .issue-card .priority {
    color: white;
    font-size: 0.75rem;
    padding: 2px 6px;
    border-radius: 6px;
  }
  
  .issue-card p {
    font-size: 0.9rem;
    color: #555;
    margin: 0 0 0.5rem 0;
  }
  
  .issue-card .meta {
    font-size: 0.8rem;
    color: #777;
    display: flex;
    justify-content: space-between;
  }
  </style>
  