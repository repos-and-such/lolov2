<template>
  <div class="content-modal-background" @click="closeModal()">
    <div class="content-modal" @click.stop="">
      <button type="button" class="close">
        <i class="material-icons" @click="closeModal()">close</i>
      </button>
      <p id="article-content"></p>
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
button {
  position: absolute;
  top: 20px;
  right: 2vw;
  border: none;
  -webkit-appearance: none;
  -moz-appearance: none;
  color: rgb(255, 253, 252);
  background: transparent;
  cursor: pointer;
}

button:focus {
  outline: none !important;
}

button:active {
  transform: scale(0.95);
  outline: none !important;
  box-shadow: none;
}

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
  width: 80vw;
  max-width: 1200px;
  height: 92vh;
  background-color: white;
  z-index: 199;
  padding: 14px 24px;
  overflow: auto;
  box-shadow: 3px 3px 5px rgb(71, 71, 71);
}

@media screen and (max-width: 700px){
.content-modal {
  width: 100vw;
  height: 76vh;
}

button {
  top: 54px;
  right: 3vw;
}
}

</style>
