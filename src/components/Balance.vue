<template>
    <h4>Your Balance</h4>
    <h1 id="balance">RM {{ total }}</h1>
</template>


<script setup>
const props = defineProps({
    total: {
        type: Number,
        required: true,
    },
});
</script>