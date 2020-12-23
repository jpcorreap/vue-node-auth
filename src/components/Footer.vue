<template>
    <div class="flexbox-container">
            <div>
                <p>{{description}}</p>
            </div>
            <div>
                <h3>EQUIPO</h3>
                <ul v-for="item in team" :key="item.cod">
                    <li>{{item.nombre}} - {{item.rol}}</li>
                </ul>
            </div>
    </div>
</template>

<script>
export default {
    name: "FooterEquipo",
    props: ["team", "description"],
};
</script>

<style scoped>
.flexbox-container {
    display: -ms-flex;
    display: -webkit-flex;
    display: flex;
}
ul {
    list-style-type: none;
    padding: 0;
    margin: 0;
    margin-top: 5px;
}

.flexbox-container > div {
    width: 100%;
    padding: 10px;
    font-size: 14px;
    font-family: Roboto;
    background-color: rgb(71, 64, 68);
    color: white;
    line-height: 1;
    text-align: left;
}

.flexbox-container > div:first-child {
    margin-right: 1px;
}
</style>