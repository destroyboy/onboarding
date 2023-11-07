<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Quicksand">
<link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@24,400,0,0" />


<style>

.top-nav {
    background-color: #262F3D;
    height: 100px;
}

.top-nav img {
    padding-top: 25px;
    padding-left: 25px;
    width: auto;
    height: 50%;
}

.content-table {
    font-family: 'Quicksand', sans-serif;
    margin: 25px 0px;
    font-size: 0.9em;
    border-radius: 5px 5px 0 0;
    border-spacing: 0px;
    border-collapse: collapse;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
    overflow: hidden;
}

.content-table thead tr {
    background-color: #009879;
    color: #ffffff;
    text-align: left;
    font-weight: bold;
}

.content-table th,
.content-table td {
    min-width: 150px;
    max-width: 150px;
    padding: 12px 15px;
}
.content-table td {
    word-wrap: break-word;
}

.content-table td textarea{
    height: 18px;
    width: 150px;
    resize: none;
}

.content-table th:nth-child(1),
.content-table td:nth-child(1) {
    min-width: 60px;
    max-width: 60px;
    padding: 12px 15px;
}

.content-table tbody tr {
    border-bottom: 1px solid #dddddd;
    overflow: hidden;
}
.content-table tbody tr:nth-of-type(even) {
    background-color: #f3f3f3;
    overflow: hidden;
}
.content-table tbody tr:last-of-type {
    border-bottom: 2px solid #009879;
    overflow: hidden;
}
.content-table tbody tr.active-row td:not(:first-child){
    font-weight: bold;
    color: #009879;
    overflow: hidden;
}

.material-symbols-outlined-smaller {
    font-family: 'Material Symbols Outlined';
    font-weight: normal;
    font-style: normal;
    font-size: 0.9em;
    line-height: 1;
    letter-spacing: normal;
    text-transform: none;
    display: inline-block;
    white-space: nowrap;
    word-wrap: normal;
    direction: ltr;
    -moz-font-feature-settings: 'liga';
    -moz-osx-font-smoothing: grayscale;
  }

.reset-style {
    all:revert;
}

.selected {
    color:  #009879;
}

.unselected {
    color:  lightgrey;
}


</style>
<div class="top-nav">
     <img src="/consid_logo.png" alt="consid logo">
</div>

<table class="content-table">
    <thead>
        <tr>
            <th>Actions</th>
            <th on:click={()=>ascendingFirstName=!ascendingFirstName}>First Name<span class="material-symbols-outlined-smaller">{ascendingFirstName?"arrow_downward":"arrow_upward"}</span></th>
            <th on:click={()=>ascendingSecondName=!ascendingSecondName}>Second Name<span class="material-symbols-outlined-smaller">{ascendingSecondName?"arrow_downward":"arrow_upward"}</span></th>
            <th on:click={()=>ascendingJobTitle=!ascendingJobTitle}>Job Title<span class="material-symbols-outlined-smaller">{ascendingJobTitle?"arrow_downward":"arrow_upward"}</span></th>
            <th on:click={()=>ascendingOffice=!ascendingOffice}>Office<span class="material-symbols-outlined-smaller">{ascendingOffice?"arrow_downward":"arrow_upward"}</span></th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>
                <span class="material-symbols-outlined">
                    add
                </span>
            </td>
            <td><textarea on:keydown={preventEnter} placeholder="Filter..."/></td>
            <td><textarea on:keydown={preventEnter} placeholder="Filter..."/></td>
            <td><textarea on:keydown={preventEnter} placeholder="Filter..."/></td>
            <td><textarea on:keydown={preventEnter} placeholder="Filter..."/></td>
        </tr>
        <tr>
            <div><button class="button">Save</button><button>Discard</button></div>
        </tr>
        {#each employees as employee (employee._id) }
        <tr class:active-row={employee._id === selected_id}>
            <td>
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <!-- svelte-ignore a11y-no-static-element-interactions -->
                <span on:click={()=>deleteRow(employee._id)} class:selected={employee._id === selected_id} class:unselected={employee._id !== selected_id} class="material-symbols-outlined">
                    delete
                </span>
                <!-- svelte-ignore a11y-no-static-element-interactions -->
                <!-- svelte-ignore a11y-click-events-have-key-events -->
                <span on:click={()=>clickEdit(employee._id)} class:selected={employee._id === selected_id} class="material-symbols-outlined">
                    {employee._id !== selected_id?"lock":"lock_open_right"}
                </span>
            </td>
            
            <td on:input={(e)=>fieldChanged(e, "firstName")} contenteditable={employee._id === selected_id}>
                {employee.firstName}
            </td>
            <td on:input={(e)=>fieldChanged(e, "secondName")} contenteditable={employee._id === selected_id}>
                {employee.secondName}
            </td>
            <td on:input={(e)=>fieldChanged(e, "jobTitle")} contenteditable={employee._id === selected_id}>
                {employee.jobTitle}
            </td>
            <td on:input={(e)=>fieldChanged(e, "office")} contenteditable={employee._id === selected_id}>
                {employee.office}
            </td>
        </tr>
        {/each}
    </tbody>
</table>

<script lang="ts">
    import { onMount } from "svelte"
    import { data } from "../test"
    
    let employees: any[] = []
    let selected_id = ""

    let ascendingFirstName = true;
    let ascendingSecondName = true;
    let ascendingJobTitle = true;
    let ascendingOffice = true;

    let selectedEmployeeFields = new Map()

    onMount(async ()=>{
        employees = data.prototype.getEmployees()
        //await refreshEmployees()
    })

    function preventEnter(e:KeyboardEvent) {
        if (e.key=='Enter') {
            e.preventDefault()
        }
    }

    function fieldChanged(e: Event, field: string) {
        console.log(e)
        var child = ( <HTMLElement>e.target ).firstChild
        console.log(child?.textContent)
        selectedEmployeeFields.set(field, child?.textContent)
    }

    async function refreshEmployees() {

        const headers = {
            'cache-control': 'no-cache',
            'x-apikey': '65367a869c6e0650201d476b',
            'content-type': 'application/json'
            }
        const response = await fetch('https://onboarding-1b0a.restdb.io/rest/employee', { method: "GET", headers: headers })
        employees = await response.json()
        console.log(employees)
    }

    async function deleteRow(employeeId: string) {
        const headers = {
            'cache-control': 'no-cache',
            'x-apikey': '65367a869c6e0650201d476b',
            'content-type': 'application/json'
            }
        const response = await fetch('https://onboarding-1b0a.restdb.io/rest/employee/' + employeeId, { method: "DELETE", headers: headers })
        await refreshEmployees()
    }

    function clickEdit(employeeId: string) {
        if (selected_id!==employeeId) {
            console.log(selectedEmployeeFields)
            selected_id = employeeId
            selectedEmployeeFields.clear()
        }
        else {
            console.log(selectedEmployeeFields)
            selectedEmployeeFields.clear()
        }        
    }

</script>
