<template>
    <div class="audioBoard">
        <TimeControls />
    </div>
</template>

<script>
    import AudioTrack from './AudioTrack.vue'
    import AudioControls from './AudioControls.vue'
    import TimeControls from './TimeControls.vue'

    export default {
        name: "AudioBoard",
        components: {
            AudioTrack,
            AudioControls,
            TimeControls
        },
        data() {

        },
        methods: {

        },
        mounted () {

        }
    }
</script>

<style scoped>

    .audioBoard{
        display: flex;
        flex-direction:  column;
        height: 90vh;
        width: calc(100% - 200px);
        background-color: #444;
    }



</style>