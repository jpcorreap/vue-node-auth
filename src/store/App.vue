<template>
    <div id="app">
        <div class="row justify-content-center mb-5">
            <div class="col mt-5" v-for="(item, i) of articles" :key="i">
                <articles :member="item"></articles>
            </div>
        </div>
        <footer-equipo :description="description" :team="team"> </footer-equipo>
    </div>
</template>

<script>
import FooterEquipo from "./components/Footer.vue";
import Articles from "components/Articles"

export default {
    name: 'App',
    components: {
        Articles,
        FooterEquipo

},
data () {
    return {
        team: [
            {
                cod: 1,
                nombre: "Juan Pablo Correa", 
                rol:  "Arquitecto de Software"
            },
            {
                cod: 2,
                nombre: "Jair Fonseca P.",
                rol: "Tester"
            },
            {
                cod: 3,
                nombre: "Heiler Melendez",
                rol: "Analista de Software"
            },
        ],
        description: "Conoce nuestros articulos"
    };
},
};
</script>
<style >
#app {
    font-family: SF;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    font-family: "Noto Sans TC", sans-serif;
}
</style>