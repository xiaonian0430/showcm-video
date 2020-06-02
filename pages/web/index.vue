<template>
	<view>
		<view>
			<cu-custom :display="display" :isBack="isBack">
				<block slot="backText"> </block>
				<block slot="content" class="tarbar"></block>
			</cu-custom>
		</view>
		<web-view :webview-styles="webviewStyles" :src="src"></web-view>
	</view>
</template>

<script>
	import Vue from 'vue'
	export default {
		data() {
			return {
				webviewStyles: {
					progress: {
						color: '#FF3333'
					}
				},
				src: '',
				isBack: false,
				display: 'none'
			}
		},
		onLoad: function(option) {
			var that = this
			
			// #ifdef APP-PLUS
			var CustomBar = Vue.prototype.CustomBar
			that.isBack = true
			that.display = 'block'
			that.CustomBar = CustomBar
			var wv = ''
			var currentWebview = that.$mp.page.$getAppWebview() //获取当前页面的webview对象 
			setTimeout(function() {
				wv   = currentWebview.children()[0] 
				wv.setStyle({top:CustomBar}) 
			}, 1000); //如果是页面初始化调用时，需要延时一下 
			// #endif
			
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
