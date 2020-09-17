<template>
  <div class="content-modal-background" @click="closeModal()">
    <div class="content-modal" @click.stop="">
      <div id="article-content"></div>
    </div>
 </div>
</template>

<script>
export default {
  name: 'ContentModal',
  props: {
    content: String
  },
  methods: {
    closeModal() {
      this.$emit('close');
    }
  },
  mounted() {
    document.getElementById('article-content').innerHTML = this.content;
  }
}
</script>

<style scoped>
.content-modal-background {
  position: fixed;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.411);
  z-index: 98;
}

.content-modal {
  width: 70vw;
  height: 90vh;
  background-color: white;
  z-index: 99;
  padding: 16px;
  overflow: auto;
  box-shadow: 3px 3px 5px rgb(71, 71, 71);
}
</style>
