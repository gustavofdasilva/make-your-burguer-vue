<template>
    <div :class="theme == 'warn' ? 'warn-message':'success-message'" class="message-container">
        <p>{{ message }}</p>
  </div>
</template>

<script>
    export default {
        name: "Message",
        props: ["message", "theme"]
    }
</script>

<style scoped>
    .message-container{
        border: 2px solid;
        border-radius: 5px;
        padding: 10px;
        max-width: 400px;
        margin: 30px auto;
    }

    .warn-message {
        color: #850000;
        background-color: #ffcccc;
        border: 2px solid #ffb8b8;
    }

    .success-message {
        color: #004085;
        background-color: #cce5ff;
        border: 2px solid #b8daff;
    }

</style>