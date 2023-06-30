<template>
    <div class="container">
        <br>
        Spa component
        <br>
        <br>
        <navigation />
        <error/>
        <router-view></router-view>
    </div>
</template>

<script>

import Navigation from "./components/Navigation.vue";
import Error from "./components/Error.vue";

export default {
    inject: ["auth"],
    components: { Navigation, Error },
    created() {
        axios.interceptors.response.use(
            response => { return response },
            error => {
                if (error.response.status === 401) {
                    this.auth.reset();
                }
                return Promise.reject(error);
            }
        );
    },
};

</script>
