<script lang="ts" setup>
const checkLogin = async () => {
  if (typeof window !== "undefined" && typeof localStorage !== "undefined") {
    const token = localStorage.getItem("token");
    if (token !== null && token !== "") {
      await navigateTo("/todolist");
    }
  } else {
    return false;
  }
};

checkLogin();

useHead({
  title: "Todolist - Login",
});

const email = ref("");
const password = ref("");
const showLoadding = ref(false);
const config = useRuntimeConfig();
const BASE_URL = config.public.apiEndpoint;
const showErrorWindow = ref(false);
const messageError = ref("");
const errorWindow = async (message: string) => {
  showErrorWindow.value = true;
  messageError.value = message;
};

const login = async () => {
  // console.log(email.value, password.value);
  if (email.value === "" || password.value === "") {
    await errorWindow("Please fill in your email and password");
    return;
  } else {
    showLoadding.value = true;
  }
  try {
    const response = await $fetch(`${BASE_URL}user/login`, {
      method: "post",
      body: {
        email: email.value,
        password: password.value,
      },
    });
    if (response?.token) {
      localStorage.setItem("token", response.token);
      const token = localStorage.getItem("token");
      if (token) {
        await navigateTo("/todolist");
      }
    }
    // console.log(response.message);
  } catch (err: any) {
    // console.error(err.response);
    // console.error(err.data.message);
    showLoadding.value = false;
    errorWindow(err.data.message);
  } finally {
    showLoadding.value = false;
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
      <div class="bg-[rgb(38,38,38,.9)] p-[15vh] rounded-xl absolute">
        <h1 class="text-white text-[5vw]">Loadding ...</h1>
      </div>
    </div>
    <div class="flex justify-center z-[2]" v-if="showErrorWindow">
      <div class="bg-[rgb(38,38,38,.9)] p-[15vh] rounded-xl absolute">
        <h1 class="text-white text-[4vw]">{{ messageError }}</h1>
        <div class="flex justify-center mt-5">
          <button
            class="text-white text-[3vw] border-2 border-white rounded-lg py-1 px-5"
            @click="(showErrorWindow = false), (messageError = '')"
          >
            ok
          </button>
        </div>
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
        class="text-white py-2 px-5 border-white border-2 rounded-lg"
      >
        Login
      </button>
    </div>
    <div class="text-xl flex justify-center">
      <span class="text-white">Don't have an account -></span>
      <button
        @click="registerPage"
        class="text-white px-2 text-lg underline underline-offset-2 under"
      >
        Register
      </button>
    </div>
  </div>
</template>

<style></style>
