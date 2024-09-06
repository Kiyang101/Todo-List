<script lang="ts" setup>
const checkLogin = async () => {
  const token = localStorage.getItem("token");
  if (!token) {
    await navigateTo("/login");
  }
};
checkLogin();

useHead({
  title: "Todolist - home",
});

const config = useRuntimeConfig();
const BASE_URL = config.public.apiEndpoint;
const user = ref();
const todos = ref();

type todos = {
  title: String;
  description: String;
  status: String;
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
    // console.log(response);
    user.value = response;
  } catch (err) {
    console.log(err);
  }
};

const loadTodo = async () => {
  try {
    const response = await $fetch(`${BASE_URL}todo/get`, {
      method: "get",
      headers: { authorization: `Bearer ${localStorage.getItem("token")}` },
    });
    todos.value = response;
  } catch (err) {
    console.log(err);
  }
  // console.log({ response: response });
  // console.log({ todo: todos.value });
};

const show = ref(false);
const title = ref("");
const Description = ref("");
const status = ref("pending");

const ShowWindowAddTodo = () => {
  show.value = true;
};
const CloseWindowAddTodo = () => {
  title.value = "";
  Description.value = "";
  show.value = false;
};
const submit = async () => {
  if (!title.value || !Description.value) {
    return console.log("Data isn't complete");
  }
  const authtoken = localStorage.getItem("token");
  await $fetch(`${BASE_URL}todo/add`, {
    method: "post",
    headers: {
      Authorization: `Bearer ${authtoken}`,
    },
    body: {
      email: user.value.email,
      title: title.value,
      description: Description.value,
      status: status.value,
    },
  });
  CloseWindowAddTodo();
  loadTodo();
};
const edit = ref(true);
const toggleEdit = () => {
  edit.value = !edit.value;
};
const full = ref(false);
const fullTitle = ref("");
const fullDescription = ref("");
const fullStatus = ref("");
const fullId = ref("");
const fullData = ref();
const showFull = (data: any) => {
  fullData.value = data;
  fullId.value = data.id;
  fullTitle.value = data.title;
  fullDescription.value = data.description;
  fullStatus.value = data.status;
  full.value = true;
};
const closeFull = () => {
  full.value = false;
  edit.value = true;
  fullId.value = "";
  fullTitle.value = "";
  fullDescription.value = "";
  fullStatus.value = "";
};

const editSubmit = async () => {
  if (!fullTitle.value || !fullDescription.value) {
    return console.log("Data isn't complete");
  }
  const authtoken = localStorage.getItem("token");
  let change = ref(0);
  if (fullTitle.value !== fullData.value.title) {
    await $fetch(`${BASE_URL}todo/update/${fullId.value}`, {
      method: "put",
      headers: {
        Authorization: `Bearer ${authtoken}`,
      },
      body: {
        title: fullTitle.value,
        email: user.value.email,
      },
    });
  } else {
    change.value += 1;
  }
  if (fullDescription.value !== fullData.value.description) {
    await $fetch(`${BASE_URL}todo/update/${fullId.value}`, {
      method: "put",
      headers: {
        Authorization: `Bearer ${authtoken}`,
      },
      body: {
        description: fullDescription.value,
        email: user.value.email,
      },
    });
  } else {
    change.value += 1;
  }
  if (fullStatus.value !== fullData.value.status) {
    await $fetch(`${BASE_URL}todo/update/${fullId.value}`, {
      method: "put",
      headers: {
        Authorization: `Bearer ${authtoken}`,
      },
      body: {
        status: fullStatus.value,
        email: user.value.email,
      },
    });
  } else {
    change.value += 1;
  }
  if (change.value <= 0) {
    return console.log("No change anything");
  }

  closeFull();
  loadTodo();
};
const windowDelete = ref(false);
const showWindowDelete = () => {
  windowDelete.value = true;
};
const deleteTodo = async (send: boolean) => {
  if (send) {
    await $fetch(`${BASE_URL}todo/delete/${fullId.value}`, {
      method: "delete",
      headers: {
        Authorization: `Bearer ${localStorage.getItem("token")}`,
      },
      body: {
        email: user.value.email,
      },
    });
    windowDelete.value = false;
    closeFull();
  } else {
    windowDelete.value = false;
  }
  loadTodo();
};

const logOut = async () => {
  localStorage.removeItem("token");
  console.log("logout success");
  await navigateTo("/login");
};

getUsers();
loadTodo();
</script>

