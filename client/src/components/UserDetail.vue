<template>
  <div>UserDetail</div>
</template>
<script setup>
import axios from "axios";

const getUserById = async (id) => {
  const response = await axios.get(`http://localhost:8080/users/${id}`);
  console.log(response.data);
};
getUserById(1);
</script>
<style lang=""></style>
