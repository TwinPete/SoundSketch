<template>
    <div class="audioClip"

         v-bind:style="{ left: clip.offsetLeft + 'px' }"
    >
        <div class="waveform" v-bind:class="[clip.backgroundColor, clip.id]"></div>
        <div class="overlay"></div>
        <div class="resizeLeft" @mousedown="$emit('startCroppingLeft', clip.id)" @mouseup="$emit('stopCropping', clip.id)"></div>
        <div class="resizeRight" @mousedown="$emit('startCroppingRight', clip.id)" @mouseup="$emit('stopCropping', clip.id)"></div>
    </div>
</template>

<script>
    import WaveSurfer from 'wavesurfer';

    export default {
        name: "AudioClip",
        props: ['clip', 'currentMilliseconds', 'currentSeconds', 'isRunning', 'isDragging'],
        data () {
            return {
                onResize: false,
                endPos: this.cropRight
            }
        },
        watch: {
            endPos: function(newVal, oldVal){
                // console.log('new Val: ' + newVal + ' old Val: ' + oldVal);
            },
            cropLeft: function(newVal, oldVal){
                alert('gdgs');
            },
            currentMilliseconds: function(newVal, oldVal){
                let endPos = ((this.clip.endPos - this.clip.startPos)/1000);
                // console.log('endPos ' + endPos);
                // console.log('this clip: current position: ' + this.clip.offsetLeft);
                // console.log('Prop changed in clip ' + this.clip.id + ': ', newVal/1000, ' | was: ', oldVal/1000);
                if(!this.clip.isPlaying){
                    // console.log('startPos' + this.clip.startPos);
                    if(this.isRunning && newVal >= this.clip.startPos){
                        console.log('heureka! ');
                        this.clip.isPlaying = true;
                        // alert((newVal/1000) - (this.clip.offsetLeft/10) );
                        console.log('startPos');
                        let start = this.clip.startPos;
                        console.log(start);
                        console.log('this offsetLeft');
                        let offset = this.clip.offsetLeft*100;
                        console.log(offset);
                        console.log('this ne val and current time status');
                        console.log(newVal);
                        let difference = (start - offset)/1000;
                        console.log('la difference ');
                        console.log(difference);
                        let startPos = (newVal/1000) - (this.clip.offsetLeft/10);
                        console.log(startPos);
                        // startPos = ((newVal/1000) - (this.clip.offsetLeft/10)) + difference;
                        this.wavesurfer.play(startPos, endPos);
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

            startDrag(){
                alert('drag');
            },
            startCroppingRight(){
                this.onResize = true;
            },
            stopCroppingRight(){
                this.onResize = false;
            },
            resizeRight(event){
                if(this.onResize){
                    console.log(event.clientX - 422);
                    console.log(this.endPos());
                    event.preventDefault();

                    let clip = document.querySelector('.nana').parentNode;
                    console.log(clip);

                }
            }
        },
        created (){

        },
        beforeMount(){

        },
        ready: function(){
            console.log('ready');
        },
        mounted () {


            if(this.type == 'file'){
                console.log();
            }else if(this.type == 'recording'){
                console.log();
            }


            this.wavesurfer = WaveSurfer.create({
                container: '.' + this.clip.id,
                height: 70,
                waveColor: this.clip.waveColor,
                cursorColor: 'transparent',
                progressColor: this.clip.progressColor,
                minPxPerSec: 10,
                fillParent: false
            });

            if(this.clip.type == 'file'){
                this.wavesurfer.load(this.clip.path)
            }
            if(this.clip.type == 'recording'){
                this.wavesurfer.load(this.clip.blob)
            }

            console.log(this.wavesurfer);

            let o = this.clip;
            let r = document.querySelector("." + o.id);



            console.log('dieser clip: ');
            console.log(r);
            console.log('offsetLeft für diesen Clip:  ' + this.clip.offsetLeft);
            o.startPos = (this.clip.offsetLeft * 100);

            console.log('startPos for ' +o.id + ':');
            console.log(o.startPos);

            setTimeout(function(){
                let compStyle = window.getComputedStyle(r);
                let width = compStyle.getPropertyValue('width').match(/\d+/);
                console.log('die width dieses clips ist: '+ width);
                console.log('die duration dieses CLips ist ');
                console.log(width*100);

                let sum = o.startPos + (width*100);
                console.log(sum);
                o.endPos = sum;
                sum = sum - o.startPos;
                o.duration = sum;
                console.log('offset width of element: ' + (width));
                console.log('endPos: ' + o.endPos);
                console.log('duration: ' + o.duration);
            }, 1000);


        },
        updated (){

        }
    }
</script>

<style scoped>

    .audioClip{
        position: absolute;
        width: auto;
        height: calc(100% - 2px);
        border-radius: 15px;
        /*left: 100px;*/
        overflow: hidden;
    }

    .waveform{
        width: auto;
        height: 100%;
        border-radius: 15px;
    }

    .overlay{
        position: absolute;
        top: -1px;
        left: -1px;
        width: calc(100% + 2px);
        height: calc(100% + 2px);
        border-radius: 10px;
        background-color: transparent;
        z-index: 99;
    }

    .resizeLeft, .resizeRight{
        position: absolute;
        transform: translateY(-50%);
        height: 90%;
        width: 10px;
        background-color: transparent;
        z-index: 100;
        top: 50%;
        cursor: w-resize;
    }

    .resizeLeft{
        left: -1px;
    }

    .resizeRight{
        right: 0;
    }

    .audioClipBlue{
        background-color: #2f35a0;
        border: solid 1px #2c3195;
    }

    .audioClipRed{
        background-color: #a0302c;
        border: solid 1px #952d29;
    }

    .audioClipPink{
        background-color: #a09e2c;
        border: solid 1px #878525;
    }

</style>