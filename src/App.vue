<template>
	<div id="app">
		<audio id="app__audio" ref="audio" @timeupdate='onTimeUpdateListener' preload="metadata">
			<source :src="activeTrack.trackPath" id="app__audio_src" type="audio/mpeg">
			Your browser does not support the audio element.
		</audio>

		<!-- <img :src="activeTrack.trackCover" class="app__bg" alt=""> -->
		<div class="app__wrapper">
			<!-- <div class="tracklist" @click="toggleTracklist()">
				<h3>Tracklist</h3>
				<ul>
					<li v-for="track in trackList" :key="track.id" @click="setTrackId(track.id)"
						:class=" { 'active' : track.id == activeTrackId} ">{{track.name}}</li>
				</ul>
			</div> -->
			<div class="app__lector">
				<!-- <img class="app__cover" :src="activeTrack.trackCover" alt=""> -->
				<div class="app__now-play">
					<div class="app__status">
						{{this.status}}
					</div>
					<div class="app__name">
						<h2 class="app__name_title">{{activeTrack.title}}</h2>
						<span class="app__name_author">
							{{activeTrack.author}}<span v-if="activeTrack.featuring.length"> ft.
								{{activeTrack.featuring.join(",")}}</span>
						</span>
					</div>
				</div>
				<div class="app__control">

					<button class="app__restart" @click="repeatTrack()">Restart</button>

					<span class="pause-play-button">
						<button v-if="isPlaying" @click="setPause">Pause</button>
						<button v-else @click="setPlay">Play</button>
					</span>

					<!-- <div class="app__control_buttons">
						<i class="gg-play-list" @click="toggleTracklist()"></i>
						<i class="gg-play-track-prev" @click="setTrack('prev')"></i>

						<i class="gg-play-track-next" @click="setTrack('next')"></i>
						<i class="gg-heart" @click="activeTrack.isLiked = !activeTrack.isLiked"
							:style="activeTrack.isLiked ? 'color: #e37d7d' : 'opacity: 0.15'"></i>
					</div> -->

					<div class="app__control_timer">
						<div class="app__control_timer__display">
							<span class="app__control_timer__progress">{{activeTrackCurrentTime | formatTime}}</span>
							<span class="app__control_timer__end">{{activeTrackDuration | formatTime}}</span>
						</div>
						<div class="app__control_timer__progressbar">
							<input type="range" min="0" :max="activeTrackDuration" :value="activeTrackCurrentTime" step='1'
								@change="setCurrentTime()" class="app__control_timer__slider">
							<span class="progressbar__bg">
								<span class="progressbar__state"
									:style=" 'width : calc((' + activeTrackCurrentTime / activeTrackDuration + ') * 100%)' "></span>
							</span>
						</div>
					</div>

					<div class="app__volume-container">
						<button @click="muteTrack()">Mute</button>
						<input class="range_input" type="range" min="0" max="100" step="5" value="50" ref="volRange"
							@input="volRange()">
					</div>

					<button class="app__download" @click="download()">Save</button>
					<!-- <a href="activeTrack.trackPath" download="filename.mp3">DownloadButton</a> -->

				</div>
			</div>
		</div>
	</div>
</template>

