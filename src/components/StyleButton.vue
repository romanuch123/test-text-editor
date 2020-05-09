<template>
  <button class="btn" @click="changeStyle">
    {{ name }}
  </button>
</template>

<script>
export default {
  name: 'StyleButton',
  props: {
    name: String,
  },
  data() {
    return {
    };
  },
  methods: {
    changeStyle() {
      this.$emit('change-style');
    }
  }
}
</script>

<style scoped>
  .btn {
    border: none;
    background: #388e3c;
    color: #f1f1f1;
    border-radius: 5px;
    padding: 5px 20px;
    margin: 5px;
  }
  .btn:focus {
    outline: none;
  }
  .btn:hover {
    cursor: pointer;
    background: #1b5e20;
  }
</style>
