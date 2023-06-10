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
        fullname: item.fullname,
       
        email: item.email,
        phone: item.mobile,
      }));
  
      const dataGrid = new DevExpress.ui.dxDataGrid(document.getElementById("dataGrid"), {
        dataSource: gridData,
        columns: [
          { dataField: "fullname", caption: "full Name" },
          { dataField: "email", caption: "Email" },
          { dataField: "phone", caption: "Mobile" },
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
            title: "Row in the editing state",
          },
        },
        paging: {
          pageSize: 20,
        },
      });
  
      dataGrid.render();
    });
  </script>
  
  <div id="dataGrid"></div>
  