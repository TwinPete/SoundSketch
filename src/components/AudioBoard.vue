<template>
    <div class="audioBoard">
        <TimeControls v-bind:tracks="tracks"
                      v-on:mute="mute(id)"
                      v-on:select-track="select"
        />
    </div>
</template>

<script>
    import AudioTrack from './AudioTrack.vue'
    import AudioControls from './AudioControls.vue'
    import TimeControls from './TimeControls.vue'

    export default {
        name: "AudioBoard",
        components: {
            TimeControls
        },
        data() {
            return {
                tracks: [
                    {
                        id: 0,
                        type: 'file',
                        mute: false,
                        selected: false
                    },
                    {
                        id: 1,
                        type: 'file',
                        mute: false,
                        selected: false
                    },
                    {
                        id: 2,
                        type: 'recording',
                        mute: false,
                        selected: false
                    }
                ]
            }
        },
        methods: {
            mute(value){
                alert(value);
            },
            select(id){
                this.tracks.forEach(function(track){
                    if(track.id != id){
                        track.selected = false;
                    }else{
                        track.selected = true;
                    }
                });
            }
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