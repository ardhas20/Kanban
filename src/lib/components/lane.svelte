<script lang="ts">
    import IssueCard from "./IssueCard.svelte";
    import { board } from "$lib/stores/boardStore";
    import { get } from "svelte/store";
    import { createEventDispatcher } from "svelte";
  
    export let title: string;
    export let color: string;
    const dispatch = createEventDispatcher();
  
    let storyPointsSum = 0;
  
    $: issues = get(board)[title];
    $: storyPointsSum = issues?.reduce((sum, i) => sum + (i.storyPoints || 0), 0) || 0;
  
    function handleDrop(e) {
      dispatch("dropItem", e, title);
    }
  
    function handleDragOver(e) {
      e.preventDefault();
    }
  </script>
  
  <div
    class="flex flex-col rounded-2xl shadow-md p-4 transition-colors"
    class:bg-green-50={color === "bg-green-100"}
    class:bg-green-100={color === "bg-green-200"}
    class:bg-green-200={color === "bg-green-300"}
    class:bg-lime-100={color === "bg-lime-200"}
    on:drop={(e) => handleDrop(e)}
    on:dragover={handleDragOver}
  >
    <header class="flex justify-between items-center mb-2 font-marker">
      <h2 class="text-xl font-bold">{title}</h2>
      <span class="text-sm bg-green-700 text-white px-2 py-1 rounded-full">{storyPointsSum} SP</span>
    </header>
  
    <div class="flex flex-col gap-3">
      {#each issues as issue (issue.id)}
        <IssueCard {issue} lane={title} />
      {/each}
    </div>
  </div>
  
  <style>
    .font-marker {
      font-family: 'Permanent Marker', cursive;
    }
  </style>
  