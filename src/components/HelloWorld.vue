<script setup>
import { onMounted } from 'vue';
import { ref, computed } from 'vue'

defineProps({
  msg: String,
});

const googleForm = ref();

const googleFormAction = computed(() => {
  const uri = window.encodeURIComponent(
    window.location.origin + "?login=google",
  );

  return `https://dev-api.playside.gg/v1/auth/users/auth/google_oauth2`;
});
const count = ref(0)
const csrf = ref("")
onMounted(intialize);

async function intialize() {
  const val = await fetch('https://dev-api.playside.gg/v1/csrf');
  const resp = await val.json();
  console.log(resp.csrf);
  csrf.value = resp.csrf;
}

async function googleLogin() {
  console.log({
      "authenticity_token": csrf.value,
    });
    const body = {
      "authenticity_token": csrf.value,
    };
    console.log(body);
  const response = await fetch('https://dev-api.playside.gg/v1/auth/users/auth/google_oauth2', 
  { 
    method:"POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(body), 
  });
  // const response = await fetch('https://dev-api.playside.gg/v1/auth/users/auth/google_oauth2', 
  // { 
  //   method:"POST",
  //   headers: {
  //     "X-CSRF-Token": csrf.value,
  //   }
  // });
  console.log(await response.json());
  // googleForm.value.submit();
}
</script>

<template>
  <h1>{{ msg }}</h1>

  <div class="card">
    <button type="button" @click="googleLogin()">login</button>
    <p>
      Edit
      <code>components/HelloWorld.vue</code> to test HMR
    </p>
  </div>

  <p>
    Check out
    <a href="https://vuejs.org/guide/quick-start.html#local" target="_blank"
      >create-vue</a
    >, the official Vue + Vite starter
  </p>
  <p>
    Install
    <a href="https://github.com/vuejs/language-tools" target="_blank">Volar</a>
    in your IDE for a better DX
  </p>
  <form
    type="hidden"
    ref="googleForm" 
    method="POST" 
    name="authenticity_token"
    :value="csrf"
    :action="googleFormAction" 
  />
  <p class="read-the-docs">Click on the Vite and Vue logos to learn more</p>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
