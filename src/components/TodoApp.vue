<script setup lang="ts">
import { onMounted, ref } from "vue";
import axios from "axios";

const table = ref([]);
const record_id = ref("");
const name = ref("");
const complete = ref(false);
const visible_create = ref(false);
const visible_edit = ref(false);
const searchQuery = ref("");

const url = "https://calm-plum-jaguar-tutu.cyclic.app/todos/";

const getData = async () => {
  try {
    const response = await axios.get(url);
    table.value = response.data.data;
  } catch (error) {
    console.error(error);
  }
};

const postData = async () => {
  try {
    const response = await axios.post(url, {
      todoName: name.value,
      isComplete: complete.value,
    });
  } catch (error) {
    console.error(error);
  }
};

const putData = async () => {
  try {
    const response = await axios.put(url + record_id.value, {
      isComplete: complete.value,
    });
  } catch (error) {
    console.error(error);
  }
};

const deleteData = async (_id: string) => {
  try {
    const response = await axios.delete(url + _id);
  } catch (error) {
    console.error(error);
  }
};

const saveEdit = async () => {
  await putData();
  await getData();
  visible_edit.value = false;
};

const saveCreate = async () => {
  await postData();
  await getData();
  visible_create.value = false;
};

const openEdit = (_id: string, isComplete: boolean) => {
  visible_edit.value = true;
  record_id.value = _id;
  complete.value = isComplete;
};

const openCreate = () => {
  visible_create.value = true;
  complete.value = false;
  name.value = "";
};

const deleteTodo = async (_id: string) => {
  await deleteData(_id);
  await getData();
};

const sortUp = () => {
  table.value.sort((a, b) => a.todoName.localeCompare(b.todoName));
};

const sortDown = () => {
  table.value.sort((a, b) => b.todoName.localeCompare(a.todoName));
};

const isFinish = async () => {
  await getData();
  table.value = table.value.filter((res) => {
    return res.isComplete === true;
  });
};

const notFinish = async () => {
  await getData();
  table.value = table.value.filter((res) => {
    return res.isComplete === false;
  });
};

const search = async () => {
  await getData();
  table.value = table.value.filter((res) => {
    return res.todoName.includes(searchQuery.value);
  });
};

onMounted(getData);
</script>

<template>
  <div class="id">
    <h1>Todo APP</h1>
    <div class="menu-row">
      <input
        type="text"
        placeholder="Enter Todo Names..."
        v-model="searchQuery"
      />
      <button @click="search" style="margin-right:50px">Search</button>
      <button @click="openCreate">Create New Todo</button>
      <button @click="getData">Refresh</button>
    </div>
    <table class="dashboard">
      <thead>
        <tr>
          <th>
            Title
            <font-awesome-icon
              @click="sortUp"
              icon="fa-solid fa-arrow-up"
              style="padding-left: 5px; padding-right: 10px"
            />
            <font-awesome-icon
              @click="sortDown"
              icon="fa-solid fa-arrow-down"
            />
          </th>
          <th>
            Complete
            <font-awesome-icon
              @click="isFinish"
              icon="fa-solid fa-circle-check"
              style="padding-left: 5px; padding-right: 10px"
            />
            <font-awesome-icon
              @click="notFinish"
              icon="fa-solid fa-circle-xmark"
            />
          </th>
          <th>
            Actions
            <font-awesome-icon icon="fa-solid fa-circle-check" />
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="record in table" :key="record._id">
          <td>{{ record.todoName }}</td>
          <td>{{ record.isComplete }}</td>
          <td>
            <button @click="openEdit(record._id, record.isComplete)">
              edit
            </button>
            <button @click="deleteTodo(record._id)">Delete</button>
            <!-- <button @click="Delete">delete</button> -->
          </td>
        </tr>
      </tbody>
    </table>
    <div v-if="visible_edit" class="edit_page">
      <div>
        <div style="align-items: center">
          <font-awesome-icon
            icon="fa-solid fa-circle-check"
            style="height: 50px; width: 50px; margin-bottom: 10px"
          />
        </div>
        <label for="complete">Complete?</label>
        <input type="checkbox" name="complete" v-model="complete" />
      </div>
      <div>
        <button @click="saveEdit">SAVE</button>
        <button @click="visible_edit = false">Cancel</button>
      </div>
    </div>
  </div>

  <div v-if="visible_create" class="create_page">
    <div>
      <div style="align-items: center">
        <font-awesome-icon
          icon="fa-solid fa-circle-check"
          style="height: 50px; width: 50px; margin-bottom: 10px"
        />
      </div>
      <label for="name">Todo Name</label>
      <input name="name" v-model="name" />
      <label for="complete">Complete?</label>
      <input type="checkbox" name="complete" v-model="complete" />
    </div>
    <div>
      <button @click="saveCreate">Create</button>
      <button @click="visible_create = false">Cancel</button>
    </div>
  </div>
</template>

<style>
.id {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
  font-family: "Franklin Gothic Medium", "Arial Narrow", Arial, sans-serif;
}

.dashboard {
  width: 1200px;
  border-collapse: collapse;
}

.edit_page {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: beige;
  height: 300px;
  width: 300px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 20px;
}

.create_page {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: beige;
  height: 300px;
  width: 300px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  border-radius: 20px;
}

.edit_page div:nth-child(1) {
  display: flex;
  flex-direction: column;
}

.create_page div:nth-child(1) {
  display: flex;
  flex-direction: column;
}

.edit_page div:nth-child(1) input {
  height: 20px;
  margin-bottom: 10px;
}

th,
td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: center;
}

thead {
  background-color: #f4f4f4;
}

button {
  margin: 5px;
  padding: 5px 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
</style>