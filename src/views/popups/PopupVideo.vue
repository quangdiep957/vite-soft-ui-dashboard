<template>
  <div class="popup-container">
    <div class="popup-content">
      <i class="fas fa-long-arrow-alt-left" @click="closePopup()"></i>
      <iframe width="560" height="315" :src="url" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    videoLink: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      showPopup: false,
      url : ""
    };
  },
  methods: {
    openPopup() {
      this.showPopup = true;
    },
    closePopup() {
      this.$emit("close");
    }
  },
  created()
  {
    console.log(this.videoLink,"video");
      this.url = this.videoLink;
  },
  watch: {
    videoLink(newValue, oldValue) {
      this.url = newValue;
 
     }
  },
};
</script>

<style scoped>
.popup-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.36);
  z-index: 10000;

  display: flex;
  align-items: center;
  justify-content: center;
}

.popup-content {
  position: fixed;
    top: 50%;
    width: 50%;
    height: 50%;
    transform: translate(-50%, -50%);
    left: 50%;
    background-color: aliceblue;
}

iframe {
  width: 100%;
  height: 100%;
}
</style>
