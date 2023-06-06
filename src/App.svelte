<script>
  import { onMount } from "svelte";
  import Grid from "gridjs-svelte";

  let jsonData = [];

  onMount(async () => {
    await fetchData();
  });

  async function fetchData() {
    const response = await fetch("https://api.recruitly.io/api/job?apiKey=TEST64518616D4CF145D4E20BD47169EA7229BA3");
    const responseData = await response.json();
    jsonData = responseData.data.map(item => ({
      ...item,
      actions: {
        edit: `<button onclick="editJob(${item.id})">Edit</button>`,
        delete: `<button onclick="deleteJob(${item.id})">Delete</button>`
      }
    }));
  }

  function editJob(jobId) {
    // Handle edit logic here
    console.log("Edit job:", jobId);
  }

  function deleteJob(jobId) {
    // Handle delete logic here
    console.log("Delete job:", jobId);
  }
</script>

<main>
  <Grid
    search
    sort
    pagination={{ enabled: true, limit: 38 }}
    data={jsonData.map(item => ({
      title: item.title,
      reference: item.reference,
      status: item.status,
      edit: item.actions.edit,
      delete: item.actions.delete
    }))}
    columns={[
      "Title",
      "Reference",
      "Status",
      "Edit",
      "Delete"
    ]}
  />
</main>

<style global>
  @import "https://cdn.jsdelivr.net/npm/gridjs/dist/theme/mermaid.min.css";
</style>

