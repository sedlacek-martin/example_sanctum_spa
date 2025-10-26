<script setup>
import axios from 'axios';
import {ref} from "vue";

const apiClient = axios.create({
  baseURL: 'http://localhost',
  withCredentials: true,
  withXSRFToken: true
});

const loginEmail = ref('');
const loginPassword = ref('');
const loginError = ref(null);
const registerName = ref('');
const registerEmail = ref('');
const registerPassword = ref('');
const registerError = ref(null);

const getUser = async () => {
  apiClient.get('/api/user'
  ).then((response) => {
    console.log('User data:', response.data);
  }).catch((error) => {
    console.error('Error fetching user data:', error);
  });
};

const handleLogin = async (e) => {

  try {
    // Get CSRF token
    apiClient.get('/sanctum/csrf-cookie', {
      headers: {
        'X-Requested-With': 'XMLHttpRequest',
        'Accept': 'application/json',
        'Content-Type': 'application/json',
      },
    }).then(() => {
      apiClient.post('/login', {email: loginEmail.value, password: loginPassword.value}).then((response) => {
        console.log('Login successful!', response.data);
      }).catch((error) => {
        console.log(error);
        loginError.value = (error.response?.data?.message || 'Login failed!');
      });
    }).catch((error) => {
      console.log(error);
      loginError.value = (error.response?.data?.message || 'Login failed!');
    });
  } catch (error) {
    console.log(error);
    loginError.value = (error.response?.data?.message || 'Login failed!');
  }
};

const handleRegister = async () => {
  try {
    // Get CSRF token
    apiClient.get('/sanctum/csrf-cookie', {
      headers: {
        'X-Requested-With': 'XMLHttpRequest',
        'Accept': 'application/json',
        'Content-Type': 'application/json',
      },
    }).then(() => {
      console.log('CSRF token retrieved');
      apiClient.post('/register', {
        email: registerEmail.value,
        password: registerPassword.value,
        password_confirmation: registerPassword.value,
        name: registerName.value
      }).then((response) => {
        console.log('Login successful!', response.data);
      }).catch((error) => {
        console.log(error);
        registerError.value = (error.response?.data?.message || 'Register 1 failed!');
      });
    }).catch((error) => {
      console.log(error);
      registerError.value = (error.response?.data?.message || 'Register 2 failed!');
    });
  } catch (error) {
    console.log(error);
    registerError.value = (error.response?.data?.message || 'Register 3 failed!');
  }
};

const handleLogout = async () => {
  try {
    const response = await apiClient.post('/logout');
    console.log('Logout successful!', response.data);
  } catch (error) {
    console.error('Logout failed:', error);
  }
};

</script>

<template>
  <main>
    <div>
      <h2>Login</h2>
      <form @submit.prevent=handleLogin>
        <label>Username:</label>
        <input
            type="email"
            :value="loginEmail"
            @input="event => loginEmail = event.target.value"
        />
        <label>Password:</label>
        <input
            type="password"
            :value="loginPassword"
            @input="event => loginPassword = event.target.value"
        />
        <button type="submit">Login</button>
        <p v-if="loginError" class="error">{{ loginError }}</p>
      </form>

    </div>
    <div>
      <h2>Register</h2>
      <form @submit.prevent=handleRegister>
        <label>Name:</label>
        <input
            type="text"
            :value="registerName"
            @input="event => registerName = event.target.value"
        />
        <label>Username:</label>
        <input
            type="email"
            :value="registerEmail"
            @input="event => registerEmail = event.target.value"
        />
        <label>Password:</label>
        <input
            type="password"
            :value="registerPassword"
            @input="event => registerPassword = event.target.value"
        />

        <button type="submit">Register</button>
        <p v-if="registerError" class="error">{{ registerError }}</p>
      </form>

    </div>
    <div>
      <h2>Actions</h2>
      <button @click=getUser>Get User</button>
      <button @click=handleLogout>Logout</button>
    </div>
  </main>
</template>

<style scoped>
main {
  display: flex;
  flex-direction: row;
  gap: 2rem;
  align-items: flex-start;
  justify-content: center;
  flex-wrap: wrap;
  max-width: 1000px;
  margin: 2rem auto;
  padding: 1rem;
}

div {
  width: 100%;
  max-width: 480px;
  background: #ffffff;
  border: 1px solid #e5e7eb; /* gray-200 */
  border-radius: 8px;
  padding: 1.25rem;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.04);
}

form {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

label {
  font-weight: 600;
  font-size: 0.9rem;
  color: #374151; /* gray-700 */
}

input {
  padding: 0.6rem 0.75rem;
  border: 1px solid #d1d5db; /* gray-300 */
  border-radius: 6px;
  outline: none;
  transition: border-color 0.15s ease, box-shadow 0.15s ease;
}

input:focus {
  border-color: #3b82f6; /* blue-500 */
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.15);
}

button {
  align-self: flex-start;
  background: #3b82f6; /* blue-500 */
  color: #ffffff;
  border: none;
  padding: 0.6rem 1rem;
  border-radius: 6px;
  cursor: pointer;
  transition: background-color 0.15s ease, transform 0.05s ease;
}

button:hover {
  background: #2563eb; /* blue-600 */
}

button:active {
  transform: translateY(1px);
}

/* Space multiple action buttons beneath forms */
div > button {
  margin-right: 0.5rem;
  margin-top: 0.75rem;
}

.error {
  color: #b91c1c; /* red-700 */
  background: #fee2e2; /* red-100 */
  border: 1px solid #fecaca; /* red-200 */
  padding: 0.5rem 0.75rem;
  border-radius: 6px;
}

@media (max-width: 640px) {
  main {
    gap: 1rem;
    margin: 1rem auto;
    padding: 0.5rem;
  }

  div {
    max-width: 100%;
  }
}

h2 {
  color: #181818;
  padding-bottom: 1rem;
}
</style>
