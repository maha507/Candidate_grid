<script>
	import { onMount } from "svelte";
	import Grid from "gridjs-svelte";
  
	let jsonData = [];
	let tableVisible = false;
  
	onMount(async () => {
	  const response = await fetch("https://api.recruitly.io/api/job?apiKey=TEST64518616D4CF145D4E20BD47169EA7229BA3");
	  const responseData = await response.json();
	  jsonData = responseData.data;
	});
  
	function openTable() {
	  if (!tableVisible) {
		fetchData();
	  }
	}
  
	async function fetchData() {
	  const response = await fetch("https://api.recruitly.io/api/job?apiKey=TEST64518616D4CF145D4E20BD47169EA7229BA3");
	  const responseData = await response.json();
	  jsonData = responseData.data;
	  tableVisible = true;
	}
  </script>
  
  <button on:click={openTable}>Display Table</button>
  
  <main>
	{#if tableVisible}
	  <Grid
		search
		sort
		pagination={{ enabled: true, limit: 38 }}
		data={jsonData.map(item => ({
		  title: item.title,
		  reference: item.reference,
		  status: item.status
		}))} />
	{/if}
  </main>
  
  <style global>
	@import "https://cdn.jsdelivr.net/npm/gridjs/dist/theme/mermaid.min.css";
  </style>