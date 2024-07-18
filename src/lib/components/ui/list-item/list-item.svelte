<script lang="ts">
  export let empresa: string;
  export let jobPosition: string;
  export let description: string;
  export let imageUrl: string;
  export let link: string;
  export let region: string;
  export let publishedDate: string;
  export let isRemoteWork: boolean;
  export let hasFriendlyBadge: boolean;
</script>

<div class="w-full flex flex-col items-center p-4 rounded-lg bg-zinc-950 shadow-sm gap-4">
  <div class="w-full flex flex-row items-center">
    <img class="w-14 h-14 object-cover rounded-full mr-4" src={imageUrl} alt={empresa} />
    <div class="w-full flex flex-col">
      <h4 class="text-xl text-white font-semibold mb-1">{empresa}</h4>    
      <p class="text-white font-semibold mb-1">{jobPosition}</p>
    </div>
  </div>
  <div class="w-full flex flex-col gap-2">    
    <div>
      <p class="text-gray-300">Vaga publicada em: {new Date(publishedDate).toLocaleDateString()}</p>
      <p class="text-gray-300">Região: {region}</p>
      <p class="text-gray-300">Trabalho remoto: {isRemoteWork ? 'Sim' : 'Não'}</p>
      <p class="text-gray-300 mb-4">Tem Selo Gupy: {hasFriendlyBadge ? 'Sim' : 'Não'}</p>
    </div>
    <p class="text-gray-300 mb-2">{description.substr(0, 150)}</p>
    <a class="w-fit px-4 py-2 text-sm font-medium text-white bg-blue-500 rounded-md hover:bg-blue-600" href={link} target="_blank">
      Ir para vaga
    </a>
  </div>
</div>
