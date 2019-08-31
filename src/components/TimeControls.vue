<template>
    <div class="timeControls">
        <div class="watch">
            <div class="watchControls">
                <div class="play" @click="startWatch"><font-awesome-icon icon="play" /></div>
                <div class="pause" @click="pauseWatch"><font-awesome-icon icon="pause" /></div>
                <div class="restart"><font-awesome-icon icon="stop" /></div>
                <div class="record"><font-awesome-icon icon="microphone" /></div>
                <div class="led">
                    <div class="light" v-bind:class="{lightOn: isRunning}"></div>
                </div>
            </div>
            <div class="display">
                {{ watch }}
            </div>
        </div>
        <div class="timeline">
            <div class="frame">
                <div class="zoom"></div>
                <div class="range">
                    <div class="rangeDisplay"
                         @mousemove="slide"
                    ></div>
                    <div class="slider" draggable="false" v-bind:class="{isDragging: isDragging}">
                        <div class="sliderHold"
                             v-on:mousedown="dragStart()"
                             v-on:mouseup="dragEnd()"
                        ></div>
                    </div>
                </div>
            </div>
            <AudioControls
                    v-bind:tracks="tracks"
                    v-on:mute="$emit('mute', $event.target.value)"
            />
            <div class="audioTracks">

                <AudioTrack
                        v-for="track in tracks" v-bind:key="track.id"
                        v-bind:track="track"
                        v-bind:currentMilliseconds="currentMilliseconds"
                        v-bind:currentSeconds="currentSeconds"
                        v-bind:isRunning="isRunning"
                        v-bind:isDragging="isDragging"
                />
            </div>
        </div>
    </div>
</template>

<script>
    import AudioTrack from './AudioTrack.vue'
    import AudioControls from './AudioControls.vue'

    export default {
        name: "TimeControls",
        components: {
            AudioTrack,
            AudioControls
        },
        props: ['tracks'],
        data () {
            return {

                isDragging: false,
                isRunning: false,
                currentMilliseconds: 0,
                currentSeconds: 0,
                watch: '00:00:00:00',
                is: []

            }

        },
        watch: {
            currentMilliseconds: function(newVal, oldVal){
                // console.log('Prop changed: ', newVal, ' | was: ', oldVal);
                let slider = document.querySelector('.slider');
                slider.style.left = (newVal/100) + 'px';
            },
        },
        methods: {

            dragStart(){
                this.isDragging = true;
                let slider = document.querySelector('.slider');
                slider.style.cursor = 'grabbing';
                // console.log('drag-start');
            },
            dragEnd(){
                this.isDragging = false;
                let slider = document.querySelector('.slider');

                // console.log('drag-end');
            },
            slide(event){
                if(this.isDragging){
                    // console.log(event.clientX - 422);
                    this.currentSeconds = event.clientX -422;
                    event.preventDefault();

                    let slider = document.querySelector('.slider');
                    slider.style.left = (event.clientX - 422) + 'px';

                    this.currentMilliseconds = slider.offsetLeft * 100;
                    this.watch = this.msToTime(this.currentMilliseconds);
                }
            },
            msToTime(duration) {

                if(duration == 0){
                    return '00:00:00:00';
                }

                var milliseconds = parseInt((duration%1000)/100),
                    seconds = parseInt((duration/1000)%60),
                    minutes = parseInt((duration/(1000*60))%60),
                    hours = parseInt((duration/(1000*60*60))%24);

                hours = (hours < 10) ? "0" + hours : hours;
                minutes = (minutes < 10) ? "0" + minutes : minutes;
                seconds = (seconds < 10) ? "0" + seconds : seconds;
                milliseconds = (milliseconds < 10) ? "0" + milliseconds : milliseconds;

                let time = hours + ":" + minutes + ":" + seconds + "." + milliseconds;
                return String(time);
            },
            setWatch(){
                // console.log('milliseconds: ' + this.value);

                // console.log('time: ' + this.msToTime(this.value));
                this.watch = this.msToTime(this.value);
            },
            startWatch(){

                if(!this.isRunning){
                    this.isRunning = true;
                    let interval = setInterval(() => {
                        this.startInterval(interval);
                    }, 10);
                }
            },
            pauseWatch(){
                this.isRunning= false;
            },
            startInterval(id){


                // console.log(this.isRunning);

                if(this.isRunning == false){
                    // console.log('watch finished');
                    clearInterval(id);
                    // console.log(this.currentMilliseconds);
                    this.isRunning = false;
                }

                this.currentMilliseconds = this.currentMilliseconds + 10;
                // console.log(this.currentMilliseconds);
                this.watch = this.msToTime(this.currentMilliseconds);
                // console.log(this.watch);
            },

        },
    }
</script>

<style scoped>
    .timeControls{
        min-width: 100%;
        height: 100%;
        display: flex;
        flex-direction: column;
    }

    .watch{
        display: flex;
        align-items: center;
        width: 100%;
        height:70px;
        background-color: #333;
        border-bottom: solid 1px #404040;
    }

    .watchControls{
        width: 500px;
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .watchControls > div{
        width: 30px;
        height: 20px;
        padding: 10px 20px;
        background-color: #555;
        border-right: solid 1px #494949;
        border-radius: 1px;
        font-size: 2rem;
        color: #b8b8b8;
        cursor: pointer;
    }

    .led{
        width: 24px!important;
        height: 24px!important;
        padding: 8px!important;
        cursor: default!important;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .light{
        width: 100%;
        height: 100%;
        border-radius: 50%;
        background-color: #666;
    }
    .lightOn{
        background-color: #cd2b29;
        background-color: #17cd77;
    }

    .display{
        display: flex;
        align-items: center;
        justify-content: center;
        height: auto;
        width: 150px;
        color: #fff;
        font-size: 2rem;
        padding: 10px 50px;
        background-color: #2c2c2c;
        border: solid 1px #2e2e2e;
        border-radius: 2px;
    }
    .timeline{
        position: relative;
        display: flex;
        flex-direction: column;
        height: 100%;
        overflow-x: scroll;
    }
    .frame{
        width: 100%;
        height: 100%;
        display: flex;
    }
    .zoom{
        width: 220px;
        height: 40px;
        background-color: #333;
        border-right: solid 1px #2d2d2d;
        border-bottom: solid 1px #2d2d2d;
    }
    .range{
        position: relative;
        width: calc(100% - 221px);
        height: 100%;

    }
    .rangeDisplay{
        height: 40px;
        background-color: #393939;
        border-bottom: solid 1px #303030;
    }

    .sliderHold{
        position: absolute;
        top: 0;
        left: 300px;
        height: 40px;
        width: 7px;
        transform: translateX(-50%);
        left: 50%;
        border-radius: 1px;
        border-bottom: solid 1px #454545;
        background-color: #555;
        cursor: grab;
        z-index: 100;
    }
    .slider{
        position: absolute;
        left: 0;
        /*width: 0.5px;*/
        width: 1px;
        top: 0;
        height: 100%;
        background-color: #783434;
        background-color: #777;
        -webkit-user-drag: none;
        -khtml-user-drag: none;
        -moz-user-drag: none;
        -o-user-drag: none;
        user-drag: none;
        user-select: text;
        z-index: 100;
    }
    .isDragging{
        cursor: pointer!important;
    }
    .slider::before, .slider::after{
        -webkit-user-drag: none;
        -khtml-user-drag: none;
        -moz-user-drag: none;
        -o-user-drag: none;
        user-drag: none;
    }
    .audioTracks{
        position: absolute;
        left: 221px;
        top: 42px;
        width: 100%;
        height: 70px;
    }

</style>