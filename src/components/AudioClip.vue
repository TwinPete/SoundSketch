<template>
    <div class="audioClip"
         v-bind:class="[clip.backgroundColor, clip.id]"
    ></div>
</template>

<script>
    import WaveSurfer from 'wavesurfer';

    export default {
        name: "AudioClip",
        props: ['clip', 'currentMilliseconds', 'currentSeconds', 'isRunning', 'isDragging'],
        watch: {
            endPos: function(newVal, oldVal){
                console.log('new Val: ' + newVal + ' old Val: ' + oldVal);
            },
            currentMilliseconds: function(newVal, oldVal){
                console.log('endPos' + this.endPos);
                // console.log('Prop changed in clip: ', newVal, ' | was: ', oldVal);
                if(!this.clip.isPlaying){
                    if(this.isRunning && newVal >= this.clip.startPos){
                        console.log('endPos: ' + this.clip.endPos);
                        this.clip.isPlaying = true;
                        this.wavesurfer.play((newVal/1000) - 10, (newVal/1000) - 1 );
                    }
                }

            },
            isRunning: function(newVal){
                // if(newVal){
                //     console.log('startPos ist: ' + this.startPos);
                //     if(this.currentMilliseconds >= this.startPos){
                //         let r = document.querySelector(".nana");
                //         let u = (this.currentMilliseconds/1000);
                //         console.log('los gehts mit current Seconds: ' + (this.currentMilliseconds/1000));
                //         this.wavesurfer.play(u - 10, u - 9);
                //     }
                // }
                if(!newVal){
                    this.wavesurfer.pause();
                    this.clip.isPlaying = false;
                    console.log('beendet' + newVal)
                }
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
            this.clip.startPos = (r.offsetLeft * 100);

            console.log('startPos: ' + this.clip.startPos);

            this.clip.endPos = ((r.offsetLeft + r.offsetWidth) * 100);
            console.log('endPos: ' + this.clip.endPos);


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