<script>

  import { onMount } from "svelte";

  import Grid from "gridjs-svelte";
  let jsonData = [];
  onMount(async () => {

    await fetchData();

  });

  async function fetchData() {

    const response = await fetch("https://api.recruitly.io/api/jobapiKey=TEST64518616D4CF145D4E20BD47169EA7229BA3");

    const responseData = await response.json();

    jsonData = responseData.data;

  }

  function titleFormatter(cell) {

    return '<a href="#">${cell}</a>';

  }

</script>
<main>

  <Grid

    search

    sort

    pagination={{ enabled: true, limit: 10 }}

    data={jsonData}

    columns={[

      {

        name: "Title",

        formatter: titleFormatter

      },

      {

        name: "Reference",

        selector: "reference"

      },

      {

        name: "Status",

        selector: "status"

      }

    ]} />

</main>



<style global>

  @import "https://cdn.jsdelivr.net/npm/gridjs/dist/theme/mermaid.min.css";

</style>