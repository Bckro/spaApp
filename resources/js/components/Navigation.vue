<template>
    <div>
        Navigation
        <br>
        <router-link :to="{ name: 'home' }">Home</router-link> |
        <router-link v-if="!isLogged" :to="{ name: 'login' }">Login</router-link>
        <a v-if="isLogged" @click.prevent="logout" href>Wyloguj</a>
        <router-link v-if="isLogged" :to="{ name: 'dashboard' }">| Dashboard</router-link>
    </div>
</template>

<script>
import Error from "./Error";
export default {
    components: { Error },
    inject: ["auth", "eventBus"],
    data() {
        return { isLogged: null };
    },
    created() {
        this.isLogged = localStorage.getItem("isLogged");
        this.eventBus.$on("isLogged", value => {
            this.isLogged = value;
        });
    },
    beforeDestroy() {
        this.eventBus.$off("isLogged");
    },
    methods: {
        async logout() {
            try {
                await this.auth.logout();
            } catch (err) { }
        }
    }
};
</script>
