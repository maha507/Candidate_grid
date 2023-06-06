<script>
	import { onMount } from 'svelte';
	import { Grid } from 'gridjs';
	import 'bootstrap/dist/css/bootstrap.min.css';
	let grid;
	let jsonData = [];
  
	onMount(async () => {
	  const response = await fetch("https://api.recruitly.io/api/candidate?apiKey=TEST9349C0221517DA4942E39B5DF18C68CDA154");
	  const responseData = await response.json();
	  jsonData = responseData.data;
	  console.log(jsonData, "jsonData");
  
	  grid = new Grid({
		columns: ['firstName', 'surname', 'email', 'mobile', 'Actions'],
		data: jsonData.map((item) => [
		  item.firstName,
		  item.surname,
		  item.email,
		  item.mobile,
		  createActionsCell(item),
		]),
		className: {
		  table: 'gridjs-table',
		  th: 'gridjs-th',
		  td: 'gridjs-td',
		},
		search: true, // Enable search
		sort: true, // Enable sorting
		pagination: {
		  enabled: true, // Enable pagination
		  limit: 10, // Set the number of rows per page
		},
	  }).render(document.getElementById('grid-container'));
	});
  
	function createActionsCell(item) {
	  const editButton = document.createElement('button');
	  editButton.innerHTML = 'Edit';
	  editButton.addEventListener('click', () => {
		handleEdit(item);
	  });
  
	  const deleteButton = document.createElement('button');
	  deleteButton.innerHTML = 'Delete';
	  deleteButton.addEventListener('click', () => {
		confirmDelete(item);
	  });
  
	  const cell = document.createElement('div');
	  cell.appendChild(editButton);
	  cell.appendChild(deleteButton);
  
	  return cell;
	}
  
	async function handleEdit(item) {
	  // Handle edit logic here
	  console.log('Editing', item);
	  // Show edit popup or perform other edit actions
	}
  
	function confirmDelete(item) {
	  const confirmed = confirm('Are you sure you want to delete this item?');
	  if (confirmed) {
		handleDelete(item);
	  }
	}
  
	async function handleDelete(item) {
	  // Handle delete logic here
	  console.log('Deleting', item);
  
	  const apiUrl = `https://api.recruitly.io/api/candidate/${item.id}?apiKey=TEST9349C0221517DA4942E39B5DF18C68CDA154`;
	  const response = await fetch(apiUrl, {
		method: 'DELETE',
		headers: {
		  'Content-Type': 'application/json'
		}
	  });
  
	  if (response.ok) {
		console.log('Item deleted successfully');
		// Remove the deleted item from the grid
		jsonData = jsonData.filter((candidate) => candidate.id !== item.id);
		grid.update({
		  data: jsonData.map((item) => [
			item.firstName,
			item.surname,
			item.email,
			item.mobile,
			createActionsCell(item),
		  ])
		});
	  } else {
		console.error('Failed to delete item');
	  }
	}
  </script>
  
  <div id="grid-container"></div>
  <style global>
	@import "https://cdn.jsdelivr.net/npm/gridjs/dist/theme/mermaid.min.css";
  </style>
  