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
  title: "Todolist - Register",
});

const email = ref("");
const password = ref("");
const showLoadding = ref(false);
const config = useRuntimeConfig();
const BASE_URL = config.public.apiEndpoint;
const login = async () => {
  showLoadding.value = true;
  try {
    const response = await $fetch(`${BASE_URL}user/login`, {
      method: "post",
      body: {
        email: email.value,
        password: password.value,
      },
    });
    localStorage.setItem("token", response.token);

    const token = localStorage.getItem("token");
    showLoadding.value = false;
    if (token) {
      await navigateTo("/todolist");
    }
  } catch (err) {
    console.log(err);
  }
};

const register = async () => {
  try {
    const response = await $fetch(`${BASE_URL}user/register`, {
      method: "post",
      body: {
        email: email.value,
        password: password.value,
      },
    });
    // console.log(response);
  } catch (err) {
    console.log(err);
  }
  login();
};

const loginPage = async () => {
  await navigateTo("/login");
};
</script>

<template>
  <div class="bg-[#454545] h-[100vh] py-[20vh]">
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
        <h1 class="text-3xl text-white">Register Page</h1>
      </div>
    </div>
    <div class="flex justify-center">
      <div class="p-2 text-2xl">
        <h1 class="text-white">email</h1>
        <input
          class="rounded-lg text-black pl-2"
          type="text"
          v-model="email"
          placeholder=""
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
    <div class="text-xl p-2 flex justify-center">
      <button
        @click="register"
        class="text-white py-2 px-5 border-black border-2 rounded-lg"
      >
        Register
      </button>
    </div>
    <div class="text-xl flex justify-center">
      <button
        @click="loginPage"
        class="text-white px-2 text-lg underline underline-offset-2 under"
      >
        back to Login
      </button>
    </div>
  </div>
</template>

<style></style>
