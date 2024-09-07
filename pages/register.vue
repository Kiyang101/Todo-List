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
  title: "Todolist - Register",
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

const success = ref(false);
const successMessage = ref("");
const successWindow = async (message: string) => {
  successMessage.value = message;
};
const register = async () => {
  if (email.value === "" || password.value === "") {
    await errorWindow("Please fill in your email and password");
    return;
  } else {
    showLoadding.value = true;
  }
  try {
    const response = await $fetch(`${BASE_URL}user/register`, {
      method: "post",
      body: {
        email: email.value,
        password: password.value,
      },
    });
    // console.log(response);
    success.value = true;
  } catch (err: any) {
    showLoadding.value = false;
    console.log(err);
    await errorWindow(err.data.message);
  } finally {
    if (success.value) {
      showLoadding.value = false;
      successWindow(`Register "${email.value}" success`);
    }
  }
};

const finishRegister = async () => {
  success.value = false;
  successMessage.value = "";
  await navigateTo("/login");
};

const loginPage = async () => {
  await navigateTo("/login");
};
</script>

<template>
  <div class="bg-[#454545] h-[100vh] py-[20vh]">
    <div class="flex justify-center z-[2]" v-if="showLoadding">
      <div class="bg-[rgb(38,38,38,.9)] p-[15vw] rounded-xl absolute">
        <h1 class="text-white text-[5vw]">Registering ...</h1>
      </div>
    </div>
    <div class="flex justify-center z-[2]" v-if="showErrorWindow">
      <div class="bg-[rgb(38,38,38,.9)] p-[15vh] rounded-xl absolute">
        <h1 class="text-white text-[4vw]">{{ messageError }}</h1>
        <div class="flex justify-center mt-5">
          <button
            class="text-white text-[3vw] border-2 border-white rounded-lg py-1 px-5"
            @click="showErrorWindow = false"
          >
            ok
          </button>
        </div>
      </div>
    </div>
    <div class="flex justify-center z-[2]" v-if="success">
      <div
        class="bg-[rgb(38,38,38,.9)] p-[15vh] rounded-xl absolute text-center"
      >
        <h1 class="text-white text-[4vw]">{{ successMessage }}</h1>
        <h1 class="text-white text-[3vw]">back to login page</h1>

        <div class="flex justify-center mt-5">
          <button
            class="text-white text-[3vw] border-2 border-white rounded-lg py-1 px-5"
            @click="finishRegister"
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
        class="text-white py-2 px-5 border-white border-2 rounded-lg"
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
