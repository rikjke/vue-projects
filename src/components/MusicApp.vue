<template>
    <v-app>
            <div class="music-app">
        <div class="music-container">
            <div class="player-container">
                <div class="playlist-cover">
                    <img v-if="playlistCover != 'undefined'" :src="playlistCover" alt="Playlist Cover">
                    <div class="playlist-description">
                        <h2 class="song-artist">{{current.artist}}</h2>
                        <h2 class="song-title">{{current.title}}</h2>
                        <div v-if="isPlaying" class="music-gif"><img :src="notes" alt="Music Notes"></div>
                    </div>
                 <div class="control">
                    <button @click="previous" class="prev"><i class="fas fa-backward"></i></button>
                    <button v-if="!isPlaying" @click="keep" class="play"><i class="fas fa-play"></i></button>
                    <button v-if="isPlaying" @click="pause" class="stop"><i class="fas fa-pause"></i></button>
                    <button @click="next" class="next"><i class="fas fa-forward"></i></button>
                    <div class="slider-container">
                            <i @click="decVolume" class="fa fa-volume-down"></i>
                            <v-slider
                            v-on:input="slider"
                            hint="Громкость мелодии"
                            max="1000"
                            min="0"
                            hide-details
                            color="#ffa4bb"
                            :value="volume"
                            track-color="#cccfff"
                            ></v-slider>
                            <i @click="incVolume" class="fa fa-volume-up"></i>
                    </div>
                </div>
                </div>


            </div>

            <div class="playlist">
                <h3>{{current.playlist}}</h3>
                <div v-for="song in data[0].songs" :key="song.id" class="song-container">

                    <button @click="play(song)"
                    :class="(song.src == current.src) ? 'song-playing song' : 'song'">
                    <div class="song-img"><img v-if="index == song.id && isPlaying" :src="equalizer" alt=""></div>{{song.title}}</button>

                    <div class="song-slider">
                        <v-slider
                        min="0" 
                        :max="index == song.id && player.duration?player.duration:100"
                        :key="song.id"
                        :disabled="index != song.id? true : false"
                        :value="index == song.id ? time : 0"
                        hide-details
                        track-color= "cccfff"
                        >

                        </v-slider>
                    </div>
                </div>
            </div>
        </div>
    </div>
    </v-app>
</template>

<script>
import playlistData from '../data.json'
export default {
    created() {
        this.songs = this.data[this.playlistIndex].songs
        this.current = this.songs[this.index - 1];
        this.player.src = require(`../assets/music/${this.current.src}`);
        this.player.onended = () => {
            this.next()
        };
        this.player.volume = 0.5;
        this.volume = this.player.volume * 1000;
        this.duration = this.player.duration;
        console.log(this.data[0].playlistCover)
        console.log(this.playlistCover)
        this.playlistCover = require(`../assets/${this.data[0].playlistCover}`)
        // this.playlistCover = require(this.data[0].playlistCover)
    },

    methods: {
        songTime() {
            this.duration = this.player.duration;
            this.time = this.player.currentTime;
             if (!this.isPlaying) {
                this.intervals.forEach(clearInterval)
                this.intervals = []
                }
            console.log(this.time, this.player.currentTime)
        },
        setVolume() {
            this.volume = this.player.volume * 1000;
            return this.player.volume * 1000
        },
        incVolume() {
            this.player.volume > 0.9? this.player.volume = 1 : this.player.volume += 0.1;
            this.setVolume();
        },
        decVolume() {
            this.player.volume < 0.1? this.player.volume = 0 : this.player.volume -= 0.1;
            this.setVolume();
        },
        slider(e) {
            this.player.volume = e / 1000;
        },
        previous() {
            if (this.current.id == 1) {
                this.current = this.songs[this.songs.length - 1];
            } else {
                this.current = this.songs[this.current.id - 2]
            }
            this.play(this.current);
        },
        next() {
            if (this.current.id == this.songs.length) {
                this.current = this.songs[0];
            } else {
                this.current = this.songs[this.current.id];
            }
            this.play(this.current)
        },
        keep() {
            this.player.play();
            this.isPlaying = true;
            if (this.isPlaying) {
                if (this.intervals.length) {
                    this.intervals.forEach(clearInterval)
                    this.intervals = []
                }
                var songTimer = setInterval(this.songTime, 1000);
                this.intervals.push(songTimer);
                console.log(this.intervals)
            }
        },
        play(song) {
            this.current = song;
            console.log(song);
            this.player.src = require(`../assets/music/${this.current.src}`);
            this.player.play();
            this.player.currentTime = 0;
            this.isPlaying = true;
            this.index = song.id;
            console.log(this.player.duration);
            if (this.isPlaying) {
                if (this.intervals.length) {
                    this.intervals.forEach(clearInterval)
                    this.intervals = []
                }
                var songTimer = setInterval(this.songTime, 1000);
                this.intervals.push(songTimer);
                console.log(this.intervals)
            }
        },
        pause() {
            this.player.pause();
            this.isPlaying = false;
        }
    },
    data() {
        return {
            data: playlistData,
            intervals: [],
            duration: 0,
            time: 0,
            volume: 0,
            playlistIndex: 0,
            notes: require('../assets/music.gif'),
            equalizer: require('../assets/playing.gif'),
            player: new Audio(),
            isPlaying: false,
            index: 1,
            playlistCover: '',
            current: {
                title: '',
                artist: '',
                src: '',
                id: '',
                playlist: '',
            },
            songs: [],
            // songs: [
            //     {
            //         title: 'Title Track',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/title-track.mp3'),
            //         id: '1',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Kiss Kiss',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/kiss-kiss.mp3'),
            //         id: '2',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Drunk Face',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/drunk-face.mp3'),
            //         id: '3',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Bloody Valentine',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/bloody-valentine.mp3'),
            //         id: '4',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Forget me too',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/forget-me-too.mp3'),
            //         id: '5',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'All I Know',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/all-i-know.mp3'),
            //         id: '6',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Lonely',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/lonely.mp3'),
            //         id: '7',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'WWIII',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/WWIII.mp3'),
            //         id: '8',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Kevin & Barracuda',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/kevin-and-barracuda.mp3'),
            //         id: '9',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Concert For Aliens',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/concert-for-aliens.mp3'),
            //         id: '10',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: "My Ex's Best Friend",
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/my-exs-best-friend.mp3'),
            //         id: '11',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Jawbreaker',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/jawbreaker.mp3'),
            //         id: '12',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Nothing Inside',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/nothing-inside.mp3'),
            //         id: '13',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Banyan Tree',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/banyan-tree.mp3'),
            //         id: '14',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: "Play This When I'm Gone",
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/play-this-when-im-gone.mp3'),
            //         id: '15',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Body Bag',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/body-bag.mp3'),
            //         id: '16',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Hangover Cure',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/hangover-cure.mp3'),
            //         id: '17',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Split a Pill',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/split-a-pill.mp3'),
            //         id: '18',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: "Can't Look Back",
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/cant-look-back.mp3'),
            //         id: '19',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Misery Business',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/misery-business.mp3'),
            //         id: '20',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            //     {
            //         title: 'Bloody Valentine Acoustic',
            //         artist: 'Machine Gun Kelly',
            //         src: require('../assets/music/bloody-valentine-acoustic.mp3'),
            //         id: '21',
            //         playlist: 'Tickets To My Downfall'
            //     },    
            // ]
        }
    },
}
</script>

