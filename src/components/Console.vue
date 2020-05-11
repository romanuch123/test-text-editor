<template>
  <div class="console">
    <h2>Console</h2>
    <div v-if="jsonText" class="console-view">
      {{ jsonText }}
    </div>
    <div v-else class="console-view">
      Console is empty...
    </div>

  </div>
</template>

<script>
export default {
  name: 'Console',
  props: {
    jsonText: String,
  },
  data() {
    return {
    };
  },
  methods: {
  }
}
</script>

<style scoped>
  .console-view {
    width: 90%;
    min-height: 100px;
    background: #f9f9f9;
    padding: 10px;
    margin: 0 auto;
    border: 1px solid black;
    border-radius: 8px;
  }
</style>
