<template>
    <div class="alerta">
        <slot></slot>
    </div>
</template>

<script setup>

</script>

<style scoped>
    .alerta{
        padding: 1rem;
        font-weight: 900;
        text-transform: uppercase;
        font-size: 1.6rem;
        text-align: center;
        background-color: var(--blanco);
        border-left: 1rem solid var(--rojo);
        color: var(--rojo);
        margin-bottom: 2rem;
    }

</style>