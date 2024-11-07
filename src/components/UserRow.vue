<template>
  <tr :style="rowStyle" class="user-row">
    <td>{{ user.name }}</td>
    <td>{{ user.phone }}</td>
  </tr>
  <template v-if="user.children">
    <UserRow
      v-for="child in user.children"
      :key="child.id"
      :user="child"
      :level="level + 1"
    />
  </template>
</template>

<script>
import { computed } from 'vue';

export default {
  props: {
    user: {
      type: Object,
      required: true,
    },
    level: {
      type: Number,
      default: 0,
    },
  },
  setup(props) {
    const rowStyle = computed(() => ({
      paddingLeft: `${props.level * 20}px`,
    }));

    return {
      rowStyle,
    };
  },
};
</script>

<style scoped>
.user-row {
  transition: background-color 0.3s;
}

.user-row:hover {
  background-color: #e9e9e9;
}
</style>
