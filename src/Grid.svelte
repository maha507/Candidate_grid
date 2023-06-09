<script>
	import { onMount } from 'svelte';
	import { RevoGrid } from "@revolist/svelte-datagrid";
	import { defineCustomElements } from "@revolist/revogrid/loader";
	
	let source;
	let headers;
	let isAddCandidatePopupOpen = false;
	let newCandidate = {
	  firstName: "",
	  surname: "",
	  email: "",
	  mobile: ""
	};
	
	// Fetch data from API
	onMount(() => {
	  fetch("https://api.recruitly.io/api/candidate?apiKey=TEST1236C4CF23E6921C41429A6E1D546AC9535E")
		.then(response => response.json())
		.then(data => {
		  console.log(data); // Add this line to inspect the fetched data
	
		  if (Array.isArray(data.data)) {
			// Extract only the id and firstName fields from the fetched data
			source = data.data.map(candidate => ({
			  ID: candidate.id,
			  FirstName: candidate.firstName,
			  Surname: candidate.surname,
			  Email: candidate.email,
			  Mobile: candidate.mobile
			}));
			headers = [...Object.keys(source[0]), 'Actions'].map(key => ({ name: key, prop: key }));
		  } else {
			console.error("Fetched data is not an array:", data);
		  }
		})
		.catch(error => {
		  console.error("Error fetching data:", error);
		});
	});
	
	// Call defineCustomElements to initialize the Revogrid custom elements
	defineCustomElements();
	
	function openAddCandidatePopup() {
	  isAddCandidatePopupOpen = true;
	}
	
	function closeAddCandidatePopup() {
	  isAddCandidatePopupOpen = false;
	  resetNewCandidate();
	}
	
	function resetNewCandidate() {
	  newCandidate = {
		firstName: "",
		surname: "",
		email: "",
		mobile: ""
	  };
	}
	
	async function addCandidate() {
  try {
    const response = await fetch("https://api.recruitly.io/api/candidate?apiKey=TEST1236C4CF23E6921C41429A6E1D546AC9535E", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(newCandidate),
    });

    if (response.ok) {
      console.log("Candidate added successfully!");
      // Refresh the data after adding the candidate
      refreshData();
    } else {
      console.error("Failed to add candidate:", response.status, response.statusText);
    }

    // Reset the form and close the popup
    resetNewCandidate();
    closeAddCandidatePopup();
  } catch (error) {
    console.error("Error adding candidate:", error);
  }
}

	
	function refreshData() {
	  // Fetch data from the API again to refresh the grid
	  console.log("Refreshing data...");
	
	  fetch("https://api.recruitly.io/api/candidate?apiKey=TEST1236C4CF23E6921C41429A6E1D546AC9535E")
		.then(response => response.json())
		.then(data => {
		  console.log(data); // Add this line to inspect the fetched data
	
		  if (Array.isArray(data.data)) {
			// Extract only the id and firstName fields from the fetched data
			source = data.data.map(candidate => ({
			  ID: candidate.id,
			  FirstName: candidate.firstName,
			  Surname: candidate.surname,
			  Email: candidate.email,
			  Mobile: candidate.mobile
			}));
		  } else {
			console.error("Fetched data is not an array:", data);
		  }
		})
		.catch(error => {
		  console.error("Error fetching data:", error);
		});
	}
	
	function editCandidate(candidate) {
	  console.log("Edit candidate:", candidate);
	  // Add your edit logic here
	}
	
	async function deleteCandidate(candidate) {
  try {
    const response = await fetch(`https://api.recruitly.io/api/candidate/${candidate.ID}?apiKey=TEST1236C4CF23E6921C41429A6E1D546AC9535E`, {
      method: "DELETE",
    });

    if (response.ok) {
      console.log("Candidate deleted successfully!");
      // Refresh the data after deleting the candidate
      refreshData();
    } else {
      console.error("Failed to delete candidate:", response.status, response.statusText);
    }
  } catch (error) {
    console.error("Error deleting candidate:", error);
  }
}

  </script>
  
  <style>
	@import 'bootstrap/dist/css/bootstrap.min.css';
	/* Your existing styles */
	revo-grid {
	  height: 100%;
	}
	
	main {
	  font-family: sans-serif;
	  text-align: center;
	  height: calc(80vh);
	}
	
	.popup {
	  position: fixed;
	  top: 0;
	  left: 0;
	  right: 0;
	  bottom: 0;
	  display: flex;
	  justify-content: center;
	  align-items: center;
	  background-color: rgba(0, 0, 0, 0.5);
	  z-index: 9999;
	}
	
	.popup-inner {
	  width: 300px;
	  padding: 20px;
	  background-color: #fff;
	  border-radius: 4px;
	}
  </style>
  
  <main>
	<div class="mb-3 d-flex justify-content-between">
	  <button class="btn btn-primary" on:click={openAddCandidatePopup}>Add Candidate</button>
	 
	</div>
  
	{#if source && headers}
	<RevoGrid class="grid" source={source} resize="true" columns={headers} theme="material">
        <template slot="cell-template" let-cell>
            <!-- Custom cell rendering based on the column properties or data -->
            {#if cell.column.prop === "Actions"}
              <div class="d-flex justify-content-center">
                <a href="#" class="btn btn-primary btn-sm" on:click={() => editCandidate(cell.row.data)}>Edit</a>
                <a href="#" class="btn btn-danger btn-sm" on:click={() => deleteCandidate(cell.row.data)}>Delete</a>
              </div>
            {:else if cell.column.prop === "FirstName"}
              <div class="d-flex justify-content-center">
                <a href="#">{cell.data}</a>
              </div>
            {:else}
              {cell.data}
            {/if}
          </template> 
	</RevoGrid>
	{:else}
	<p>Loading data...</p>
	{/if}
  
	{#if isAddCandidatePopupOpen}
	<div class="popup">
	  <div class="popup-inner">
		<h3>Add Candidate</h3>
		<form>
		  <div class="mb-3">
			<label for="firstName" class="form-label">First Name</label>
			<input type="text" id="firstName" bind:value={newCandidate.firstName} class="form-control" />
		  </div>
		  <div class="mb-3">
			<label for="surname" class="form-label">Surname</label>
			<input type="text" id="surname" bind:value={newCandidate.surname} class="form-control" />
		  </div>
		  <div class="mb-3">
			<label for="email" class="form-label">Email</label>
			<input type="email" id="email" bind:value={newCandidate.email} class="form-control" />
		  </div>
		  <div class="mb-3">
			<label for="mobile" class="form-label">Mobile</label>
			<input type="text" id="mobile" bind:value={newCandidate.mobile} class="form-control" />
		  </div>
		  <div class="d-flex justify-content-between">
			<button type="button" class="btn btn-secondary" on:click={closeAddCandidatePopup}>Cancel</button>
			<button type="button" class="btn btn-primary" on:click={addCandidate}>Add</button>
		  </div>
		</form>
	  </div>
	</div>
	{/if}
  </main>