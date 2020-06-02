<template>
	<view class="container">
		<!-- loading动画 -->
		<mi-loading ref="loading" title="加载中" :hasMask="true"></mi-loading>
		<scroll-view>
			<view class="topBar">
				<image src="~@/static/img/banner.png" mode="widthFix" class="response"></image>
				<view class="searchBox" @click="getSearch">
					<view class="search-t">请输入关键字搜索</view>
					<view class="button">Go
						<view class="t"></view>
					</view>
				</view>
			</view>
			<!-- 轮播图 -->
			<swiper class="card-swiper" :class="dotStyle?'square-dot':'round-dot'" :indicator-dots="true" :circular="true"
			 :autoplay="true" interval="5000" duration="500" @change="cardSwiper" indicator-color="#8799a3"
			 indicator-active-color="#0081ff">
				<swiper-item v-for="(item,index) in swiperList" :key="index" :class="cardCur==index?'cur':''" @click="getBanner(item)">
					<view class="swiper-item">
						<image :src="item.url" mode="aspectFill" v-if="item.type=='image'"></image>
						<video :src="item.url" autoplay loop muted :show-play-btn="false" :controls="false" objectFit="cover" v-if="item.type=='video'"></video>
					</view>
				</swiper-item>
			</swiper>

			<!-- 宫格列表 -->
			<view class="cu-list grid" :class="['col-3','border']">
				<view class="cu-item" v-for="(item,index) in cuIconList" :key="index" @click="getWeb(item)">
					<view :class="['cuIcon-' + item.cuIcon,'text-' + item.color]">
						<view class="cu-tag badge" v-if="item.badge!=0">
							<block v-if="item.badge!=1">{{item.badge>99?'99+':item.badge}}</block>
						</view>
					</view>
					<text>{{item.name}}</text>
				</view>
			</view>
			
			<!-- 电影列表 -->
			<view class="movieH">豆瓣高分</view>
			<view class="movieBox">
				<view v-for="(item, index) in movieInfo" :key="index" class="movieList" @click="getDate(item)">
					<image :src="item.images.small" class="movieImg"></image>
					<view>
						{{ item.title | ellipsis }}
						<view class="moiveRate">
							<uni-rate disabled="true" :value="item.rating.average/2" size=14 active-color="#D81E06" color="#DADADA">
							</uni-rate>
							<text class="moiveRateT">{{item.rating.average}}</text>
						</view>
					</view>
				</view>
			</view>
			<uni-load-more :status="listStatus" :contentText="contentText" v-if="loadStatu"></uni-load-more>
		</scroll-view>
	</view>
</template>

