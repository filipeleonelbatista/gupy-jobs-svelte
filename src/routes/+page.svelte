<script lang="ts">
  import { onMount } from 'svelte';
  import ListItem from '$lib/components/ui/list-item/list-item.svelte';
  import * as Select from "$lib/components/ui/select";
  import { Label } from "$lib/components/ui/label";
  import Pagination from '$lib/components/ui/pagination/Pagination.svelte';
  import { Input } from "$lib/components/ui/input";
  import { Button } from "$lib/components/ui/button";

  let jobs = [];
  let pagination = {
    offset: 0,
    limit: 10,
    total: 0,
  };
  let currentPage = 1;
  let isLoading = true; 
  let selectedArea = 'Desenvolvedor Front-end'; 
  let selectedRemote = true; 

  async function fetchJobs(page: number = 1) {
    const api = `https://cors-anywhere-filipeleonelbatista.onrender.com/https://portal.api.gupy.io/api/v1/jobs?`;
    const jobName = `&jobName=${selectedArea}`;
    const type = `&type=vacancy_type_effective`;
    const isRemoteWork = `&isRemoteWork=${selectedRemote}`;
    const limit = `&limit=${pagination.limit}`;
    const offset = `&offset=${(page - 1) * pagination.limit}`;
    console.log(`${api}${limit}${offset}${jobName}${isRemoteWork}${type}`)
    const response = await fetch(`${api}${limit}${offset}${jobName}${isRemoteWork}${type}`);
    const data = await response.json();
    jobs = data.data;
    pagination.total = data.pagination.total;
    currentPage = page;
    isLoading = false; 
  }

  onMount(() => {
    fetchJobs();
  });

  function handleAreaChange(event) {
    selectedArea = event.target.value;
  }

  function handleRemoteChange(v) {
    selectedRemote = v.value == "true";
  }
</script>

<main class="flex flex-col w-full min-h-screen bg-gray-900 px-4">
  <div class="flex w-full items-center justify-center p-6">
    <h2 class="text-2xl text-center text-white font-bold uppercase">
      Gupy Jobs <small>v1.2</small>
    </h2>
  </div>

  <div class="flex w-full justify-center mb-4">
    <div class="flex w-full sm:flex-col md:flex-row max-w-[1024px] gap-4">
      <div class="w-full flex flex-col gap-2">
        <Label class="text-white" for="area">Área de atuação:</Label>
        <Input type="text" on:change={handleAreaChange} value={selectedArea} />        
      </div>

      <div class="w-full flex flex-col gap-2">
        <Label class="text-white" for="area">Remoto:</Label>
        <Select.Root onSelectedChange={handleRemoteChange}>
          <Select.Trigger>
            <Select.Value placeholder="Selecione..." />
          </Select.Trigger>
          <Select.Content>
            <Select.Item value="true">Sim</Select.Item>
            <Select.Item value="false">Não</Select.Item>
          </Select.Content>
        </Select.Root>
      </div>

      <Button on:click={() => fetchJobs()}  variant="secondary" class="bg-blue-500 hover:bg-blue-700 text-white">
        Buscar
      </Button>

    </div>
  </div>

  <div class="flex-1 flex w-full overflow-auto items-center justify-center">
    {#if isLoading}
      <p class="text-white text-center">Carregando...</p>
    {:else}
      {#if jobs.length === 0}
        <p class="text-white text-center">Sem conteúdo disponível.</p>
      {:else}
        <div class="grid sm:grid-cols-1 md:grid-cols-3 max-w-[1024px] flex-wrap gap-4">
          {#each jobs as job}
            <ListItem
              empresa={job.careerPageName}
              jobPosition={job.name}
              description={job.description}
              imageUrl={job.careerPageLogo}
              link={job.jobUrl}
              region={selectedRemote ? job.country : `${job.city}, ${job.state}, ${job.country}`}
              publishedDate={job.publishedDate}
              isRemoteWork={job.isRemoteWork}
              hasFriendlyBadge={job.badges?.friendlyBadge}
            />
          {/each}
        </div>
      {/if}
    {/if}
  </div>

<!-- removido por que precisa de acessos com auth na api
  <div class="flex w-full items-center justify-center p-6">
    <Pagination
      totalItems={pagination.total}
      itemsPerPage={pagination.limit}
      currentPage={currentPage}
      on:pageChange={fetchJobs}
    />
  </div>
-->
  <div class="flex w-full items-center justify-center p-6">
    <p class="text-sm text-white">
      Feito com <span class="font-bold">SVELTE</span> e 💙 por <a class="underline text-blue-600 font-bold" target="_blank" href="https://filipeleonelbatista.dev.br">Filipe Batista</a>
    </p>
  </div>
</main>
