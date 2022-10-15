<template>
    <div>
        <button @click="deselectRows">deselect rows</button>
        <ag-grid-vue
            class="ag-theme-alpine"
            style="height: 500px"
            :columnDefs="columnDefs.value"
            :rowData="rowData.value"
            :defaultColDef="defaultColDef"
            rowSelection="multiple"
            animateRows="true"
            @cell-clicked="cellWasClicked"
            @grid-ready="onGridReady"
        >
        </ag-grid-vue>
    </div>
</template>

<script>
import { AgGridVue } from "ag-grid-vue3";  // the AG Grid Vue Component
import { reactive, onMounted, ref } from "vue";

import "ag-grid-community/styles/ag-grid.css"; // Core grid CSS, always needed
// import "ag-grid-community/styles/ag-theme-alpine.css"; // Optional theme CSS

export default {
    components: {
		'AgGridVue': AgGridVue,
	},
	data() {
		return {
        }
    },
    setup() {
		const gridApi = ref(null); // Optional - for accessing Grid's API

		// Obtain API from grid's onGridReady event
		const onGridReady = (params) => {
			gridApi.value = params.api;
		};

		const rowData = reactive({}); // Set rowData to Array of Objects, one Object per Row

		// Each Column Definition results in one Column.
		const columnDefs = reactive({
			value: [
				{ field: "code" },
				{ field: "name" },
				{ field: "image" },
				{ field: "price" },
				{ field: "category" },
				{ field: "rating" },
				{ field: "inventoryStatus" }
			],
		});

		// DefaultColDef sets props common to all Columns
		const defaultColDef = {
			editable: true,
			sortable: true,
			filter: true,
			flex: 1
		};

		// Example load data from sever
		onMounted(() => {
			fetch("data/products.json")
			.then(res => res.json())
			.then(function(remoteRowData){
                rowData.value = remoteRowData.data;
				console.log('rowData : ');
				console.log(rowData);
				console.log('remoteRowData : ');
				console.log(remoteRowData);
            })
			.then(function(){
				console.log('rowData : ');
				console.log(rowData);
			});
		});

		return {
			onGridReady,
			columnDefs,
			rowData,
			defaultColDef,
			cellWasClicked: (event) => { // Example of consuming Grid Event
				console.log("cell was clicked", event);
			},
			deselectRows: () =>{
				gridApi.value.deselectAll()
				console.log(gridApi.value);
			}
		};
	},

}
</script>

<style scoped lang="scss">
.ag-theme-alpine {
	padding-bottom: 1rem;
}
.ag-root-wrapper {
	border: 0;
}
</style>