<script>
	import uniList from "@/components/uni-list/uni-list.vue" // 宫格列表
	import uniListItem from "@/components/uni-list-item/uni-list-item.vue"
	import uniLoadMore from "@/components/uni-load-more/uni-load-more.vue" // 加载更多
	import miLoading from '@/components/mi-loading/mi-loading.vue' // loading动画
	export default {
		components: {
			uniList,
			uniListItem,
			uniLoadMore,
			miLoading
		},
		filters: {
			// 名称超出显示省略号
			ellipsis(value) {
				if (!value) return '';
				if (value.length > 7) {
					return value.slice(0, 6) + '...'
				}
				return value
			}
		},
		onShareAppMessage: function(res) {
			var that = this;
			return {
				title: 'showcm.top',
				path: 'pages/index/index' ,
				success: function(res) {
					console.log("转发成功:" + JSON.stringify(res));
					that.shareClick();
				},
				fail: function(res) {
					console.log("转发失败:" + JSON.stringify(res));
				}
			}
		},
		onReachBottom: function() { // 页面触底触发
			this.getMoreNews()
		},
		mounted() {
			setTimeout(function(){
				this.$refs.loading.show() // 打开
			}, 10)
			uni.request({
				url: 'https://api.douban.com/v2/movie/top250',
				header: {
					"Content-Type": "application/text",
				},
				data: {
					// type: 'movie',
					// tag: '喜剧',
					sort: 'recommend',
					count: 6,
					start: 0,
					playable: 'on',
					apikey: '0df993c66c0c636e29ecbb5344252a4a'
				},
				success: (res) => {
					this.$refs.loading.hide() // 关闭
					// res.data = {"count":6,"start":0,"total":250,"subjects":[{"rating":{"max":10,"average":9.7,"details":{"1":1576,"2":1281,"3":21081,"4":216453,"5":1375460},"stars":"50","min":0},"genres":["\\u72af\\u7f6a","\\u5267\\u60c5"],"title":"\\u8096\\u7533\\u514b\\u7684\\u6551\\u8d4e","casts":[{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p17525.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p17525.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p17525.webp"},"name_en":"Tim Robbins","name":"\\u8482\\u59c6\\u00b7\\u7f57\\u5bbe\\u65af","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1054521\\/","id":"1054521"},{"avatars":{"small":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p34642.webp","large":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p34642.webp","medium":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p34642.webp"},"name_en":"Morgan Freeman","name":"\\u6469\\u6839\\u00b7\\u5f17\\u91cc\\u66fc","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1054534\\/","id":"1054534"},{"avatars":{"small":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p5837.webp","large":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p5837.webp","medium":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p5837.webp"},"name_en":"Bob Gunton","name":"\\u9c8d\\u52c3\\u00b7\\u5188\\u987f","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1041179\\/","id":"1041179"}],"durations":["142\\u5206\\u949f"],"collect_count":2940440,"mainland_pubdate":"","has_video":true,"original_title":"The Shawshank Redemption","subtype":"movie","directors":[{"avatars":{"small":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p230.webp","large":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p230.webp","medium":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p230.webp"},"name_en":"Frank Darabont","name":"\\u5f17\\u5170\\u514b\\u00b7\\u5fb7\\u62c9\\u90a6\\u7279","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1047973\\/","id":"1047973"}],"pubdates":["1994-09-10(\\u591a\\u4f26\\u591a\\u7535\\u5f71\\u8282)","1994-10-14(\\u7f8e\\u56fd)"],"year":"1994","images":{"small":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p480747492.webp","large":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p480747492.webp","medium":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p480747492.webp"},"alt":"https:\\/\\/movie.douban.com\\/subject\\/1292052\\/","id":"1292052"},{"rating":{"max":10,"average":9.6,"details":{"1":1624,"2":1806,"3":25047,"4":197889,"5":1014510},"stars":"50","min":0},"genres":["\\u5267\\u60c5","\\u7231\\u60c5","\\u540c\\u6027"],"title":"\\u9738\\u738b\\u522b\\u59ec","casts":[{"avatars":{"small":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p67.webp","large":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p67.webp","medium":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p67.webp"},"name_en":"Leslie Cheung","name":"\\u5f20\\u56fd\\u8363","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1003494\\/","id":"1003494"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1391771959.66.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1391771959.66.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1391771959.66.webp"},"name_en":"Fengyi Zhang","name":"\\u5f20\\u4e30\\u6bc5","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1050265\\/","id":"1050265"},{"avatars":{"small":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1399268395.47.webp","large":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1399268395.47.webp","medium":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1399268395.47.webp"},"name_en":"Li Gong","name":"\\u5de9\\u4fd0","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1035641\\/","id":"1035641"}],"durations":["171\\u5206\\u949f","155\\u5206\\u949f(\\u7f8e\\u56fd\\u5267\\u573a\\u7248)"],"collect_count":2302921,"mainland_pubdate":"1993-07-26","has_video":true,"original_title":"\\u9738\\u738b\\u522b\\u59ec","subtype":"movie","directors":[{"avatars":{"small":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1451727734.81.webp","large":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1451727734.81.webp","medium":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1451727734.81.webp"},"name_en":"Kaige Chen","name":"\\u9648\\u51ef\\u6b4c","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1023040\\/","id":"1023040"}],"pubdates":["1993-01-01(\\u4e2d\\u56fd\\u9999\\u6e2f)","1993-07-26(\\u4e2d\\u56fd\\u5927\\u9646)"],"year":"1993","images":{"small":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2561716440.webp","large":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2561716440.webp","medium":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2561716440.webp"},"alt":"https:\\/\\/movie.douban.com\\/subject\\/1291546\\/","id":"1291546"},{"rating":{"max":10,"average":9.5,"details":{"1":1191,"2":2172,"3":33979,"4":260340,"5":973942},"stars":"50","min":0},"genres":["\\u5267\\u60c5","\\u7231\\u60c5"],"title":"\\u963f\\u7518\\u6b63\\u4f20","casts":[{"avatars":{"small":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p28603.webp","large":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p28603.webp","medium":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p28603.webp"},"name_en":"Tom Hanks","name":"\\u6c64\\u59c6\\u00b7\\u6c49\\u514b\\u65af","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1054450\\/","id":"1054450"},{"avatars":{"small":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1539139684.97.webp","large":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1539139684.97.webp","medium":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1539139684.97.webp"},"name_en":"Robin Wright","name":"\\u7f57\\u5bbe\\u00b7\\u6000\\u7279","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1002676\\/","id":"1002676"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26315.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26315.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26315.webp"},"name_en":"Gary Sinise","name":"\\u52a0\\u91cc\\u00b7\\u897f\\u5c3c\\u65af","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1031848\\/","id":"1031848"}],"durations":["142\\u5206\\u949f"],"collect_count":2493517,"mainland_pubdate":"","has_video":true,"original_title":"Forrest Gump","subtype":"movie","directors":[{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p505.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p505.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p505.webp"},"name_en":"Robert Zemeckis","name":"\\u7f57\\u4f2f\\u7279\\u00b7\\u6cfd\\u7c73\\u5409\\u65af","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1053564\\/","id":"1053564"}],"pubdates":["1994-06-23(\\u6d1b\\u6749\\u77f6\\u9996\\u6620)","1994-07-06(\\u7f8e\\u56fd)"],"year":"1994","images":{"small":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p1484728154.webp","large":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p1484728154.webp","medium":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p1484728154.webp"},"alt":"https:\\/\\/movie.douban.com\\/subject\\/1292720\\/","id":"1292720"},{"rating":{"max":10,"average":9.4,"details":{"1":1273,"2":2622,"3":44463,"4":322578,"5":1053160},"stars":"50","min":0},"genres":["\\u5267\\u60c5","\\u52a8\\u4f5c","\\u72af\\u7f6a"],"title":"\\u8fd9\\u4e2a\\u6740\\u624b\\u4e0d\\u592a\\u51b7","casts":[{"avatars":{"small":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p8833.webp","large":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p8833.webp","medium":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p8833.webp"},"name_en":"Jean Reno","name":"\\u8ba9\\u00b7\\u96f7\\u8bfa","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1025182\\/","id":"1025182"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p2274.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p2274.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p2274.webp"},"name_en":"Natalie Portman","name":"\\u5a1c\\u5854\\u8389\\u00b7\\u6ce2\\u7279\\u66fc","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1054454\\/","id":"1054454"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33896.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33896.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33896.webp"},"name_en":"Gary Oldman","name":"\\u52a0\\u91cc\\u00b7\\u5965\\u5fb7\\u66fc","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1010507\\/","id":"1010507"}],"durations":["110\\u5206\\u949f(\\u5267\\u573a\\u7248)","133\\u5206\\u949f(\\u56fd\\u9645\\u7248)"],"collect_count":2845626,"mainland_pubdate":"","has_video":false,"original_title":"L\\u00e9on","subtype":"movie","directors":[{"avatars":{"small":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33301.webp","large":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33301.webp","medium":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33301.webp"},"name_en":"Luc Besson","name":"\\u5415\\u514b\\u00b7\\u8d1d\\u677e","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1031876\\/","id":"1031876"}],"pubdates":["1994-09-14(\\u6cd5\\u56fd)"],"year":"1994","images":{"small":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p511118051.webp","large":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p511118051.webp","medium":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p511118051.webp"},"alt":"https:\\/\\/movie.douban.com\\/subject\\/1295644\\/","id":"1295644"},{"rating":{"max":10,"average":9.5,"details":{"1":861,"2":1459,"3":18060,"4":141833,"5":649543},"stars":"50","min":0},"genres":["\\u5267\\u60c5","\\u559c\\u5267","\\u7231\\u60c5"],"title":"\\u7f8e\\u4e3d\\u4eba\\u751f","casts":[{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26764.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26764.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26764.webp"},"name_en":"Roberto Benigni","name":"\\u7f57\\u4f2f\\u6258\\u00b7\\u8d1d\\u5c3c\\u5c3c","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1041004\\/","id":"1041004"},{"avatars":{"small":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p9548.webp","large":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p9548.webp","medium":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p9548.webp"},"name_en":"Nicoletta Braschi","name":"\\u5c3c\\u53ef\\u83b1\\u5854\\u00b7\\u5e03\\u62c9\\u65af\\u57fa","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1000375\\/","id":"1000375"},{"avatars":{"small":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p45590.webp","large":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p45590.webp","medium":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p45590.webp"},"name_en":"Giorgio Cantarini","name":"\\u4e54\\u6cbb\\u00b7\\u574e\\u5854\\u91cc\\u5c3c","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1000368\\/","id":"1000368"}],"durations":["116\\u5206\\u949f","125\\u5206\\u949f(\\u621b\\u7eb3\\u7535\\u5f71\\u8282)"],"collect_count":1407349,"mainland_pubdate":"2020-01-03","has_video":true,"original_title":"La vita \\u00e8 bella","subtype":"movie","directors":[{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26764.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26764.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p26764.webp"},"name_en":"Roberto Benigni","name":"\\u7f57\\u4f2f\\u6258\\u00b7\\u8d1d\\u5c3c\\u5c3c","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1041004\\/","id":"1041004"}],"pubdates":["1997-12-20(\\u610f\\u5927\\u5229)","2020-01-03(\\u4e2d\\u56fd\\u5927\\u9646)"],"year":"1997","images":{"small":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2578474613.webp","large":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2578474613.webp","medium":"https://img3.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2578474613.webp"},"alt":"https:\\/\\/movie.douban.com\\/subject\\/1292063\\/","id":"1292063"},{"rating":{"max":10,"average":9.4,"details":{"1":1000,"2":2459,"3":42922,"4":274444,"5":924953},"stars":"50","min":0},"genres":["\\u5267\\u60c5","\\u7231\\u60c5","\\u707e\\u96be"],"title":"\\u6cf0\\u5766\\u5c3c\\u514b\\u53f7","casts":[{"avatars":{"small":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p470.webp","large":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p470.webp","medium":"https://img3.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p470.webp"},"name_en":"Leonardo DiCaprio","name":"\\u83b1\\u6602\\u7eb3\\u591a\\u00b7\\u8fea\\u5361\\u666e\\u91cc\\u5965","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1041029\\/","id":"1041029"},{"avatars":{"small":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p53358.webp","large":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p53358.webp","medium":"https://img1.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p53358.webp"},"name_en":"Kate Winslet","name":"\\u51ef\\u7279\\u00b7\\u6e29\\u4e1d\\u83b1\\u7279","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1054446\\/","id":"1054446"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p45186.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p45186.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p45186.webp"},"name_en":"Billy Zane","name":"\\u6bd4\\u5229\\u00b7\\u8d5e\\u6069","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1031864\\/","id":"1031864"}],"durations":["194\\u5206\\u949f","227\\u5206\\u949f(\\u767d\\u661f\\u7248)"],"collect_count":2467810,"mainland_pubdate":"1998-04-03","has_video":true,"original_title":"Titanic","subtype":"movie","directors":[{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33715.webp","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33715.webp","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p33715.webp"},"name_en":"James Cameron","name":"\\u8a79\\u59c6\\u65af\\u00b7\\u5361\\u6885\\u9686","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1022571\\/","id":"1022571"}],"pubdates":["1997-11-01(\\u4e1c\\u4eac\\u7535\\u5f71\\u8282)","1997-12-19(\\u7f8e\\u56fd)","1998-04-03(\\u4e2d\\u56fd\\u5927\\u9646)"],"year":"1997","images":{"small":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p457760035.webp","large":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p457760035.webp","medium":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p457760035.webp"},"alt":"https:\\/\\/movie.douban.com\\/subject\\/1292722\\/","id":"1292722"}],"title":"\\u8c46\\u74e3\\u7535\\u5f71Top250"}
					this.movieInfo = res.data.subjects
				}
			})
		},
		data() {
			return {
				contentText: {
					contentdown: "加载更多",
					contentrefresh: "正在加载...",
					contentnomore: "我是有底线的"
				},
				cardCur: 0, // 轮播图初始页面
				pageNum: 1, // 电影列表初始页数
				movieInfo: [], // 电影列表
				loadStatu: false, // loading是否显示
				listStatus: 'loading', // loading状态
				swiperList: [{ // 轮播图
					id: 30466885,
					type: 'image',
					url: '../../static/img/videoList/1.jpg',
					title: '我们永不言弃'
				}, {
					id: 30176393,
					type: 'image',
					url: '../../static/img/videoList/2.jpg',
					title: '误杀'
				}, {
					id: 27668250,
					type: 'image',
					url: '../../static/img/videoList/3.jpg',
					title: '南方车站的聚会'
				}, {
					id: 30198715,
					type: 'image',
					url: '../../static/img/videoList/4.jpg',
					title: '吹哨人'
				}],
				dotStyle: false, // 轮播图样式
				current: 0,
				mode: 'round',
				cuIconList: [{ // 宫格列表
					cuIcon: 'videofill',
					color: 'red',
					badge: 0,
					type: 'tv',
					name: '电视剧'
				}, {
					cuIcon: 'recordfill',
					color: 'orange',
					badge: 0,
					type: 'movie',
					name: '电影'
				}, {
					cuIcon: 'playfill',
					color: 'yellow',
					badge: 0,
					type: 'zy',
					name: '综艺'
				}, {
					cuIcon: 'musicfill',
					color: 'olive',
					badge: 0,
					type: 'music',
					name: '音乐'
				}, {
					cuIcon: 'picfill',
					color: 'cyan',
					badge: 0,
					type: 'comic',
					name: '动漫'
				}, {
					cuIcon: 'friendfill',
					color: 'blue',
					badge: 0,
					type: 'live',
					name: '直播'
				}]
			}
		},
		methods: {
			// 轮播图事件
			cardSwiper(e) {
				this.cardCur = e.detail.current
			},
			// 触底之后触发函数，
			getMoreNews() {
				var that = this
				that.loadStatu = true
				if (that.movieInfo.length > 89) {
					that.listStatus = 'noMore'
					return
				}
				that.listStatus = 'loading'
				uni.request({
					url: 'https://douban.uieee.com/v2/movie/top250',
					method: 'GET',
					header: {
						"Content-Type": "application/text",
						"Cookie": 'bid=gmauOdTjPuM; ll="108296"; __utma=30149280.183190915.1588736614.1588736614.1588736614.1; __utmc=30149280; __utmz=30149280.1588736614.1.1.utmcsr=google|utmccn=(organic)|utmcmd=organic|utmctr=(not%20provided); __utma=223695111.1019919151.1588736616.1588736616.1588736616.1; __utmc=223695111; __utmz=223695111.1588736616.1.1.utmcsr=douban.com|utmccn=(referral)|utmcmd=referral|utmcct=/; _pk_ref.100001.4cf6=%5B%22%22%2C%22%22%2C1588736616%2C%22https%3A%2F%2Fwww.douban.com%2F%22%5D; _vwo_uuid_v2=DF41C6A193D708C4010E7A2D6F958507C|41887b5e2aa3fee81b7580e887d7e604; __yadk_uid=CaK93hfK60gv0yih8eYhMiiMaicvymVR; _pk_id.100001.4cf6=eb02d84691b41619.1588736616.1.1588736620.1588736616.; __gads=ID=5f256365a15a3bd0:T=1588736620:S=ALNI_MaoShIvp1GQXf-3uNudDn-YkC1ylA; dbcl2="199894991:L6mq4GWyH2I"; ck=C3EE'

					},
					data: {
						// type: 'movie',
						// tag: '喜剧',
						sort: 'recommend',
						count: 6,
						start: that.pageNum * 6,
						playable: 'on'
					},
					success: function(res) {
						that.pageNum++;
						that.movieInfo = that.movieInfo.concat(res.data.subjects);
						that.listStatus = 'loading'
					}
				})
			},
			getBanner(item) {
				uni.setStorage({
						key: 'storage_bg',
						data: 'https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2559577569.jpg',
						success: function() {}
					}),
					uni.setStorage({
						key: 'storage_h1',
						data: item.title,
						success: function() {
							console.log('success');
						}
					})
				uni.navigateTo({
					url: '../detail/index?id=' + item.id,
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			getSearch() {
				uni.navigateTo({
					url: '../search/index',
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			getDate(item) {
				uni.setStorage({
					key: 'storage_bg',
					data: item.images.small,
					success: function() {
						console.log('item.images.small');
					}
				})
				uni.setStorage({
					key: 'storage_h1',
					data: item.title,
					success: function() {
						console.log('success');
					}
				})
				uni.navigateTo({
					url: '../detail/index?id=' + item.id,
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			getWeb(item) {
				uni.navigateTo({
					url: '../web/index?type=' + item.type,
					animationType: 'pop-in',
					animationDuration: 200
				})
			}
		},
	}
</script>

<style scoped>
	/* 顶背景图 */
	.response {
		width: 100%;
	}

	.topBar {
		position: relative;
	}

	/* 搜索框 */
	.searchBox {
		position: absolute;
		left: 50%;
		margin-left: -30%;
		bottom: 10rpx;
		width: 60%;
		height: 60rpx;
		line-height: 60rpx;
		text-align: center;
		color: #3499cd;
		background-color: #ddd;
		border-radius: 10rpx;
		overflow: hidden;
		display: flex;
	}

	.button {
		width: 20%;
		position: relative;
		color: #FFFFFF;
		background-color: #6C7CFD;
	}

	.search-t {
		line-height: 60rpx;
		flex: 1;
	}

	.t {
		border-width: 12rpx;
		border-style: solid;
		border-color: transparent #6C7CFD transparent transparent;
		position: absolute;
		right: 100%;
		top: 18rpx;
	}

	/* 轮播图 */
	.swiper-item {
		width: 80%;
		;
		height: 100%;
		margin: 0 auto;
		border-radius: 20upx 20upx 0 0;
	}

	.swiper-img {
		width: 100%;
		height: 100%;
		border-radius: 20upx 20upx 0 0;
	}

	/* 电影列表 */
	@-webkit-keyframes masked-animation {
		0% {
			background-position: 0 0
		}

		to {
			background-position: -100% 0
		}
	}

	.movieH {
		width: 94%;
		margin: 0 auto;
		height: 100rpx;
		line-height: 100rpx;
		font-weight: 900;
		color: #DD524D;
		font-size: 36rpx;
		background-image: -webkit-linear-gradient(left, #02ab6c, #DD524D 25%, #3499cd 50%, #DD524D 75%, #3499cd);
		-webkit-text-fill-color: transparent;
		-webkit-background-clip: text;
		-webkit-background-size: 200% 100%;
		-webkit-animation: masked-animation 1s infinite linear;
	}

	.movieBox {
		width: 94%;
		margin: 0 auto;
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
	}

	.movieList {
		/* height: 300rpx; */
		width: 30%;
	}

	.movieImg {
		height: 300rpx;
		border-radius: 15rpx;
	}

	.moiveRate {
		height: 40rpx;
		line-height: 40rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 15rpx;
	}

	.moiveRateT {
		margin-right: 4rpx;
	}
</style>
