<template>
  <div class="demo-container">
    <div class="demo-header">
      <div class="demo-title" v-if="title">{{ title }}</div>
      <div class="demo-description" v-if="description">{{ description }}</div>
    </div>

    <div class="demo-content">
      <OMenu
        :trigger-type="triggerType"
        :placement="placement"
        :offset="offset"
        :close-on-outside-click="closeOnOutsideClick"
        :close-on-esc="closeOnEsc"
      >
        <template #trigger>
          <slot name="trigger">
            <button class="trigger-button">{{ triggerText }}</button>
          </slot>
        </template>

        <template #content>
          <slot name="content">
            <div v-for="item in items" :key="item.id" class="menu-item" @click="selectItem(item)">
              {{ item.text }}
            </div>
          </slot>
        </template>
      </OMenu>

      <div v-if="selectedItem" class="selected-item">Selected: {{ selectedItem.text }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  title: {
    type: String,
    default: '',
  },
  description: {
    type: String,
    default: '',
  },
  triggerType: {
    type: String,
    default: 'click',
  },
  placement: {
    type: String,
    default: 'bottom',
  },
  offset: {
    type: Array,
    default: () => [0, 0.5],
  },
  closeOnOutsideClick: {
    type: Boolean,
    default: true,
  },
  closeOnEsc: {
    type: Boolean,
    default: true,
  },
  triggerText: {
    type: String,
    default: 'Open Menu',
  },
  items: {
    type: Array,
    default: () => [
      { id: 1, text: 'Option 1' },
      { id: 2, text: 'Option 2' },
      { id: 3, text: 'Option 3' },
      { id: 4, text: 'Option 4' },
    ],
  },
})

// State
const selectedItem = ref(null)

// Methods
const selectItem = (item) => {
  selectedItem.value = item
}

// Expose menu methods for programmatic control
defineExpose({
  openMenu: () => {
    const menuInstance = document.querySelector('.menu-instance')
    if (menuInstance && typeof menuInstance.openMenu === 'function') {
      menuInstance.openMenu()
    }
  },

  closeMenu: () => {
    const menuInstance = document.querySelector('.menu-instance')
    if (menuInstance && typeof menuInstance.closeMenu === 'function') {
      menuInstance.closeMenu()
    }
  },

  toggleMenu: () => {
    const menuInstance = document.querySelector('.menu-instance')
    if (menuInstance && typeof menuInstance.toggleMenu === 'function') {
      menuInstance.toggleMenu()
    }
  },
})
</script>

<style scoped>
.demo-container {
  border: 1px solid #eaeaea;
  border-radius: 0.25rem;
  padding: 1rem;
  margin-bottom: 1.5rem;
  background-color: #fafafa;
}

.demo-header {
  margin-bottom: 1rem;
}

.demo-title {
  font-weight: bold;
  font-size: 1.1rem;
  margin-bottom: 0.25rem;
}

.demo-description {
  font-size: 0.9rem;
  color: #666;
  margin-bottom: 0.5rem;
}

.demo-content {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.trigger-button {
  padding: 0.5rem 1rem;
  background-color: #4caf50;
  border: none;
  color: white;
  border-radius: 0.25rem;
  cursor: pointer;
}

.selected-item {
  margin-top: 0.5rem;
  padding: 0.5rem;
  background-color: #e8f5e9;
  border-radius: 0.25rem;
  font-size: 0.9rem;
}

.menu-item {
  padding: 0.5rem 1rem;
  cursor: pointer;
}

.menu-item:hover {
  background-color: #f5f5f5;
}
</style>
