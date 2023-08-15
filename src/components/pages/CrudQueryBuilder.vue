<script setup>
import { ref, watch } from 'vue'
const tableNames = ['Spiders', 'Owners']
const actions = ['Create', 'Update', 'Remove']

const tableFieldsSpiders = [{ name: 'Name', type: 'text' }, { name: 'Species', type: 'text' }]
const tableFieldsOwners = [{ name: 'Nick', type: 'text' }]
const tableFields = ref(tableFieldsSpiders)
const selectedTable = ref(tableNames[0])
const selectedAction = ref(actions[0])
watch(selectedTable, (selectedTableNew) => {
    tableFields.value = selectedTableNew === tableNames[0] ? tableFieldsSpiders : tableFieldsOwners
})
const generatedQuery = ref('')

function updateSelectedTableByInputValues(e) {
    tableFields.value.map((field) => {
        if (field.name === e.target.id) {
            field.inputValue = e.target.value
        }
    });
}


function returnColumnsFromSelectedTable(action) {
    const columns = []
    const values = []
    const formattedData = []

    for (const column of tableFields.value) {
        if (action === actions[0]) {
            columns.push(column.name)
            values.push(column.inputValue)
        }

        if (action === actions[1]) {
            formattedData.push(`${column.name} = ${column.inputValue}`)
        }

        if (action === actions[2]) {
            if (column.name == 'Name' || column.name == 'Nick') {
                formattedData.push(`${column.name} = ${column.inputValue}`)
            }
        }
    }
    if (action === actions[0]) {
        return { columns: columns.join(), values: values.join() }
    }

    if (action === actions[1] || action === actions[2]) {
        return formattedData.join()
    }
}


function generateQuery(selectedAction) {
    switch (selectedAction) {
        case actions[0]:
            generatedQuery.value = `INSERT INTO ${selectedTable.value} (${returnColumnsFromSelectedTable(actions[0]).columns})
             VALUES(${returnColumnsFromSelectedTable(actions[0]).values});`
            break;
        case actions[1]:
            generatedQuery.value = `UPDATE ${selectedTable.value}
    SET ${returnColumnsFromSelectedTable(actions[1])}
;`
            break;
        case actions[2]:
            generatedQuery.value = `DELETE FROM ${selectedTable.value} WHERE ${returnColumnsFromSelectedTable(actions[2])};`
            break;
        default:
            generatedQuery.value = 'No query to return'
    }
}

</script>
<template>
    <br>
    <br>
    <br>
    <h3> 1. Select Table </h3>
    <select v-model="selectedTable" class="form-select">
        <option v-for="tableName in tableNames" :value="tableName">{{ tableName }}</option>
    </select>
    <div>Selected Table: {{ selectedTable }}</div>
    <br>
    <h3> 2. Select Action </h3>
    <select v-model="selectedAction" class="form-select">
        <option v-for="action in actions" :value="action">{{ action }}</option>
    </select>
    <div>Selected Action: {{ selectedAction }}</div>
    <br>
    <h3>3. Fill form based on selected table</h3>
    <div v-for="(field, i) in tableFields" :key="field.name">
        <label :for="field.name">{{ field.name }} </label>:
        <input class="form-control" :type="field.type" :id="field.name" @input="updateSelectedTableByInputValues">
    </div>
    <br>
    <button class="button" @click="generateQuery(selectedAction)">Generate query</button>
    <br>
    <div v-if="generatedQuery">
        <p>Generated Query:</p>
        <p>{{ generatedQuery }}</p>
    </div>
</template>

<style>
.form-control {
    display: block;
    width: 100%;
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    line-height: 1.5;
    color: #495057;
    background-color: #fff;
    background-clip: padding-box;
    border: 1px solid #ced4da;
    border-radius: 0.25rem;
    transition: border-color .15s ease-in-out, box-shadow .15s ease-in-out;
}

.form-select {
    display: block;
    width: 100%;
    padding: 0.375rem 2.25rem 0.375rem 0.75rem;
    -moz-padding-start: calc(0.75rem - 3px);
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: #212529;
    background-color: #fff;
    background-repeat: no-repeat;
    background-position: right 0.75rem center;
    background-size: 16px 12px;
    border: 1px solid #ced4da;
    border-radius: 0.25rem;
    transition: border-color .15s ease-in-out, box-shadow .15s ease-in-out;
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
}

.button {
    color: #fff;
    background-color: #007bff;
    border-color: #007bff;
    display: inline-block;
    font-weight: 400;
    text-align: center;
    white-space: nowrap;
    vertical-align: middle;
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    border: 1px solid transparent;
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    line-height: 1.5;
    border-radius: 0.25rem;
    transition: color .15s ease-in-out, background-color .15s ease-in-out, border-color .15s ease-in-out, box-shadow .15s ease-in-out;
}
</style>