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

            // ✅ Notification when moved to Done
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

<!-- Root container -->
<div class="flex flex-col min-h-screen font-marker text-gray-900 dark:bg-gray-900 dark:text-gray-100 transition-colors duration-300">

    <!-- Header -->
    <header class="flex flex-col sm:flex-row justify-between items-center p-6 bg-purple-100 dark:bg-gray-800 shadow-md rounded-b-xl">
        <h1 class="text-3xl sm:text-4xl font-bold tracking-tight mb-4 sm:mb-0 text-purple-900 dark:text-purple-200">
            My Kanban Board
        </h1>

        <div class="flex gap-3 flex-wrap justify-center">
            <button
                class="px-5 py-2.5 bg-purple-300 hover:bg-purple-400 dark:bg-gray-700 dark:hover:bg-gray-600 text-gray-900 dark:text-white font-bold rounded-lg shadow transition"
                on:click={handleExportCSV}
            >
                Export CSV
            </button>

            <button
                class="px-5 py-2.5 bg-purple-400 hover:bg-purple-500 text-white font-bold rounded-lg shadow transition"
                on:click={() => (modalOpen = true)}
            >
                Add Issue
            </button>

            <button
                class="px-5 py-2.5 border border-purple-400 rounded-lg font-semibold dark:text-purple-300 hover:bg-purple-100 dark:hover:bg-gray-700 transition"
                on:click={toggleDarkMode}
            >
                {darkMode ? "☀️ Light Mode" : "🌙 Dark Mode"}
            </button>
        </div>
    </header>

    <!-- Board -->
    <main class="flex-1 p-6 grid gap-6 sm:grid-cols-2 lg:grid-cols-4">
        {#each lanes as lane}
            <!-- svelte-ignore a11y_no_static_element_interactions -->
            <section
                class="flex flex-col rounded-xl shadow-md border border-gray-200 dark:border-gray-700 bg-white dark:bg-gray-800 hover:shadow-lg transition-colors duration-300"
                on:drop={(e) => handleDrop(e, lane)}
                on:dragover={handleDragOver}
            >
                <header
                    class={`text-center py-3 font-bold rounded-t-xl border-b 
                        ${lane === 'To Do' ? 'bg-purple-400 text-white border-purple-500' : ''}
                        ${lane === 'Doing' ? 'bg-purple-400 text-white border-purple-500' : ''}
                        ${lane === 'Done' ? 'bg-purple-400 text-white border-purple-500' : ''}
                        ${lane === 'Archive' ? 'bg-gray-200 text-gray-800 border-gray-300 dark:bg-gray-700 dark:text-gray-200' : ''}`}
                >
                    {lane} <span class="font-medium">({$storyPointsSum[lane] ?? 0} SP)</span>
                </header>

                <div class="p-4 space-y-4 min-h-[160px]">
                    {#if !$board[lane] || $board[lane].length === 0}
                        <div class="text-gray-500 text-sm text-center italic">No issues yet</div>
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
    <footer class="p-4 text-center text-gray-700 dark:text-gray-300 bg-purple-100 dark:bg-gray-800 border-t border-purple-300 dark:border-gray-700 font-bold">
        Detected Country: <span class="text-orange-900 dark:text-orange-400">{userCountry}</span>
    </footer>
</div>

<AddIssueModal bind:open={modalOpen} />
