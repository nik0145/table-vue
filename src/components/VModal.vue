<template>
  <transition name="modal">
    <div v-show="isOpen" class="modal-mask" @click="closeOutside">
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header">
              User information
              <button
                type="button"
                @click="$emit('close')"
                class="close"
                data-dismiss="modal"
                aria-label="Close"
              >
                <span aria-hidden="true">&times;</span>
              </button>
            </slot>
          </div>

          <div class="modal-body">
            <slot name="body">
              <form>
                <div v-for="(row,key) in value" :key="key" class="form-group row">
              
            
                  <label :for="key" class="col-sm-2 col-form-label"
                    >{{key}}</label
                  >
                  <div class="col-sm-10">
                    <input
                      type="text"
                      readonly
                      :value="row"
                      class="form-control"
                      id="key"
                      placeholder=""
                    />
                  </div>
                 </div>
              </form>
              
            </slot>
          </div>

          <div class="modal-footer">
            <slot name="footer">
              <button
                type="button"
                @click="$emit('close')"
                class="btn btn-secondary"
              >
                Ok
              </button>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: "modal",
  props: {
    isOpen: Boolean,
    value: Object,
  },
  mounted() {
    window.addEventListener("keyup", this.listener);
  },
  destroyed() {
    window.removeEventListener("keyup", this.listener);
  },
  methods: {
    closeOutside(event) {
      event.stopPropagation();
      let [className] = this.$el.classList;
      if (
        Array.from(event.target.parentElement.classList).includes(className)
      ) {
        this.close();
      }
    },
    listener(event) {
      if (event.keyCode === 27) {
        this.close();
      }
    },
    close() {
      this.$emit("close");
        
    },
  },
};
</script>
<style scoped>
.modal-mask {
  position: fixed;
  overflow-x:hidden;
  overflow-y:auto;
  z-index: 9998;
  top: 0;
  left: 0;
  bottom: 0;
  outline: 0;
  left: 0;
  width: 100%;
  display: flex;
  flex-direction:column;
  background-color: rgba(0, 0, 0, 0.5);
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 500px;
  border-radius: 10px;
  margin: 0px auto;
  background-color: #fff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
