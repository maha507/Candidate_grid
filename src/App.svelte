<script>
    import { onMount } from "svelte";
    import "bootstrap/dist/css/bootstrap.min.css";
    import DevExpress from "devextreme";
  
    let jsonData = [];
    let gridData = [];
  
    onMount(async () => {
      const response = await fetch(
        "https://api.recruitly.io/api/candidate?apiKey=TEST9349C0221517DA4942E39B5DF18C68CDA154"
      );
      const responseData = await response.json();
      jsonData = responseData.data;
  
      gridData = jsonData.map((item) => ({
        id: item.id,
        reference: item.reference,
        name: item.fullName,
        email: item.email,
        phone: item.mobile,
      }));
  
      const dataGrid = new DevExpress.ui.dxDataGrid(document.getElementById("dataGrid"), {
        dataSource: gridData,
        columns: [
          { dataField: "id", caption: "ID" },
          { dataField: "name", caption: "Name", dataType: "url" },
          { dataField: "email", caption: "Email" },
          { dataField: "phone", caption: "Mobile" },
          // Define other columns as needed
        ],
        showBorders: true,
        filterRow: {
          visible: true,
        },
        editing: {
          allowDeleting: true,
          allowAdding: true,
          allowUpdating: true,
          mode: "popup",
          form: {
            labelLocation: "top",
          },
          popup: {
            showTitle: true,
            title: "Candidate in editing stage",
            cssClass: "custom-popup", // Add custom CSS class to the popup
            showcssClass: true,
          },
        },
        paging: {
          pageSize: 20,
        },
      });
  
      dataGrid.render();
    });
  </script>
  
  <style>
    /* Custom CSS for popups */
    .custom-popup .dx-popup-title {
      background-color: #f0f0f0;
      color: #333;
      font-weight: bold;
      padding: 10px;
    }
  
    .custom-popup .dx-popup-content {
      display: flex;
      flex-direction: column;
      padding: 10px;
    }
  
    .custom-popup .dx-form .dx-item {
      margin-bottom: 10px;
    }
  </style>
  
  <div id="dataGrid"></div>
  