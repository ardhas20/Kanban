<script lang="ts">
    import { board, storyPointsSum } from "$lib/stores/boardStore";
    import AddIssueModal from "$lib/components/AddIssueModal.svelte";
    import IssueCard from "$lib/components/IssueCard.svelte";
    import { exportBoardAsCSV } from "$lib/utils/exportCSV";
    import { onMount } from "svelte";

    let modalOpen = false;
    const lanes = ["To Do", "Doing", "Done", "Archive"];
    let userCountry = "";
    let darkMode = false;

    onMount(async () => {
        try {
            const res = await fetch("https://ipapi.co/json/");
            const data = await res.json();
            userCountry = data.country_name || "Unknown";
        } catch {
            userCountry = "Unknown";
        }
    });

    const handleExportCSV = () => {
        exportBoardAsCSV($board);
    };

    const handleDrop = (e: DragEvent, toLane: string) => {
        e.preventDefault();
        const data = e.dataTransfer?.getData("text/plain");
        if (!data) return;

        const { id, fromLane } = JSON.parse(data);
        if (fromLane === toLane) return;

        board.update(b => {
            const itemIndex = b[fromLane].findIndex(i => i.id === id);
            if (itemIndex === -1) return b;
            const [moved] = b[fromLane].splice(itemIndex, 1);
            b[toLane].push(moved);

            if (toLane === "Done" && "Notification" in window) {
                if (Notification.permission === "granted") {
                    new Notification(`✅ "${moved.title}" moved to Done`);
                } else if (Notification.permission !== "denied") {
                    Notification.requestPermission().then(p => {
                        if (p === "granted") {
                            new Notification(`✅ "${moved.title}" moved to Done`);
                        }
                    });
                }
            }

            return { ...b };
        });
    };

    const handleDragOver = (e: DragEvent) => e.preventDefault();

    const toggleDarkMode = () => {
        darkMode = !darkMode;
        document.documentElement.classList.toggle("dark", darkMode);
    };
</script>

<div class="flex flex-col min-h-screen font-marker text-gray-800 dark:text-gray-100 dark:bg-gray-900">

    <!-- Header -->
    <header class="flex flex-col sm:flex-row justify-between items-center p-6 bg-gradient-to-r from-purple-500 to-purple-700 dark:from-gray-800 dark:to-gray-900 shadow-xl rounded-b-3xl mb-6">
        <h1 class="text-3xl sm:text-4xl font-extrabold tracking-wide text-white mb-4 sm:mb-0">
             My Kanban Board
        </h1>
        <div class="flex flex-wrap gap-3">
            <button class="px-5 py-2.5 rounded-xl bg-white text-purple-700 font-bold shadow" on:click={handleExportCSV}>Export CSV</button>
            <button class="px-5 py-2.5 rounded-xl bg-purple-600 text-white font-bold shadow" on:click={() => modalOpen = true}>Add Issue</button>
            <button class="px-5 py-2.5 rounded-xl bg-gray-200 text-gray-800 font-semibold shadow" on:click={() => board.undo()}> Undo</button>
            <button class="px-5 py-2.5 rounded-xl bg-gray-200 text-gray-800 font-semibold shadow" on:click={() => board.redo()}>Redo</button>
            <button class="px-5 py-2.5 rounded-xl border border-white font-semibold shadow" on:click={toggleDarkMode}>
                {darkMode ? " Light Mode" : " Dark Mode"}
            </button>
        </div>
    </header>

    <!-- Board -->
    <main class="flex-1 p-4 grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        {#each lanes as lane}
            <!-- svelte-ignore a11y_no_static_element_interactions -->
            <section
                class={`flex flex-col rounded-2xl shadow-lg border p-3
                    ${darkMode ? 'border-gray-600 bg-gray-800' : 'border-gray-300 bg-purple-50'}`}
                on:drop={(e) => handleDrop(e, lane)}
                on:dragover={handleDragOver}
            >
                <header class={`text-center py-2 font-bold rounded-xl mb-3
                    ${lane === 'Archive'
                        ? 'bg-gray-300 dark:bg-gray-600 text-gray-800 dark:text-gray-100'
                        : darkMode ? 'bg-purple-700 text-white' : 'bg-purple-600 text-white'}`}
                >
                    {lane} <span class="font-medium">({$storyPointsSum[lane] ?? 0} SP)</span>
                </header>

                <div class="flex flex-col gap-3 min-h-[180px]">
                    {#if !$board[lane] || $board[lane].length === 0}
                        <div class="text-gray-500 dark:text-gray-300 text-sm italic text-center py-4">No issues yet</div>
                    {:else}
                        {#each $board[lane] as issue (issue.id)}
                            <IssueCard {issue} lane={lane} />
                        {/each}
                    {/if}
                </div>
            </section>
        {/each}
    </main>

    <!-- Footer -->
    <footer class="p-4 text-center text-gray-700 dark:text-gray-300 bg-gradient-to-t from-purple-100 to-purple-200 dark:from-gray-800 dark:to-gray-900 border-t border-gray-300 dark:border-gray-700 font-semibold">
        Detected Country: <span class="text-orange-900 dark:text-orange-400">{userCountry}</span>
    </footer>

</div>

<AddIssueModal bind:open={modalOpen} />
