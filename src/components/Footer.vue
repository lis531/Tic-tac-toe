<script setup>

</script>

<template>
    <p>Borys Gajewski &copy; {{ new Date().getFullYear() }}</p>
</template>

<style scoped>
    p {
        font-size: 1.5rem;
        margin: 2vh 0 0 0;
        @media (max-width: 1024px) {
            font-size: 1.2rem;
        }
    }
</style>