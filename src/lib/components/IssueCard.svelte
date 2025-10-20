<script lang="ts">
  import { board } from "$lib/stores/boardStore";
  export let issue;
  export let lane: string;

  const formatDate = (d: string) => new Date(d).toLocaleDateString();
  const isOverdue = (d: string) => d && new Date() > new Date(d);

  const removeIssue = () => {
    board.update(b => {
      b[lane] = b[lane].filter(i => i.id !== issue.id);
      return b;
    });
  };

  const exportICS = () => {
    const start = new Date(issue.dueDate).toISOString().replace(/[-:]/g, "").split(".")[0];
    const end = new Date(new Date(issue.dueDate).getTime() + 3600000)
      .toISOString()
      .replace(/[-:]/g, "")
      .split(".")[0];

    const icsContent = `BEGIN:VCALENDAR
VERSION:2.0
BEGIN:VEVENT
SUMMARY:${issue.title}
DESCRIPTION:${issue.description}
DTSTART:${start}Z
DTEND:${end}Z
END:VEVENT
END:VCALENDAR`;

    const blob = new Blob([icsContent], { type: "text/calendar" });
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = `${issue.title}.ics`;
    a.click();
    URL.revokeObjectURL(url);
  };

  const shareIssue = async () => {
    const text = `Issue: ${issue.title}\nPriority: ${issue.priority}\nDue: ${formatDate(issue.dueDate)}\n${issue.description}`;
    if (navigator.share) {
      try {
        await navigator.share({ title: issue.title, text });
      } catch (err) {
        console.error("Share cancelled or failed:", err);
      }
    } else {
      alert("Sharing is not supported on this device.");
    }
  };
</script>

<!-- Issue Card -->
<!-- svelte-ignore a11y_no_static_element_interactions -->
<div
  class="bg-purple-50 rounded-2xl shadow p-4 border border-purple-200 font-marker hover:shadow-lg transition-shadow cursor-grab relative"
  draggable="true"
  on:dragstart={(e) => e.dataTransfer.setData('text/plain', JSON.stringify({ id: issue.id, fromLane: lane }))}
>
  <!-- Top row: title + remove -->
  <div class="flex justify-between items-start mb-2">
    <div>
      <h3 class="font-bold text-purple-900">{issue.title}</h3>
      {#if issue.description}
        <p class="text-purple-700 text-sm mt-1">{issue.description}</p>
      {/if}
    </div>
    <button
      on:click={removeIssue}
      class="text-red-600 hover:text-red-800 text-sm font-semibold ml-2"
      title="Remove issue"
    >
      ✕
    </button>
  </div>

  <!-- Bottom: due date + SP -->
  <div class="flex justify-between items-center mt-3 text-xs text-purple-600">
    <p>
      Due: {issue.dueDate ? formatDate(issue.dueDate) : "—"}
      {#if isOverdue(issue.dueDate)}
        <span class="text-red-600 font-bold ml-1">⚠️</span>
      {/if}
    </p>
    <span class="font-medium text-purple-800">{issue.storyPoints} SP</span>
  </div>

  <!-- Action buttons -->
  <div class="flex gap-2 mt-3 text-sm">
    <button
      on:click={exportICS}
      class="text-purple-800 border border-purple-300 px-2 py-1 rounded-lg hover:bg-purple-100 transition"
    >
      Export
    </button>
    <button
      on:click={shareIssue}
      class="text-purple-800 border border-purple-300 px-2 py-1 rounded-lg hover:bg-purple-100 transition"
    >
      Share
    </button>
  </div>
</div>
