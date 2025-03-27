<script setup>
import OMenu from '@/components/OMenu.vue'
import { ref, computed, inject } from 'vue'

const props = defineProps({
  modelValue: {
    type: [String, Number, Boolean, Object],
    default: null,
  },
  options: {
    type: Array,
    default: () => [],
    validator: (value) => {
      return value.every((option) => 'value' in option && 'label' in option)
    },
  },
  placeholder: {
    type: String,
    default: 'Select an option',
  },
  disabled: {
    type: Boolean,
    default: false,
  },
})

const emit = defineEmits(['update:modelValue', 'change'])

// Access rem conversion utilities from App.vue
const convertRemToPixels = inject('convertRemToPixels', null)
const remToPixel = inject('remToPixel', ref(16))

// Dropdown offset using rem conversion
const dropdownOffset = computed(() => {
  return [0, 0.5] // 0 horizontal, 0.5rem vertical offset
})

// Get the currently selected option
const selectedOption = computed(() => {
  return props.options.find((option) => option.value === props.modelValue)
})

// Display value (either selected option or placeholder)
const displayValue = computed(() => {
  return selectedOption.value ? selectedOption.value.label : props.placeholder
})

// Track open state
const isOpen = ref(false)
const dropdownMenu = ref(null)

// Methods
const selectOption = (option) => {
  emit('update:modelValue', option.value)
  emit('change', option.value)

  // Close the dropdown
  if (dropdownMenu.value) {
    dropdownMenu.value.closeMenu()
  }
}

const onDropdownOpen = () => {
  isOpen.value = true
}

const onDropdownClose = () => {
  isOpen.value = false
}

// Expose methods for parent component
defineExpose({
  openDropdown: () => {
    if (dropdownMenu.value && !props.disabled) {
      dropdownMenu.value.openMenu()
    }
  },
  closeDropdown: () => {
    if (dropdownMenu.value) {
      dropdownMenu.value.closeMenu()
    }
  },
})
</script>

<template>
  <div class="custom-dropdown">
    <OMenu
      :offset="dropdownOffset"
      placement="bottom-start"
      @open="onDropdownOpen"
      @close="onDropdownClose"
      ref="dropdownMenu"
    >
      <template #trigger>
        <button
          class="dropdown-button"
          :class="{ 'is-open': isOpen, 'is-disabled': disabled }"
          :disabled="disabled"
        >
          <span class="dropdown-value">{{ displayValue }}</span>
          <span class="dropdown-arrow" :class="{ 'is-open': isOpen }">▼</span>
        </button>
      </template>
      <template #content>
        <div class="dropdown-content">
          <input type="text" />
          <input type="text" />
        </div>
      </template>
    </OMenu>
  </div>
</template>

<style scoped>
.custom-dropdown {
  display: inline-block;
  width: 100%;
  position: relative;
}

.dropdown-button {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  padding: 0.625rem 1rem;
  background-color: white;
  border: 0.0625rem solid #ccc;
  border-radius: 0.25rem;
  cursor: pointer;
  text-align: left;
  transition: border-color 0.2s;
}

.dropdown-button:hover:not(.is-disabled) {
  border-color: #999;
}

.dropdown-button.is-open {
  border-color: #2196f3;
}

.dropdown-button.is-disabled {
  background-color: #f5f5f5;
  cursor: not-allowed;
  color: #999;
}

.dropdown-arrow {
  font-size: 0.75rem;
  transition: transform 0.2s;
}

.dropdown-arrow.is-open {
  transform: rotate(180deg);
}

.dropdown-content {
  min-width: 100%;
}

.dropdown-item {
  padding: 0.625rem 1rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

.dropdown-item:hover {
  background-color: #f0f8ff;
}

.dropdown-item.is-selected {
  background-color: #e3f2fd;
  font-weight: bold;
}
</style>
