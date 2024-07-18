<script lang="ts">
	import { onMount } from 'svelte';
	import { writable, readable } from 'svelte/store';
	import ListItem from '$lib/components/ui/list-item/list-item.svelte';
	import * as Select from '$lib/components/ui/select';
	import { Label } from '$lib/components/ui/label';
	import Pagination from '$lib/components/ui/pagination/Pagination.svelte';
	import { Input } from '$lib/components/ui/input';
	import { Button } from '$lib/components/ui/button';

	interface Job {
		id: number;
		companyId: number;
		name: string;
		description: string;
		careerPageId: number;
		careerPageName: string;
		careerPageLogo: string;
		type: string;
		publishedDate: string;
		applicationDeadline: string;
		isRemoteWork: boolean;
		city: string;
		state: string;
		country: string;
		jobUrl: string;
		badges: {
			friendlyBadge: boolean;
		};
		disabilities: boolean;
		workplaceType: string;
		careerPageUrl: string;
	}

	let jobs: Job[] = [];

	const pagination = writable({ offset: 0, limit: 10, total: 0 });
	const currentPage = writable(1);

	let isLoading = true;
	let selectedArea = 'Desenvolvedor Front-end';
	let selectedVacancyType = 'vacancy_type_effective';
	let selectedRemote = true;

	async function fetchJobs(page: number = 1) {
		const api = `https://cors-anywhere-filipeleonelbatista.onrender.com/https://portal.api.gupy.io/api/v1/jobs?`;
		const jobName = `&jobName=${selectedArea}`;
		const type = `&type=${selectedVacancyType}`;
		const isRemoteWork = `&isRemoteWork=${selectedRemote}`;
		const limit = `&limit=10`;
		const offset = `&offset=${page}`;
		const response = await fetch(`${api}${limit}${offset}${jobName}${isRemoteWork}${type}`);
		const data = await response.json();
		jobs = data.data;

		pagination.set(data.pagination);
		currentPage.update(() => page);

		jobs = data.data;
		isLoading = false;
	}

	onMount(() => {
		fetchJobs();
	});

	console.log('Ola', $pagination, $currentPage);

	function handleAreaChange(event) {
		selectedArea = event.target.value;
	}

	function handleRemoteChange(v) {
		selectedRemote = v.value == 'true';
	}

	function handleVacancyChange(v) {
		selectedVacancyType = v.value;
	}

	const vacancyDic: {
		[key: string]: string;
	} = {
		vacancy_type_effective: 'Efetivo',
		vacancy_type_internship: 'Estágio'
	};
</script>

<main class="flex min-h-screen w-full flex-col bg-gray-900 px-4">
	<div class="flex w-full items-center justify-center p-6">
		<h2 class="text-center text-2xl font-bold uppercase text-white">
			Gupy Jobs <small>v1.5</small>
		</h2>
	</div>

	<div class="mb-4 flex w-full justify-center">
		<div class="flex flex-col md:flex-row items-end w-full max-w-[1024px] gap-4">
			<div class="flex w-full flex-col gap-2">
				<Label class="text-white" for="area">Área de atuação:</Label>
				<Input type="text" on:change={handleAreaChange} value={selectedArea} />
			</div>

			<div class="flex w-full flex-col gap-2">
				<Label class="text-white" for="area">Remoto:</Label>
				<Select.Root
					selected={{ value: selectedRemote, label: selectedRemote ? 'Sim' : 'Não' }}
					onSelectedChange={handleRemoteChange}
				>
					<Select.Trigger>
						<Select.Value placeholder="Selecione..." />
					</Select.Trigger>
					<Select.Content>
						<Select.Item value="true">Sim</Select.Item>
						<Select.Item value="false">Não</Select.Item>
					</Select.Content>
				</Select.Root>
			</div>

			<div class="flex w-full flex-col gap-2">
				<Label class="text-white" for="area">Tipo:</Label>
				<Select.Root
					selected={{ value: selectedVacancyType, label: vacancyDic[selectedVacancyType] }}
					onSelectedChange={handleVacancyChange}
				>
					<Select.Trigger>
						<Select.Value placeholder="Selecione..." />
					</Select.Trigger>
					<Select.Content>
						<Select.Item value="vacancy_type_effective" label="Efetivo" />
						<Select.Item value="vacancy_type_internship" label="Estágio" />
					</Select.Content>
				</Select.Root>
			</div>

			<Button
				on:click={() => fetchJobs()}
				variant="secondary"
				class="bg-blue-500 text-white hover:bg-blue-700"
			>
				Buscar
			</Button>
		</div>
	</div>

	<div class="flex w-full flex-1 items-center justify-center overflow-auto">
		{#if isLoading}
			<p class="text-center text-white">Carregando...</p>
		{:else if jobs.length === 0}
			<p class="text-center text-white">Sem conteúdo disponível.</p>
		{:else}
			<div class="grid max-w-[1024px] flex-wrap gap-4 sm:grid-cols-1 md:grid-cols-3">
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
	</div>

	{#if !isLoading && jobs.length > 0}
		<div class="flex w-full items-center justify-center p-6">
			<Pagination
				totalItems={$pagination.total}
				itemsPerPage={$pagination.limit}
				currentPage={$currentPage}
				on:pageChange={(event) => fetchJobs(event.detail)}
			/>
		</div>
	{/if}

	<div class="flex w-full items-center justify-center p-6">
		<p class="text-sm text-white">
			Feito com <span class="font-bold">SVELTE</span> e 💙 por
			<a
				class="font-bold text-blue-600 underline"
				target="_blank"
				href="https://filipeleonelbatista.dev.br">Filipe Batista</a
			>
		</p>
	</div>
</main>