<script>
export default {
	name: 'App',
	data() {
		return {
			trackList: [
				{
					id: 12345,
					name: "Kaash Paige - Love Songs ft 6LACK Remix",
					author: "Kaash Paige",
					featuring: ["6LACK"],
					title: "Love songs - Remix",
					isLiked: false,
					trackPath: "https://hamza-k.github.io/music-player-app/songs/tracks/track1.mp3",
					trackCover: "https://hamza-k.github.io/music-player-app/songs/covers/cover1.jpg"
				},
				{
					id: 34567,
					name: "Masego - Queen Tings Ft Tiffany Gouché",
					author: "Masego",
					featuring: ["Tiffany Gouché"],
					title: "Queen tings",
					isLiked: false,
					trackPath: "https://hamza-k.github.io/music-player-app/songs/tracks/track2.mp3",
					trackCover: "https://hamza-k.github.io/music-player-app/songs/covers/cover2.jpg"
				},
				{
					id: 36431,
					name: "The Weeknd - Material girl",
					author: "The Weeknd",
					featuring: [],
					title: "Material girl",
					isLiked: false,
					trackPath: "https://hamza-k.github.io/music-player-app/songs/tracks/track3.mp3",
					trackCover: "https://hamza-k.github.io/music-player-app/songs/covers/cover3.png"
				},
				{
					id: 64686,
					name: "Khalid - Suncity ft Empress Of",
					author: "Khalid",
					featuring: ["Empress Of"],
					title: "Suncity",
					isLiked: false,
					trackPath: "https://hamza-k.github.io/music-player-app/songs/tracks/track4.mp3",
					trackCover: "https://hamza-k.github.io/music-player-app/songs/covers/cover4.jpg"
				}
			],
			playlist: [12345, 34567, 36431, 64686],
			activeTrackId: 34567,
			isPlaying: false,
			isMuted: false,
			volume: .5,
			status: 'Paused...',
			activeTrackDuration: 0,
			activeTrackCurrentTime: 0,
			isTracklistOpen: false
		}
	},
	computed: {
		activeTrack: function () {
			return this.trackList.find(el => el.id == this.activeTrackId)
		}
	},
	methods: {
		setPlay: function () {
			document.getElementById('app__audio').play()
			this.isPlaying = true
			this.status = 'Play'
		},
		setPause: function () {
			document.getElementById('app__audio').pause()
			this.isPlaying = false
			this.status = 'Paused...'
		},
		setTrackDuration: function () {
			let globalThis = this
			document.getElementById('app__audio').addEventListener('loadedmetadata', function () {
				globalThis.activeTrackDuration = Math.round(this.duration)
			}, false)
		},
		setTrackId: function (el) {
			document.getElementById('app__audio').pause()
			this.activeTrackId = el
			document.getElementById('app__audio').load()
			document.getElementById('app__audio').play()
			this.isPlaying = true
			this.setTrackDuration()
		},
		repeatTrack: function () {
			document.getElementById('app__audio').load()
			document.getElementById('app__audio').play()
			this.isPlaying = true
		},
		setTrack: function (move) {
			document.getElementById('app__audio').pause()
			let trackIdIndex = this.playlist.findIndex(el => el == this.activeTrackId)
			let totalTrack = this.playlist.length
			if (move == "prev") {
				if (this.activeTrackCurrentTime <= 2) {
					if (trackIdIndex == 0) {
						trackIdIndex = totalTrack - 1
					} else {
						trackIdIndex--
					}
				} else {
					this.activeTrackCurrentTime = 0
				}
			} else if (move == "next") {
				if (trackIdIndex == totalTrack - 1) {
					trackIdIndex = 0
				} else {
					trackIdIndex++
				}
			}
			this.activeTrackId = this.playlist[trackIdIndex]
			document.getElementById('app__audio').load()
			document.getElementById('app__audio').play()
			this.isPlaying = true
		},
		onTimeUpdateListener: function () {
			this.activeTrackCurrentTime = this.$refs.audio.currentTime
		},
		setCurrentTime: function () {
			let globalThis = this
			document.querySelector('.app__control_timer__slider').addEventListener('change', function () {
				globalThis.$refs.audio.currentTime = this.value
			}, false)
		},
		// toggleTracklist: function () {
		// 	this.isTracklistOpen = !this.isTracklistOpen
		// }
		download() {
			this.isPlaying = false;
			this.$refs.audio.pause();
			this.$refs.audio.currentTime = 0;
			window.open(this.activeTrack.trackPath, 'download');
		},
		muteTrack() {
			this.isMuted = !this.isMuted;
			this.$refs.audio.volume = this.isMuted ? 0 : this.volume;
		},
		volRange() {
			const volRange = this.$refs.volRange;
			if (!volRange) return;
			const vol = volRange.value;
			volRange.setAttribute('value', vol);
			this.volume = vol / 100;
			if (this.isMuted) return;
			this.$refs.audio.volume = this.volume;
		},
	},
	filters: {
		formatTime: function (el) {
			let minutes = Math.floor(el / 60)
			let seconds = Math.floor(el % 60)
			return `${minutes}:${seconds > 9 ? seconds : '0' + seconds}`
		}
	},
	watch: {
		activeTrackCurrentTime: function (val) {
			if (val == this.activeTrackDuration) {
				this.setTrack('next')
			}
		}
	},
	mounted() {
		this.setTrackId(this.activeTrackId)
		this.isPlaying = false
	}

}
</script>

<style lang="scss" scoped>
@import './style.scss'
</style>
