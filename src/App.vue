<script setup lang="ts">
import { ref, onMounted, onBeforeUnmount } from "vue";

import Form from "./components/todo/Form.vue";
import List from "./components/todo/List.vue";

import type { BeforeInstallPromptEvent } from "./types/BeforeInstallPromptEvent";

// Установка PWA
const _install_ready = ref(false);
const _install_prompt = ref<BeforeInstallPromptEvent | null>(null);
const _app_installed = ref(false);

// Определяем возможность установки PWA
onMounted(() => {
  window.addEventListener("beforeinstallprompt", savePrompt as EventListener);
});

function savePrompt(event: BeforeInstallPromptEvent) {
  event.preventDefault(); // Предотвращает мобильную подсказку
  // Сохраняем ссылку на событие, чтобы активировать его позже
  _install_prompt.value = event;
  // Уведомить пользовательский интерфейс о том, что приложение может быть установлено
  _install_ready.value = true;
}

function installPWA() {
  // Запуск приглашения к установке
  if (_install_prompt.value) {
    _install_prompt.value.prompt().then(() => {
      _app_installed.value = true;
    });
  }
}

onBeforeUnmount(() => {
  window.removeEventListener("beforeinstallprompt", savePrompt);
});
</script>

<template>
  <div class="todo-container">
    <header>
      <h1>Todo App</h1>
    </header>

    <Form />

    <hr />
    <h2>Todo list</h2>

    <List />
  </div>

  <p v-show="_install_ready && !_app_installed">
    Install this app
    <button @click="installPWA()">Install</button>
  </p>
</template>
