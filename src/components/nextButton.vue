<template>
    <button class="next" :style="getNextButtonStyle">Next Product</button>
</template>

<script>
    export default {
        name: "nextButton",
    }

    
</script>

<style>
    .next {
        border: 2px solid #aaaaaa;
        background-color: white;
    }
</style>