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
          { dataField: "id", caption: "ID", width: 500 },
          { dataField: "name", caption: "Name", width: 200 },
          { dataField: "email", caption: "Email", width: 200 },
          { dataField: "phone", caption: "Mobile", width: 150 },
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
            title: "Row in the editing state",
          },
          texts: {
            saveRowChanges: "Save",
            cancelRowChanges: "Cancel",
            deleteRow: "Delete",
            confirmDeleteMessage: "Are you sure you want to delete this record?",
          },
        },
        paging: {
          pageSize: 20,
        },
        onRowInserting: async (e) => {
          try {
            const response = await fetch(
              "https://api.recruitly.io/api/candidate/add",
              {
                method: "POST",
                headers: {
                  "Content-Type": "application/json",
                  apiKey: "TEST9349C0221517DA4942E39B5DF18C68CDA154",
                },
                body: JSON.stringify({
                  reference: e.data.reference,
                  fullName: e.data.name,
                  email: e.data.email,
                  mobile: e.data.phone,
                }),
              }
            );
  
            const responseData = await response.json();
            if (response.ok) {
              e.data.id = responseData.id;
              gridData.push(e.data);
              dataGrid.refresh();
            } else {
              console.error("Failed to add record:", responseData.error);
            }
          } catch (error) {
            console.error("Failed to add record:", error);
          }
        },
        onRowUpdating: async (e) => {
          try {
            const response = await fetch(
              `https://api.recruitly.io/api/candidate/update/${e.key}`,
              {
                method: "PUT",
                headers: {
                  "Content-Type": "application/json",
                  apiKey: "TEST9349C0221517DA4942E39B5DF18C68CDA154",
                },
                body: JSON.stringify({
                  reference: e.newData.reference,
                  fullName: e.newData.name,
                  email: e.newData.email,
                  mobile: e.newData.phone,
                }),
              }
            );
  
            const responseData = await response.json();
            if (response.ok) {
              const updatedItemIndex = gridData.findIndex((item) => item.id === e.key);
              gridData[updatedItemIndex] = e.newData;
              dataGrid.refresh();
            } else {
              console.error("Failed to update record:", responseData.error);
            }
          } catch (error) {
            console.error("Failed to update record:", error);
          }
        },
        onRowRemoving: async (e) => {
          try {
            const response = await fetch(
              `https://api.recruitly.io/api/candidate/delete/${e.key}`,
              {
                method: "DELETE",
                headers: {
                  "Content-Type": "application/json",
                  apiKey: "TEST9349C0221517DA4942E39B5DF18C68CDA154",
                },
              }
            );
  
            if (response.ok) {
              const removedItemIndex = gridData.findIndex((item) => item.id === e.key);
              gridData.splice(removedItemIndex, 1);
              dataGrid.refresh();
            } else {
              console.error("Failed to delete record.");
            }
          } catch (error) {
            console.error("Failed to delete record:", error);
          }
        },
        onInitialized: () => {
          // Function called when the grid is initialized
          // ...
        },
      });
  
      dataGrid.render();
    });
  </script>
  <div id="dataGrid"></div>
  <h1></h1>
