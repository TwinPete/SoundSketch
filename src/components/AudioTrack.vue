<template>
    <div class="audioTrack"
    >
        <AudioClip
                v-bind:key="clip.id" v-for="clip in clips"
                v-bind:clip="clip"
                v-bind:currentMilliseconds="currentMilliseconds"
                v-bind:currentSeconds="currentSeconds"
                v-bind:isRunning="isRunning"
                v-bind:isDragging="isDragging"
        />
    </div>
</template>

<script>
    import AudioClip from './AudioClip.vue'

    export default {
        name: "AudioTrack",
        components: {
          AudioClip
        },
        props: ['currentMilliseconds', 'currentSeconds', 'isRunning', 'isDragging'],
        data () {
            return {
                clips: [
                    {
                        id: 'nana',
                        waveColor: '#5290db',
                        progressColor: '#7eb5f1',
                        backgroundColor: 'audioClipBlue',
                        isPlaying: false,
                        startPos: 0,
                        endPos: 0,
                        duration: 0,
                        isDragged: false,
                    }
                ]
            }
        },
        methods: {

        },
        mounted() {


        },
        components: {
            AudioClip
        },
    }
</script>

<style scoped>

.audioTrack{
    min-width: 100%;
    height: 69px;
    border-bottom: solid 1px #3d3d3d;
}
</style>