<template>
    <div class="audioClip"
         v-bind:class="[clip.backgroundColor, clip.id]"
    ></div>
</template>

<script>
    import WaveSurfer from 'wavesurfer';

    export default {
        name: "AudioClip",
        props: ['clip', 'currentMilliseconds', 'isRunning'],
        watch: {
            currentMilliseconds: function(newVal, oldVal){
                // console.log('Prop changed in clip: ', newVal, ' | was: ', oldVal);
                if(newVal == this.startPos){
                    this.wavesurfer.play();
                }
            },
            isRunning: function(newVal){
                if(!newVal){
                    this.wavesurfer.pause();
                    console.log('beendet' + newVal)           }
            }
        },
        methods: {

        },
        created (){

        },
        beforeMount(){

        },
        mounted () {
            this.wavesurfer = WaveSurfer.create({
                container: '.' + this.clip.id,
                height: 70,
                waveColor: this.clip.waveColor,
                cursorColor: 'transparent',
                progressColor: this.clip.progressColor,
                minPxPerSec: 10,
                fillParent: false
            });
            this.wavesurfer.load("https://upload.wikimedia.org/wikipedia/commons/6/6e/Micronesia_National_Anthem.ogg")
            console.log(this.wavesurfer);

            let r = document.querySelector(".nana");
            this.startPos = (r.offsetLeft * 100);
            console.log('startPos: ' + this.startPos);
        },
        update (){

        }
    }
</script>

<style scoped>

    .audioClip{
        position: absolute;
        width: auto;
        height: calc(100% - 2px);
        border-radius: 10px;
        left: 100px;
    }

    .audioClipBlue{
        background-color: #2f35a0;
        border: solid 2px #2c3195;
    }

</style>