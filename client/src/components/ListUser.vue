<template>
  <div>ListUser</div>
</template>
<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";

const users = ref([]);
const getAllUsers = async () => {
  const response = await axios.get("http://localhost:8080/users");
  console.log(response.data);
};

onMounted(() => {
  getAllUsers();
});

const deleteUser = async (id) => {
  const response = await axios.delete(`http://localhost:8080/users/${id}`);
  console.log(response.data);
  getAllUsers();
};
// deleteUser(3);

const addUser = async () => {
  const newUser = {
    student_name: "Huyền",
    email: "huyen2005@gmail.com",
    phone: "0938824145",
    address: "Nghệ An",
    status: true,
    created_at: "2024-10-10",
  };
  const response = await axios.post("http://localhost:8080/users", newUser);
  console.log(response.data);
  getAllUsers();
};
// addUser();

const updateUser = async (id) => {
  const updateUser = {
    student_name: "Bảo",
  };
  const response = await axios.patch(
    `http://localhost:8080/users/${id}`,
    updateUser
  );
  console.log(response.data);
  getAllUsers();
};
updateUser(2);
</script>
<style lang=""></style>
