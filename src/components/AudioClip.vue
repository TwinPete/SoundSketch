<template>
    <div class="audioClip"
         @click="startDrag()"
         v-bind:style="{ left: clip.offsetLeft + 'px' }"
    >
        <div class="waveform" v-bind:class="[clip.backgroundColor, clip.id]"></div>
        <div class="overlay"></div>
        <div class="resizeLeft"></div>
        <div class="resizeRight" @mousedown="startResize()" @mouseup="stopResize()"></div>
    </div>
</template>

<script>
    import WaveSurfer from 'wavesurfer';

    export default {
        name: "AudioClip",
        props: ['clip', 'currentMilliseconds', 'currentSeconds', 'isRunning', 'isDragging'],
        data () {
            return {
                onResize: false
            }
        },
        watch: {
            endPos: function(newVal, oldVal){
                console.log('new Val: ' + newVal + ' old Val: ' + oldVal);
            },
            currentMilliseconds: function(newVal, oldVal){
                console.log('endPos' + this.clip.endPos);
                console.log('this clip: current position: ' + this.clip.offsetLeft);
                console.log('Prop changed in clip ' + this.clip.id + ': ', newVal/1000, ' | was: ', oldVal/1000);
                if(!this.clip.isPlaying){
                    console.log('startPos' + this.clip.startPos);
                    if(this.isRunning && newVal >= this.clip.startPos){
                        console.log('heureka! ');
                        this.clip.isPlaying = true;
                        // alert((newVal/1000) - (this.clip.offsetLeft/10) );
                        this.wavesurfer.play((newVal/1000) - (this.clip.offsetLeft/10), (this.clip.endPos/100));
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
            startResize(){
                this.onResize = true;
            },
            stopResize(){
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
            this.wavesurfer = WaveSurfer.create({
                container: '.' + this.clip.id,
                height: 70,
                waveColor: this.clip.waveColor,
                cursorColor: 'transparent',
                progressColor: this.clip.progressColor,
                minPxPerSec: 10,
                fillParent: false
            });



            if(this.clip.id == 'nana'){
                this.wavesurfer.load("https://upload.wikimedia.org/wikipedia/commons/b/b5/Dub_Beat.ogg")
            }
            if(this.clip.id == 'nena'){
                this.wavesurfer.load("https://upload.wikimedia.org/wikipedia/commons/0/06/Summertime.ogg")
            }

            console.log(this.wavesurfer);

            let o = this.clip;
            let r = document.querySelector("." + o.id);

            if (typeof(r) != 'undefined' && r != null)
            {
                // alert(o.id + ' exists');
                // let e = this.clip.offsetLeft;
                // alert(e);
                // let s = window.getComputedStyle(r).getPropertyValue('width');
                // alert(s);
            }


            // setTimeout(function(){
            //     let r = document.querySelector("." + o.id);
            //     console.log('dieser clip: ');
            //     console.log(r);
            //     o.startPos = (r.offsetLeft * 100);
            //
            //     console.log('startPos for ' +o.id + ':' + o.startPos);
            //
            //     o.endPos = ((r.offsetLeft + r.offsetWidth) * 100);
            //     console.log('endPos: ' + o.endPos);
            // },9000);

            // setTimeout(function(){
            //     let r = document.querySelector("." + o.id);
            //     console.log('dieser clip: ');
            //     let compStyle = window.getComputedStyle(r);
            //     let left = compStyle.getPropertyValue('left');
            //     console.log('dieser clip: ' + o.id + ' Left: ' + left);
            //
            // },9000);



            console.log('dieser clip: ');
            console.log(r);
            o.startPos = (this.clip.offsetLeft * 100);

            console.log('startPos for ' +o.id + ':' + o.startPos);

            setTimeout(function(){
                let compStyle = window.getComputedStyle(r);
                let width = compStyle.getPropertyValue('width').match(/\d+/);
                o.endPos = (o.startPos * 100) + (width * 100);
                console.log('offset width of element: ' + (width));
                console.log('endPos: ' + o.endPos);
            }, 1000);

            // var p1 = new Promise(
            //     // Resolver-Funktion kann den Promise sowohl auflösen als auch verwerfen
            //     // reject the promise
            //     function(resolve, reject) {
            //
            //         this.wavesurfer.on('waveform-ready', function () {
            //             resolve('hello Promise');
            //         });
            //     });
            //
            // p1.then(function(val){
            //     alert(val);
            // });

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
        border-radius: 10px;
        /*left: 100px;*/
        overflow: hidden;
    }

    .waveform{
        width: auto;
        height: 100%;
        border-radius: 10px;
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

</style>