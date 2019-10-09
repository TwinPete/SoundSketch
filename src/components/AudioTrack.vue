<template>
    <div class="audioTrack"

         
        @click="$emit('select-track', track.id)"
         @mousemove="getCursorMovement"

         v-on:keydown="cut()"
         v-bind:class="{ trackSelected: track.selected}"
         v-bind:id="id"
    >
        <AudioClip
                v-bind:key="clip.id" v-for="clip in clips"
                v-bind:clip="clip"
                v-bind:currentMilliseconds="currentMilliseconds"
                v-bind:currentSeconds="currentSeconds"
                v-bind:isRunning="isRunning"
                v-bind:isDragging="isDragging"
                v-on:startCroppingRight="startCroppingRight"
                v-on:startCroppingLeft="startCroppingLeft"
                v-on:stopCropping="stopCropping"

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
        props: ['currentMilliseconds', 'currentSeconds', 'isRunning', 'isDragging', 'isRecording', 'track'],
        watch: {
            currentMilliseconds: function(newVal, oldVal){

                if(this.isRecording){
                    let recording = document.querySelector('.recording');
                    let left = recording.offsetLeft;
                    recording.style.width = (this.currentMilliseconds/100 - left) + 'px';
                }
            },
            isRecording: function(newVal, oldVal){
                if(this.track.selected && newVal){
                    console.log('oldVal: ' + oldVal + ' and newVal: ' + newVal);
                    console.log('generate clip...')


                    let clips = this.clips;

                    let recording = document.createElement('div');
                    recording.classList.add('recording');
                    recording.setAttribute('id', 'recording');
                    let offsetLeft = this.currentMilliseconds/100;
                    recording.style.left = offsetLeft + 'px';
                    let track = document.querySelector('#track_' + this.track.id);
                    track.append(recording);


                    // Audio recording

                    let constraintObj = {
                        audio: true,
                        video: false
                    }

                    if(navigator.mediaDevices === undefined){
                        navigator.mediaDevices = {};
                        navigator.mediaDevices.getUserMedia = function(constraints){
                            let getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia;
                            if (!getUserMedia){
                                return Promise.reject(new Error('getUserMedia is not implemented in this Browser'));
                            }
                            return new Promise(function(resolve, reject){
                                getUserMedia.call(navigator, constraints, resolve, reject);
                            });
                        }
                    }else{
                        navigator.mediaDevices.enumerateDevices()
                            .then(devices => {
                                devices.forEach(device =>{
                                    console.log(device.kind.toUpperCase(), device.label);
                                })
                            })
                            .catch(err =>{
                                console.log(err.name, err.message);
                            })
                    }

                    navigator.mediaDevices.getUserMedia(constraintObj)
                        .then(function(mediaStreamObj){

                            let mediaRecorder = new MediaRecorder(mediaStreamObj);
                            let chunks = [];

                            mediaRecorder.start();
                            console.log(mediaRecorder.state);


                            document.querySelector('.record').addEventListener('click', function(e){
                                mediaRecorder.stop();
                                console.log(mediaRecorder.state);
                            });

                            mediaRecorder.ondataavailable = function(e){
                                chunks.push(e.data);
                            }
                            mediaRecorder.onstop = function(e){
                                let blob = new Blob(chunks, { 'type': 'audio/mp3' });
                                chunks = [];
                                let audioURL = window.URL.createObjectURL(blob);


                                let clip = {
                                    id: 'nuna',
                                    type: 'recording',
                                    blob: audioURL,
                                    waveColor: '#cfdb4f',
                                    progressColor: '#def191',
                                    backgroundColor: 'audioClipPink',
                                    isPlaying: false,
                                    startPos: 0,
                                    endPos: 0,
                                    duration: 0,
                                    isDragged: false,
                                    offsetLeft: offsetLeft,
                                    cropLeft: 0,
                                    cropRight: 0
                                }



                                console.log(clips);
                                clips.push(clip);
                                recording.remove();
                                console.log(clips);
                            }

                        })
                        .catch(function(err){
                           console.log(err.name, err.message);
                        });

                }
            }
        },
        data () {
            return {
                id: 'track_' + this.track.id,
                clips: [],
                croppingClipId: '',
                croppingLeft: false,
                croppingRight: false
            }
        },
        methods: {
            startCroppingRight(clipId){
                console.log('is cropping RIght...');
                console.log(clipId);
                this.croppingRight = true;
                this.croppingClipId = clipId;
            },
            startCroppingLeft(clipId){
                console.log('is cropping Left...');
                console.log(clipId);
                this.croppingLeft = true;
                this.croppingClipId = clipId;
            },
            stopCropping(clipId){
                // alert(clipId);
                this.croppingRight = false;
                this.croppingLeft = false;
                this.croppingClipId = '';

                let c = this.clips.filter( clip => clip.id == clipId);
                let clip = c[0];
                console.log(clip);

                clip.startPos = (clip.offsetLeft*100) + (clip.cropLeft);
                console.log('new startPos: ' + clip.startPos);

            },
            getCursorMovement(){
                if(this.croppingRight){
                    let currentPos = (event.clientX - 422);
                    console.log(currentPos);

                    console.log(this.croppingClipId);
                    let c = this.clips.filter( clip => clip.id == this.croppingClipId);
                    let clip = c[0];

                    let duration = clip.duration/100;
                    console.log('duration ist: '+ duration);
                    let offsetLeft = clip.offsetLeft;
                    console.log('offsetLeft' + offsetLeft);
                    clip.cropRight = (duration - (currentPos - offsetLeft));
                    console.log('cropRight ist: ' + clip.cropRight);

                    if(currentPos > (duration + offsetLeft)){
                        this.croppingRight = false;
                    }

                    let cropRight = clip.cropRight * 100;
                    console.log('cropRight in ms');
                    console.log(cropRight);

                    let i = (duration*100) + clip.startPos;
                    console.log('ganze duration');
                    console.log(i);

                    console.log('startPos');
                    console.log(clip.startPos);
                    clip.endPos = i - cropRight;
                    console.log('new End Pos: ');
                    console.log(clip.endPos);

                    console.log(clip);

                    let wave = document.querySelectorAll('.' + this.croppingClipId)[0].firstChild;

                    wave.style.width = (duration - clip.cropRight) + 'px';



                }
                if(this.croppingLeft){

                    let currentPos = (event.clientX - 422);
                    console.log(currentPos);

                    console.log(this.croppingClipId);
                    let c = this.clips.filter( clip => clip.id == this.croppingClipId);
                    let clip = c[0];

                    let startPos = clip.startPos/100;
                    if(currentPos <= clip.offsetLeft){
                        console.log('darunter');
                        clip.startPos = clip.offsetLeft*100;
                        this.croppingLeft = false;

                    }

                    let element = document.querySelectorAll('.' + this.croppingClipId)[0];
                    // console.log(element);
                    console.log(element.parentElement);

                    let cropLeft = currentPos - clip.offsetLeft
                    clip.cropLeft = cropLeft * 100;

                    // clip.startPos = (clip.offsetLeft*100) + (clip.cropLeft*100);

                    element.style.marginLeft = -cropLeft + 'px';
                    element.parentElement.style.left = (clip.offsetLeft + cropLeft) + 'px';
                }
            },
            cut(){
                alert('cut');
            }

        },
        mounted() {
            let clip_1 = {
                id: 'nana',
                type: 'file',
                path: './bach_prelude.wav',
                waveColor: '#5290db',
                progressColor: '#7eb5f1',
                backgroundColor: 'audioClipBlue',
                isPlaying: false,
                startPos: 0,
                endPos: 0,
                duration: 0,
                isDragged: false,
                offsetLeft: 100,
                cropLeft: 0,
                cropRight: 0
            }
            let clip_2 = {
                id: 'nena',
                type: 'file',
                path: './debussy_reflets.wav',
                waveColor: '#be635d',
                progressColor: '#d4958c',
                backgroundColor: 'audioClipRed',
                isPlaying: false,
                startPos: 0,
                endPos: 0,
                duration: 0,
                isDragged: false,
                offsetLeft: 200,
                cropLeft: 0,
                cropRight: 0
            }

            console.log(this.track);

            if(this.track.id == 0){
                this.clips.push(clip_1);
            }else if(this.track.id == 1){
                this.clips.push(clip_2);
            }

        },
        components: {
            AudioClip
        },
    }
</script>

<style>

.audioTrack{
    min-width: 100%;
    height: 69px;
    border-bottom: solid 1px #3d3d3d;

}
.trackSelected{
    background-color: #4d4e50;
}
.recording{
    position: absolute;
    width: auto;
    background-color: #a09e2c;
    height: 100%;
    border-radius: 15px;
}
</style>