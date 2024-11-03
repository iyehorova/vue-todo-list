<script>
export default {
  props: {
    value: String,
  },
  emits: ['update:value']
}
</script>

<template>
  <nav class="filter">
    <a
      key="{filterKey}"
      href="#/"
      class="filter__link"
      :class="{ selected: value === 'all' }"
      @click="$emit('update:value', 'all')"
    >
      All
    </a>
    <a
      key="{filterKey}"
      href="#/"
      class="filter__link"
      :class="{ selected: value === 'active' }"
      @click="$emit('update:value', 'active')"
    >
      Active
    </a>
    <a
      key="{filterKey}"
      href="#/"
      class="filter__link"
      :class="{'selected': value === 'completed'}"
      @click="$emit('update:value', 'completed')"
    >
      Completed
    </a>
  </nav>
</template>

<style></style>
