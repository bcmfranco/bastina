<template>
  <div class="switcher" @click="toggleSwitch" :class="{ 'active': isActive }">
    <div class="switcher-button"></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isActive: false
    };
  },
  methods: {
    toggleSwitch() {
      this.isActive = !this.isActive;
      this.$emit('change', this.isActive);
    }
  }
};
</script>

<style scoped>
.switcher {
  width: 60px;
  height: 30px;
  background-color: #ccc;
  border-radius: 15px;
  position: relative;
  cursor: pointer;
}

.switcher.active {
  background-color: #5cb85c;
}

.switcher-button {
  width: 30px;
  height: 30px;
  background-color: #fff;
  border-radius: 15px;
  position: absolute;
  top: 0;
  left: 0;
  transition: transform 0.3s;
}

.switcher.active .switcher-button {
  transform: translateX(30px);
}
</style>
