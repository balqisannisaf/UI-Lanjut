<template>
  <h3>To-Do ⏳</h3>

  <form @submit.prevent="storeTodo(todo)">
    <input v-model="todo.text" type="text" name="text" />
    <button :disabled="!todo.text" type="submit">Add</button>
  </form>

  <div>
    <ul>
      <li v-for="pendingTodo in pendingTodos" :key="pendingTodo.id">
        <span v-if="currentlyEditingId === pendingTodo.id">
          <input 
            type="text" 
            v-model="editingText" 
            @keyup.enter="saveEdit(pendingTodo.id)"
          />
          <button @click="saveEdit(pendingTodo.id)">✅</button>
          <button @click="currentlyEditingId = null">❌</button>
        </span>

        <span v-else>
          <span>{{ pendingTodo.text }}</span>
          
          <button @click="enterEditMode(pendingTodo)">✍️</button>
          
          <button @click="updateTodo({ ...pendingTodo, isCompleted: true})">✔️</button>
          <button @click="destroyTodo(pendingTodo.id)">🗑️</button>
        </span>
      </li>
    </ul>
  </div>
</template>

<script>
import { useTodos } from '@/stores/todos';
import { mapActions, mapState } from 'pinia';

export default {
    computed: {
      // Mengambil daftar tugas yang belum selesai dari Pinia
    ...mapState(useTodos, [
      'pendingTodos',
    ])
  },

    data: () => ({
      // State untuk input form penambahan tugas baru
      todo: {
        text: null,
        isCompleted: false,
      },
      // ⭐ State BARU untuk fitur edit
      currentlyEditingId: null, // ID tugas yang sedang diedit
      editingText: '', // Teks yang sedang diketik dalam input edit
  }),

    methods: {
      // Pinia actions
    ...mapActions(useTodos, [
      'storeTodo',
      'updateTodo',
      'destroyTodo',
    ]),
    
      // ⭐ Method BARU untuk fitur edit
    enterEditMode(todo) {
      // 1. Set ID tugas ini sebagai yang sedang diedit
      this.currentlyEditingId = todo.id;
      // 2. Muat teks tugas yang sudah ada ke input edit
      this.editingText = todo.text;
    },

    saveEdit(todoId) {
      // Validasi
      if (!this.editingText.trim()) {
        alert("Tugas tidak boleh kosong!");
        return;
      }
      
      // 3. Panggil Pinia untuk mengupdate teks
      this.updateTodo({
        id: todoId,
        text: this.editingText, // Kirim teks baru
      });

      // 4. Keluar dari mode edit (reset state)
      this.currentlyEditingId = null;
      this.editingText = '';
    }
  }
}
</script>