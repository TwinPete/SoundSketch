<template>
    <div class="audio-track__controls">
        <div class="control"
             v-for="track in tracks" v-bind:key="track.id"
             @click="$emit('mute', track.id)"
        >
            <div class="mute"

            ></div>
        </div>
    </div>
</template>

<script>
    export default {
        name: "AudioControls",
        props: ['tracks']

    }
</script>

<style scoped>

    .audio-track__controls{
        position: absolute;
        top: 41px;
        left: 0px;
        display :flex;
        flex-direction: column;
        width: 220px;
    }



    .control{
        height: 69px;
        width: 100%;
        background-color: #333;
        border-bottom: solid 1px #2a2a2a;
        border-right: solid 1px #2a2a2a;
        border-radius: 2px;
    }

    .mute{
        width: 100%;
        height: 100%;
    }


</style>