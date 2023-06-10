<script>
    import { onMount } from "svelte";
    import "bootstrap/dist/css/bootstrap.min.css";
    import { Datagrid } from "@activewidgets/svelte";
  
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
    });
  
    async function addRecord(e) {
      try {
        const response = await fetch("https://api.recruitly.io/api/candidate/add", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
            apiKey: "TEST9349C0221517DA4942E39B5DF18C68CDA154",
          },
          body: JSON.stringify({
            reference: e.detail.record.reference,
            fullName: e.detail.record.name,
            email: e.detail.record.email,
            mobile: e.detail.record.phone,
          }),
        });
  
        const responseData = await response.json();
        if (response.ok) {
          e.detail.record.id = responseData.id;
          gridData.push(e.detail.record);
        } else {
          console.error("Failed to add record:", responseData.error);
        }
      } catch (error) {
        console.error("Failed to add record:", error);
      }
    }
  
    async function updateRecord(e) {
      try {
        const response = await fetch(
          `https://api.recruitly.io/api/candidate/update/${e.detail.record.id}`,
          {
            method: "PUT",
            headers: {
              "Content-Type": "application/json",
              apiKey: "TEST9349C0221517DA4942E39B5DF18C68CDA154",
            },
            body: JSON.stringify({
              reference: e.detail.record.reference,
              fullName: e.detail.record.name,
              email: e.detail.record.email,
              mobile: e.detail.record.phone,
            }),
          }
        );
  
        const responseData = await response.json();
        if (response.ok) {
          const updatedItemIndex = gridData.findIndex((item) => item.id === e.detail.record.id);
          gridData[updatedItemIndex] = e.detail.record;
        } else {
          console.error("Failed to update record:", responseData.error);
        }
      } catch (error) {
        console.error("Failed to update record:", error);
      }
    }
  
    async function deleteRecord(e) {
      try {
        const response = await fetch(
          `https://api.recruitly.io/api/candidate/delete/${e.detail.record.id}`,
          {
            method: "DELETE",
            headers: {
              "Content-Type": "application/json",
              apiKey: "TEST9349C0221517DA4942E39B5DF18C68CDA154",
            },
          }
        );
  
        if (response.ok) {
          const removedItemIndex = gridData.findIndex((item) => item.id === e.detail.record.id);
          gridData.splice(removedItemIndex, 1);
        } else {
          console.error("Failed to delete record.");
        }
      } catch (error) {
        console.error("Failed to delete record:", error);
      }
    }
  </script>
  
  <style>
    .aw-svelte-theme {
      --aw-grid-row-height: 40px;
      --aw-grid-header-row-height: 40px;
      --aw-grid-footer-row-height: 40px;
      --aw-input-padding: 5px;
    }
  </style>
  
  <Datagrid
    class="aw-svelte-theme"
    data={gridData}
    columns={[
      { field: "id", caption: "ID", width: 50 },
      { field: "name", caption: "Name", width: 200 },
      { field: "email", caption: "Email", width: 200 },
      { field: "phone", caption: "Mobile", width: 150 },
    ]}
    editable
    on:recordinsert={addRecord}
    on:recordupdate={updateRecord}
    on:recorddelete={deleteRecord}
  />
  