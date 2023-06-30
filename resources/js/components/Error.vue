<template>
    <div>
        <div v-show="isError" class="modal">
            <div class="modal-content">
                <h3>Upps, coś poszło nie tak!</h3>
                <h6 class="text-justify">{{ message }}</h6>
                <div class="d-flex mt-3">
                    <button @click.prevent="hide" class="btn btn-light mr-3">Ukryj</button>
                    <a href="/" class="btn btn-light">Strona główna</a>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    inject: ["eventBus"],
    created() { this.eventBus.$on("error", this.handleError); },
    beforeDestroy() { this.eventBus.$off("error"); },
    data() {
        return {
            isError: false,
            message: ''
        };
    },
    methods: {
        hide() { this.isError = false; },
        handleError(err) {
            this.isError = true;
            if (err.response) {
                this.message = `${err.response.statusText} - ${err.response.status}`;
            } else {
                this.message = "HTTP Error!";
            }
            console.log(err);
        }
    },
    watch: {
        $route(to, from) {
            this.hide();
        }
    }
};
</script>

    
<style>
.modal {
  display: flex;
  align-items: center;
  justify-content: center;
  position: fixed;
  z-index: 1;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  background-color: #d40000;
  padding: 30px;
  border: 1px solid #000000;
  max-width: 500px;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
}

.btn {
  padding: 10px 20px;
}
</style>