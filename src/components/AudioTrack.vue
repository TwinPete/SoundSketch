<template>
    <div class="audioTrack"
    >

    </div>
</template>

<script>
    import AudioClip from './AudioClip.vue'

    export default {
        name: "AudioTrack",
        props: ['value', 'isRunning'],
        data () {
            return {
                clips: [
                    {
                        id: 'nana',
                        waveColor: '#5290db',
                        progressColor: '#7eb5f1',
                        backgroundColor: 'audioSnippetBlue',
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