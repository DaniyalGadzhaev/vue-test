<template>
  <div class="modal-backdrop" @click="close"></div>
  <div class="modal">
    <form @submit.prevent="saveUser" class="modal-form">
      <label class="modal-label">Имя</label>
      <input v-model="name" required class="modal-input" />

      <label class="modal-label">Телефон</label>
      <input v-model="phone" required class="modal-input" />

      <label class="modal-label">Начальник</label>
      <select v-model="parentId" class="modal-select">
        <option :value="null">Нет</option>
        <option v-for="user in users" :key="user.id" :value="user.id">
          {{ user.name }}
        </option>
      </select>

      <div class="modal-button-group">
        <button type="submit" class="modal-button modal-button-submit">
          Сохранить
        </button>
        <button
          type="button"
          @click="close"
          class="modal-button modal-button-cancel"
        >
          Отмена
        </button>
      </div>
    </form>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  props: {
    users: {
      type: Array,
      required: true,
    },
  },
  emits: ['close', 'save'],
  setup(props, { emit }) {
    const name = ref('');
    const phone = ref('');
    const parentId = ref(null);

    const saveUser = () => {
      const newUser = {
        id: Date.now(),
        name: name.value,
        phone: phone.value,
        parentId: parentId.value,
      };
      emit('save', newUser);
      close();
    };

    const close = () => {
      emit('close');
    };

    return {
      name,
      phone,
      parentId,
      saveUser,
      close,
    };
  },
};
</script>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  z-index: 500;
}

.modal {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background: white;
  padding: 20px 40px;
  border-radius: 8px;
  box-shadow: 0px 0px 15px rgba(0, 0, 0, 0.2);
  z-index: 1000;
}

.modal-form {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.modal-label {
  margin: 10px 0 5px;
  font-weight: bold;
}

.modal-input,
.modal-select {
  padding: 8px;
  margin-bottom: 15px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 100%;
}

.modal-button-group {
  display: flex;
  gap: 10px;
  justify-content: flex-end;
}

.modal-button {
  padding: 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.modal-button-submit {
  background-color: #4caf50;
  color: white;
}

.modal-button-submit:hover {
  background-color: #45a049;
}

.modal-button-cancel {
  background-color: #ccc;
  color: #333;
}

.modal-button-cancel:hover {
  background-color: #bbb;
}
</style>