<template>
  <div>
    <div class="px-[10vw] p-2">
      <div class="absolute right-0 px-5">
        <button
          class="text-red-500 text-xl rounded-lg border-red-500 border-2 p-2"
          @click="logOut"
        >
          LogOut
        </button>
      </div>
      <button
        @click="ShowWindowAddTodo"
        class="w-full flex justify-center border-2 border-[#454545] py-2 rounded-xl cursor-pointer mb-5"
      >
        Add TodoList
      </button>
      <!-- Popup Add Todo -->

      <div class="flex justify-center" :class="!show ? 'hidden' : 'block'">
        <div
          class="z-[1] fixed top-0 h-[100vh] w-[100vw] bg-[rgba(0,0,0,.7)] m-0 p-0 left-0 right-0"
        ></div>
        <div
          class="z-[2] w-[60vw] h-[70vh] absolute bg-slate-300 pt-16 rounded-xl"
        >
          <div class="right-0 absolute p-3 top-0 text-red-500 cursor-pointer">
            <button @click="CloseWindowAddTodo">close</button>
          </div>
          <div class="flex justify-center">
            <div class="mb-5">
              <h1 class="text-3xl">Title</h1>
              <input
                type="text"
                v-model="title"
                class="focus:border-blue-400 focus:ring-blue-400 border rounded-lg p-1 bg-gray-100 border-gray-300 text-gray-900 w-[40vw]"
              />
            </div>
          </div>
          <div class="flex justify-center">
            <div class="mb-5">
              <h1 class="text-xl">Description</h1>
              <textarea
                v-model="Description"
                class="border rounded-lg p-1 bg-gray-100 border-gray-300 text-gray-900 w-[40vw] h-[20vh] resize-none"
              ></textarea>
            </div>
          </div>
          <div class="flex justify-center">
            <div class="mb-5">
              <h1 class="text-xl">Status</h1>
              <select class="w-[40vw] h-[4vh] text-xl" v-model="status">
                <option value="pending">pending</option>
                <option value="in_progress">in_progress</option>
                <option value="complete">complete</option>
              </select>
            </div>
          </div>

          <div class="flex justify-center">
            <button
              @click="submit"
              class="text-2xl text-white rounded-lg bg-slate-500 px-7 py-1"
            >
              submit
            </button>
          </div>
        </div>
      </div>

      <!-- Full display todo -->
      <div class="flex justify-center" :class="!full ? 'hidden' : 'block'">
        <div
          class="z-[1] fixed top-0 h-[100vh] w-[100vw] bg-[rgba(0,0,0,.7)] m-0 p-0 left-0 right-0"
        ></div>
        <div
          class="z-[2] w-[60vw] h-[70vh] absolute bg-slate-300 pt-16 rounded-xl"
        >
          <div
            class="right-0 absolute p-4 top-0 text-red-500 cursor-pointer text-xl"
          >
            <button @click="closeFull">close</button>
          </div>
          <div class="flex justify-center">
            <div class="mb-5">
              <h1 class="text-3xl">Title</h1>
              <input
                type="text"
                :disabled="edit"
                v-model="fullTitle"
                class="focus:border-blue-400 focus:ring-blue-400 border rounded-lg p-1 bg-gray-100 border-gray-300 text-gray-900 w-[40vw]"
              />
            </div>
          </div>

          <div class="flex justify-center">
            <div class="mb-5">
              <h1 class="text-xl">Description</h1>
              <textarea
                :disabled="edit"
                v-model="fullDescription"
                class="border rounded-lg p-1 bg-gray-100 border-gray-300 text-gray-900 w-[40vw] h-[20vh] resize-none"
              ></textarea>
            </div>
          </div>
          <div class="flex justify-center">
            <div class="mb-5">
              <h1 class="text-xl">Status</h1>
              <select
                class="w-[40vw] h-[4vh] text-xl"
                v-model="fullStatus"
                :disabled="edit"
              >
                <option value="pending">pending</option>
                <option value="in_progress">in_progress</option>
                <option value="complete">complete</option>
              </select>
            </div>
          </div>

          <div class="flex justify-center">
            <button
              class="text-2xl text-white rounded-lg bg-slate-500 px-7 py-1"
              @click="toggleEdit"
            >
              edit
            </button>
            <button
              class="text-2xl text-white rounded-lg bg-slate-500 px-7 py-1 ml-5"
              :class="edit ? 'hidden' : 'block'"
              @click="editSubmit"
            >
              submit
            </button>
          </div>
          <div class="absolute right-0 bottom-0 p-5 text-red-500 text-xl">
            <button @click="showWindowDelete">Delete</button>
          </div>
          <div
            class="flex justify-center items-center absolute top-0 w-full h-full"
            :class="!windowDelete ? 'hidden' : 'block'"
          >
            <div
              class="absolute bg-slate-800 w-[30vw] h-[25vh] text-white rounded-lg p-5"
            >
              <h1 class="text-center text-3xl mt-[10%]">
                Delete "{{ fullTitle }}"
              </h1>
              <div class="flex justify-around mt-[10%] text-2xl">
                <button
                  class="bg-red-400 p-2 rounded-lg"
                  @click="deleteTodo(false)"
                >
                  cancel
                </button>
                <button
                  class="bg-green-400 p-2 rounded-lg"
                  @click="deleteTodo(true)"
                >
                  confirm
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Display List of Todo -->
      <div
        v-for="todo in todos"
        :key="todo.id"
        class="border-[3px] rounded-xl p-2 mb-3 cursor-pointer"
        :class="{
          'border-green-500': todo.status === 'complete',
          'border-red-500': todo.status === 'pending',
          'border-yellow-500': todo.status === 'in_progress',
        }"
        @click="showFull(todo)"
      >
        <div>
          <h1 class="text-3xl">{{ todo.title }}</h1>
        </div>
        <p>
          {{ todo.description }}
        </p>
      </div>
      <div v-if="!todos || todos.length === 0" class="flex justify-center p-7">
        <p class="text-2xl">
          You don't have a To-Do list.<br />Let's add your to-do list.
        </p>
      </div>
    </div>
  </div>
</template>

<style></style>
