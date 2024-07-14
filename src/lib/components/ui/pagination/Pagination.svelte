<script lang="ts">
  import { createEventDispatcher } from 'svelte';

  export let totalItems: number;
  export let itemsPerPage: number;
  export let currentPage: number;

  const dispatch = createEventDispatcher();

  let totalPages = Math.ceil(totalItems / itemsPerPage);
  const maxPagesToShow = 5;

  function changePage(page: number) {
    if (page > 0 && page <= totalPages) {
      dispatch('pageChange', page);
    }
  }

  function getPagesToShow() {
    const halfMaxPagesToShow = Math.floor(maxPagesToShow / 2);
    let startPage = Math.max(currentPage - halfMaxPagesToShow, 1);
    let endPage = Math.min(startPage + maxPagesToShow - 1, totalPages);

    if (endPage - startPage + 1 < maxPagesToShow) {
      startPage = Math.max(endPage - maxPagesToShow + 1, 1);
    }

    return Array.from({ length: (endPage - startPage + 1) }, (_, i) => startPage + i);
  }

  $: totalPages = Math.ceil(totalItems / itemsPerPage);
</script>

<div class="flex items-center space-x-2">
  <button
    class="px-2 py-1 cursor-pointer text-white rounded-md bg-zinc-800 shadow-sm hover:bg-zinc-700"
    on:click={() => changePage(currentPage - 1)}
    disabled={currentPage === 1}
  >
    Anterior
  </button>

  {#if totalPages > maxPagesToShow}
    {#if getPagesToShow()[0] > 1}
      <button
        class="px-2 py-1 rounded-md bg-zinc-800 shadow-sm hover:bg-zinc-700"
        on:click={() => changePage(1)}
      >
        1
      </button>
      {#if getPagesToShow()[0] > 2}
        <span class="px-2 py-1 rounded-md bg-zinc-800 shadow-sm">...</span>
      {/if}
    {/if}
  {/if}

  {#each getPagesToShow() as page}
    <button
      class="px-2 py-1 text-white rounded-md bg-zinc-800 shadow-sm hover:bg-zinc-700"
      class:selected={currentPage === page}
      on:click={() => changePage(page)}
    >
      {page}
    </button>
  {/each}

  {#if totalPages > maxPagesToShow}
    {#if getPagesToShow().slice(-1)[0] < totalPages}
      {#if getPagesToShow().slice(-1)[0] < totalPages - 1}
        <span class="px-2 py-1 rounded-md bg-zinc-800 shadow-sm">...</span>
      {/if}
      <button
        class="px-2 py-1 text-white rounded-md bg-zinc-800 shadow-sm hover:bg-zinc-700"
        on:click={() => changePage(totalPages)}
      >
        {totalPages}
      </button>
    {/if}
  {/if}

  <button
    class="px-2 py-1 cursor-pointer text-white rounded-md bg-zinc-800 shadow-sm hover:bg-zinc-700"
    on:click={() => changePage(currentPage + 1)}
    disabled={currentPage === totalPages}
  >
    Próximo
  </button>
</div>

<style>
  .selected {
    background-color: #007bff;
    color: white;
  }
</style>
