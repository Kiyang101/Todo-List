<script lang="ts" setup>
const checkLogin = async () => {
  const token = ref();
  if (typeof window !== "undefined" && typeof localStorage !== "undefined") {
    token.value = localStorage.getItem("token");
  } else {
    return false;
  }
  if (token.value !== null) {
    await navigateTo("/todolist");
  }
};

checkLogin();

useHead({
  title: "Todolist - Login",
});

const email = ref("test@mail.com");
const password = ref("1234");
const showLoadding = ref(false);
const config = useRuntimeConfig();
const BASE_URL = config.public.apiEndpoint;
const login = async () => {
  // console.log(email.value, password.value);
  showLoadding.value = true;
  try {
    const response = await $fetch(`${BASE_URL}user/login`, {
      method: "post",
      body: {
        email: email.value,
        password: password.value,
      },
      // credentials: "include",
    });
    localStorage.setItem("token", response.token);

    // console.log(response);
    const token = localStorage.getItem("token");
    showLoadding.value = false;
    if (token) {
      await navigateTo("/todolist");
    }
  } catch (err) {
    console.log(err);
  }
};

const logout = async () => {
  localStorage.removeItem("token");
  // console.log("logout success");
  await navigateTo("/login");
};

const getUsers = async () => {
  try {
    const authtoken = localStorage.getItem("token");
    const response = await $fetch(`${BASE_URL}user/get`, {
      method: "get",
      headers: {
        Authorization: `Bearer ${authtoken}`,
      },
    });
    console.log(response);
  } catch (err) {
    console.log(err);
  }
};

const registerPage = async () => {
  await navigateTo("/register");
};
</script>

<template>
  <div class="bg-[#303030] h-[100vh] py-[20vh]">
    <div class="flex justify-center z-[2]" v-if="showLoadding">
      <div class="bg-[rgb(38,38,38,.9)] p-[15vw] rounded-xl absolute">
        <h1 class="text-white text-[5vw]">Loadding ...</h1>
      </div>
    </div>
    <div class="flex justify-center">
      <div class="p-5">
        <h1 class="text-5xl text-white">To-Do List</h1>
      </div>
    </div>
    <div class="flex justify-center">
      <div class="p-5">
        <h1 class="text-3xl text-white">Login Page</h1>
      </div>
    </div>
    <div class="flex justify-center">
      <div class="p-2 text-2xl">
        <h1 class="text-white">email</h1>
        <input
          class="rounded-lg text-black pl-2"
          type="text"
          v-model="email"
          placeholder="your@mail.com"
        />
      </div>
    </div>
    <div class="flex justify-center">
      <div class="p-2 text-2xl">
        <h1 class="text-white">password</h1>
        <input
          class="rounded-lg text-black pl-2"
          type="password"
          v-model="password"
          placeholder=""
        />
      </div>
    </div>
    <div class="text-xl p-4 flex justify-center">
      <button
        @click="login"
        class="text-white py-2 px-5 border-black border-2 rounded-lg"
      >
        Login
      </button>
      <!-- <button @click="logout" class="text-white p-2">logout</button> -->
      <!-- <button @click="getUsers" class="text-white p-2">GetUsers</button> -->
    </div>
    <div class="text-xl flex justify-center">
      <span class="text-white">Don't have account -></span>
      <button
        @click="registerPage"
        class="text-white px-2 text-lg underline underline-offset-2 under"
      >
        Register
      </button>
    </div>
  </div>
</template>

<style>
/* body {
  background-color: #454545;
} */
</style>
