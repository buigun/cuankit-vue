<script setup>
import { ref, inject } from "vue";
import { RouterLink, useRouter } from "vue-router";
import { useAuthStore } from "../../../stores/auth";
import { useUserStore } from "../../../stores/user";

const api = inject("$api");
const router = useRouter();

const authStore = useAuthStore();
const userStore = useUserStore();

const { setAuthData } = authStore;
const { fetchUser } = userStore;

const form = ref({
  email: "",
  password: "",
});

function handleSubmit() {
  const { email, password } = form.value;

  api.post("login", { email, password }, async (resp) => {
    const accessToken = resp.data.data.access_token;
    const tokenType = resp.data.data.token_type;

    if (accessToken && tokenType) {
      setAuthData(tokenType, accessToken);
      const isUserFetched = await fetchUser(tokenType, accessToken);
      if (isUserFetched) router.push({ name: "home" });
    }
  });
}
</script>

<template>
  <div class="w-full p-5 mx-auto sm:max-w-md">
    <h2 class="mb-20 text-5xl font-bold text-center">Welcome Back</h2>
    <form @submit.prevent="handleSubmit">
      <div class="mb-4">
        <label for="email" class="block mb-1">Email Address</label>
        <input
          id="email"
          v-model="form.email"
          type="email"
          name="email"
          placeholder="Type your email"
          class="block w-full py-3 mt-2 border border-gray-300 rounded-full shadow-sm px-7 focus:border-indigo-300 focus:outline-none focus:ring focus:ring-indigo-200 focus:ring-opacity-50 disabled:bg-gray-100"
        />
      </div>
      <div class="mb-4">
        <label for="password" class="block mb-1">Password</label>
        <input
          id="password"
          v-model="form.password"
          type="password"
          name="password"
          placeholder="Type your password"
          class="block w-full py-3 mt-2 border border-gray-300 rounded-full shadow-sm px-7 focus:border-indigo-300 focus:outline-none focus:ring focus:ring-indigo-200 focus:ring-opacity-50 disabled:bg-gray-100"
        />
      </div>
      <div class="mt-6">
        <button
          type="submit"
          class="inline-flex items-center justify-center w-full px-8 py-3 text-base font-medium text-white bg-indigo-600 border border-transparent rounded-full hover:bg-indigo-700 md:py-2 md:text-lg md:px-10 hover:shadow"
        >
          Sign In
        </button>

        <RouterLink
          to="/register"
          class="inline-flex items-center justify-center w-full px-8 py-3 mt-2 text-base font-medium text-black bg-gray-200 border border-transparent rounded-full hover:bg-gray-300 md:py-2 md:text-lg md:px-10 hover:shadow"
        >
          Create New Account
        </RouterLink>
      </div>
    </form>
  </div>
</template>