<style scoped>
    .music-app {
        font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
        font-size: 1.5em;
        background: center no-repeat;
        background-image: linear-gradient(to bottom, rgba(0, 0, 0, .25), rgba(0, 0, 0, .75)), url('../assets/music-bg.jpg');
        min-height: 100vh;
        background-size: cover;
        display: flex;
        justify-content: center;
    }
    .music-container {
        color: #eeeeee;
        width: 75vw;
        display: grid;
        grid-template-columns: 1fr 1.5fr;
        grid-column-gap: 30px;
        background: #fffff0;
        background-image: linear-gradient(to bottom, rgba(255, 41, 159, 0.4), rgba(255, 41, 159, 0.6));
        padding: 35px;
        border-radius: 10px;
        margin: 80px 0;
    }
    .music-container h2 {
        font-size: 28px;
    }
    .playlist-cover {
        position: relative;
    }
    .playlist-cover::after {
        content: '';
        position: absolute;
        left: 0;
        right: 0;
        top: 0;
        bottom: 0;
        background: rgba(0,0,0,.35);
        border-radius: 10px;
        z-index: 0;
    }
    .playlist-cover img {
        width: 100%;
        display: block;
        border-radius: 10px;
    }
    .playlist {
    }
    button {
        border: none;
        background: transparent;
        cursor: pointer;
        -webkit-tap-highlight-color: transparent;
    }
    .song {
        font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
        font-weight: 700;
        font-size: 18px;
        text-align: left;
        padding: 10px;
        border-radius: 5px;
        border: 1px solid transparent;
        border-radius: 20px 0px 20px 0px;
        display: block;
        position: relative;
    }
    .song-img {
        width: 25px;
        margin-right: 5px;
        display: inline-block;
    }
    .song-img img {
        width: 100%;
    }
    .song-playing {
        border: 1px solid #fff;
    }
    button:focus {
        outline: none;
    }
    .playlist-description {
        position: absolute;
        z-index: 1;
        left: 50%;
        transform: translate(-50%);
        top: 15%;
        text-align: center;
    }
    .song-artist {
        margin-bottom: 25px;
        font-size: 18px !important;
    }
    .control {
        display: flex;
        justify-content: space-around;
        background-color: #fcfcfc;
        padding: 10px 25px;
        border-radius: 0px 00px 10px 10px;
        z-index: 5;
        position: absolute;
        left: 0;
        right: 0;
        bottom: -50px;
        flex-wrap: wrap;
    }
    .control i {
        font-size: 35px;
        color: #ffa4bb;
    }
    .slider-container {
        flex-basis: 100%;
        justify-content: center;
        display: flex;
        margin-top: 15px;
    }
    .slider {
        height: 5px;
        width: 250px;
        border-radius: 15px;
        background: #555;
        margin: 0px 10px;
    }
    .slider-container i {
        font-size: 26px;
        margin-top: 3px;
        cursor: pointer;
    }
    .v-slider__thumb{
        cursor:grabbing;
        height:42px;
        width:42px;

    }
    .song-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }
    .song-slider {
        width: 300px;        
    }
    @media (min-width: 1281px) {
        .player-container, .playlist-cover {
            height: 50vh;
    }
        .playlist-cover img {
            height: 100%;
    }
    }
    @media (max-width: 1280px) {
        .music-container {
            grid-template-columns: 1fr;
        }
        .player-container {
            margin-bottom: 100px;
        }
    }
    @media (max-width: 900px) {
        .song-container {
            flex-direction: column;
            align-items: start;
        }
    }
    @media (max-width: 680px) {
        .player-container, .playlist-cover {
            height: 60vh;
        }
        .playlist-cover img {
            height: 100%;
        }
        .music-container {
            width: 100vh;
            margin: 0;
        }
        .playlist-description {
            top: 10%;
        }
    }
</style>