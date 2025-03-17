<template>
  <div class="container">
    <n-card style="width: 400px;">
      <n-tabs v-model:value="activeTab" type="line" animated>
        <n-tab-pane name="login" tab="Connexion">
          <n-form @submit.prevent="login">
            <n-form-item label="Email">
              <n-input v-model="loginForm.email" type="email" placeholder="Entrez votre email" required />
            </n-form-item>
            <n-form-item label="Mot de passe">
              <n-input v-model="loginForm.password" type="password" placeholder="Entrez votre mot de passe" required />
            </n-form-item>
            <n-button type="success" block html-type="submit">Se connecter</n-button>
          </n-form>
          <p class="redirect-text">
            Pas de compte ?
            <a @click="activeTab = 'register'" class="link">S'inscrire</a>
          </p>
        </n-tab-pane>

        <n-tab-pane name="register" tab="Inscription">
          <n-form @submit.prevent="register">
            <n-form-item label="Email">
              <n-input v-model="registerForm.email" type="email" placeholder="Entrez votre email" required />
            </n-form-item>
            <n-form-item label="Mot de passe">
              <n-input v-model="registerForm.password" type="password" placeholder="Entrez votre mot de passe" required />
            </n-form-item>
            <n-form-item label="Confirmer le mot de passe">
              <n-input v-model="registerForm.confirmPassword" type="password" placeholder="Confirmez votre mot de passe" required />
            </n-form-item>
            <n-button type="success" block html-type="submit">S'inscrire</n-button>
          </n-form>
          <p class="redirect-text">
            Déjà inscrit ?
            <a @click="activeTab = 'login'" class="link">Se connecter</a>
          </p>
        </n-tab-pane>
      </n-tabs>
    </n-card>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";
import axios from "axios";

const router = useRouter();
const activeTab = ref("login");

const loginForm = ref({ email: "", password: "" });
const registerForm = ref({ email: "", password: "", confirmPassword: "" });

const login = async () => {
  console.log("Tentative de connexion...");
  try {
    const response = await axios.post("https://pokemon-api-seyrinian-production.up.railway.app/auth/login", loginForm.value);
    console.log("Réponse API :", response.data);
    localStorage.setItem("token", response.data.token);
    localStorage.setItem("userId", response.data.user.id);
    router.push("/deckbuilder");
  } catch (error) {
    console.error("Erreur de connexion :", error);
  }
};

const register = async () => {
  console.log("Tentative d'inscription...");
  if (registerForm.value.password !== registerForm.value.confirmPassword) {
    console.error("Les mots de passe ne correspondent pas");
    return;
  }
  try {
    const response = await axios.post("https://pokemon-api-seyrinian-production.up.railway.app/auth/register", registerForm.value);
    console.log("Réponse API :", response.data);
    activeTab.value = "login"; // Redirige vers connexion après inscription
  } catch (error) {
    console.error("Erreur d'inscription :", error);
  }
};
</script>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
}
.redirect-text {
  text-align: center;
  margin-top: 10px;
}
.link {
  color: #27ae60;
  cursor: pointer;
  text-decoration: none;
}
</style>