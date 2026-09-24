<script setup lang="ts">
import { ref } from "vue";

interface Tarefa {
  id: number;
  text: string;
  done: boolean;
}

interface Lista {
  id: number;
  titulo: string;
  novaTarefa: string;
  esconderConcluidas: boolean;
  tarefas: Tarefa[];
}

let listaId = 0;
let tarefaId = 0;

const titulo = ref("");
const listas = ref<Lista[]>([]);

function adicionarLista() {
  if (!titulo.value.trim()) return;

  listas.value.push({
    id: listaId++,
    titulo: titulo.value,
    novaTarefa: "",
    esconderConcluidas: false,
    tarefas: []
  });

  titulo.value = "";
}

function adicionarTarefa(lista: Lista) {
  if (!lista.novaTarefa.trim()) return;

  lista.tarefas.push({
    id: tarefaId++,
    text: lista.novaTarefa,
    done: false
  });

  lista.novaTarefa = "";
}

function removerTarefa(lista: Lista, tarefa: Tarefa) {
  lista.tarefas = lista.tarefas.filter(
    (t) => t.id !== tarefa.id
  );
}
</script>

<template>
  <header>
    <img
      alt="Vue logo"
      class="logo"
      src="./assets/logo.svg"
      width="125"
      height="125"
    />
  </header>

  <main>
    <input
      v-model="titulo"
      placeholder="Título da lista"
      @keyup.enter="adicionarLista"
    />

    <button @click="adicionarLista">
      Nova Lista
    </button>

    <div
      v-for="lista in listas"
      :key="lista.id"
    >
      <h2>{{ lista.titulo }}</h2>

      <form @submit.prevent="adicionarTarefa(lista)">
        <input
          v-model="lista.novaTarefa"
          placeholder="Nova tarefa"
          required
        />

        <button type="submit">
          Adicionar
        </button>
      </form>

      <ul>
        <li
          v-for="todo in (
            lista.esconderConcluidas
              ? lista.tarefas.filter((t) => !t.done)
              : lista.tarefas
          )"
          :key="todo.id"
        >
          <input
            type="checkbox"
            v-model="todo.done"
          />

          <span :class="{ done: todo.done }">
            {{ todo.text }}
          </span>

          <button
            type="button"
            @click="removerTarefa(lista, todo)"
          >
            X
          </button>
        </li>
      </ul>

      <button
        type="button"
        @click="
          lista.esconderConcluidas =
            !lista.esconderConcluidas
        "
      >
        {{
          lista.esconderConcluidas
            ? "Mostrar todas"
            : "Ocultar concluídas"
        }}
      </button>

      <hr />
    </div>
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

.done {
  text-decoration: line-through;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }
}
</style>
