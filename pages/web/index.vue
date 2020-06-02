<template>
	<view>
		<web-view :webview-styles="webviewStyles" :src="src"></web-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				webviewStyles: {
					progress: {
						color: '#FF3333'
					}
				},
				src: ''
			}
		},
		onLoad: function(option) {
			var that = this
			var type = option.type
			switch(type) {
				case 'tv':
					that.src = 'https://tv.youku.com/'
					break
				case 'movie':
					that.src = 'https://movie.youku.com/'
					break
				case 'zy':
					that.src = 'https://zy.youku.com/'
					break
				case 'music':
					that.src = 'https://music.youku.com/'
					break
				case 'comic':
					that.src = 'https://comic.youku.com/'
					break
				case 'live':
					that.src = 'https://live.youku.com/'
					break
				default:
					that.src = 'https://www.youku.com/'
			}
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
