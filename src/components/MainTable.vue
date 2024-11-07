<template>
  <div class="table-container">
    <button @click="openModal" class="add-button">Добавить</button>
    <table class="user-table">
      <thead>
        <tr>
          <th @click="sortTable('name')">Имя</th>
          <th @click="sortTable('phone')">Телефон</th>
          <th>Действие</th>
        </tr>
      </thead>
      <tbody>
        <template v-for="user in sortedUsers" :key="user.id">
          <tr class="user-row">
            <td>{{ user.name }}</td>
            <td>{{ user.phone }}</td>
            <td>
              <button @click="deleteUser(user.id)" class="delete-button">
                Удалить
              </button>
            </td>
          </tr>
          <template v-if="user.children">
            <UserRow
              v-for="child in user.children"
              :key="child.id"
              :user="child"
              :level="1"
            />
          </template>
        </template>
      </tbody>
    </table>
    <UserModal
      v-if="showModal"
      @close="closeModal"
      @save="addUser"
      :users="users"
    />
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue';
import UserModal from './UserModal.vue';
import UserRow from './UserRow.vue';

export default {
  components: {
    UserModal,
    UserRow,
  },
  setup() {
    const users = ref([]);
    const showModal = ref(false);
    const sortKey = ref('name');
    const sortDirection = ref(1);

    const openModal = () => (showModal.value = true);
    const closeModal = () => (showModal.value = false);

    const addUser = (newUser) => {
      if (newUser.parentId !== null) {
        const parent = findUserById(users.value, newUser.parentId);
        if (parent) {
          parent.children = parent.children || [];
          parent.children.push(newUser);
        }
      } else {
        users.value.push(newUser);
      }
      saveToLocalStorage();
    };

    const deleteUser = (id) => {
      const deleteById = (userList) => {
        for (let i = 0; i < userList.length; i++) {
          if (userList[i].id === id) {
            userList.splice(i, 1);
            return true;
          }
          if (userList[i].children) {
            if (deleteById(userList[i].children)) return true;
          }
        }
        return false;
      };
      deleteById(users.value);
      saveToLocalStorage();
    };

    const saveToLocalStorage = () => {
      localStorage.setItem('users', JSON.stringify(users.value));
    };

    const loadFromLocalStorage = () => {
      const savedUsers = localStorage.getItem('users');
      if (savedUsers) users.value = JSON.parse(savedUsers);
    };

    const sortedUsers = computed(() => {
      return [...users.value].sort((a, b) => {
        const sortOrder = sortDirection.value;
        return (a[sortKey.value] > b[sortKey.value] ? 1 : -1) * sortOrder;
      });
    });

    const sortTable = (key) => {
      if (sortKey.value === key) sortDirection.value *= -1;
      else {
        sortKey.value = key;
        sortDirection.value = 1;
      }
    };

    const findUserById = (users, id) => {
      for (const user of users) {
        if (user.id === id) return user;
        if (user.children) {
          const found = findUserById(user.children, id);
          if (found) return found;
        }
      }
    };

    onMounted(() => loadFromLocalStorage());

    return {
      users,
      showModal,
      openModal,
      closeModal,
      addUser,
      deleteUser,
      sortedUsers,
      sortTable,
    };
  },
};
</script>

<style scoped>
.table-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  max-width: 800px;
  margin: 20px auto;
}

.add-button {
  font-size: 16px;
  padding: 10px 20px;
  color: white;
  background-color: #4caf50;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
  margin-bottom: 15px;
}

.add-button:hover {
  background-color: #45a049;
}

.user-table {
  width: 100%;
  border-collapse: collapse;
  background-color: white;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  border-radius: 5px;
  overflow: hidden;
}

.user-table th, .user-table td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

.user-table th {
  background-color: #4caf50;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

.user-table th:hover {
  background-color: #45a049;
}

.user-row:hover {
  background-color: #f1f1f1;
}

.user-table td {
  color: #333;
}

.delete-button {
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 6px 12px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.delete-button:hover {
  background-color: #e53935;
}
</style>
