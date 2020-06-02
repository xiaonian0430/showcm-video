<template>
	<view class="detailBox">
		<!-- loading动画 -->
		<mi-loading ref="loading" title="加载中" :hasMask="true"></mi-loading>
		<view class="detailBgMax">
			<view class="detailBg" :style="{backgroundImage: 'url(' + viewBg + ')'}">></view>
		</view>
		<cu-custom :isBack="true">
			<block slot="backText"> </block>
			<block slot="content" class="tarbar">{{ viewTitle }}</block>
		</cu-custom>
		<scroll-view class="scrollBox">
			<view :class="summarStatu ? 'textMark' : ''">
				<view class="bigImgBox">
					<image :src="viewBanner.small" class="bigImg"></image>
				</view>
				<view class="bigtitleBox">
					<text v-if="summarStatu">{{ viewTitle }}</text>
					<br>
					<text class="textSty">{{ countries }}{{ genres }}{{ pubdates }}{{ durations }}</text>
				</view>
				<view class="summarybox">
					<text v-if="summarStatu">剧情简介: </text>
					<br>
					<view class="summary">
						<template v-if="showText">
							{{summary}}
							<text v-if="summary !== null && summary.length > 85" class="full_text" @click="toggleDescription"></text>
						</template>
						<template v-else>
							<text v-if="summarStatu">{{summary.substr(0, 85) + '... ' }}</text>
							<text v-if="summary !== null && summary.length > 85" class="full_text" @click="toggleDescription">全文</text>
						</template>

					</view>
				</view>
				<view class="summarybox">
					<text v-if="summarStatu">演职员: </text>
				</view>

				<view class="castsBox">
					<view v-for="(item, index) in directors" :key="item.id" class="castsBox-li">
						<view>
							<img :src="item.avatars.small" alt="导演">
							<text class="summaryColor">{{ item.name }}</text>
							<br>
							<text class="summaryVColor">导演</text>
						</view>
					</view>
					<view v-for="(item, index) in casts" :key="index" class="castsBox-li">
						<view>
							<img :src="item.avatars.small" alt="演员">
							<text class="summaryColor">{{ item.name }}</text>
							<br>
							<text class="summaryVColor">演员</text>
						</view>
					</view>
				</view>
				<!--        评论-->
				<view class="popular_comments" style="margin-top: 50rpx;">
					<text class="hTitle" v-if="summarStatu">精选评论</text>
					<view class="popular_comments-page" v-for="(item, index) in popular_comments" :key="index" style="width: 94%;margin: 14rpx auto;border-bottom: 1px solid rgba(204, 204, 204, .1);">
						<view class="commentsPage-t" style="display: flex; height: 100rpx;align-items: center">
							<img :src="item.author.avatar" alt="" style="width: 80rpx;height: 80rpx;border-radius: 40rpx;">
							<view style="display: flex;flex-direction: column;justify-content: space-around;height: 100rpx;">
								<text style="font-size: 30rpx;height: 30rpx;color: #fff;font-weight: 700;margin-left: 20rpx;">{{ item.author.name }}</text>
								<view style="margin-left: 20rpx;height: 20rpx;">
									<uni-rate disabled="true" :value="item.rating.value/2" size=14 active-color="#ffd21e" color="#DADADA">
									</uni-rate>
								</view>
							</view>
						</view>
						<view style="font-size: 30rpx;padding: 10rpx 0 10rpx 0;color: #fff;">
							{{ item.content }}
						</view>
						<view style="font-size: 30rpx;color: #b7acac;text-align: right;margin-bottom: 8rpx">{{ item.created_at }}</view>
					</view>
				</view>
			</view>
		</scroll-view>
		<!--        分割线-->
		<!-- #ifdef MP-WEIXIN || MP-BAIDU -->
		<view style="width: 98%;margin: 0 auto;" v-if="trailersState">
				<view  v-if="summarStatu" style="height: 100rpx;display: flex;align-items: center;color: #007AFF;z-index: 2;font-size: 40rpx;color: #EDE1D9;font-weight: 700;argin-left: 3%;;position: relative;">
					预告片 / 剧照
					<text></text>
				</view>
			<!-- <img src="http://img.zcool.cn/community/01e858594b2e58a8012193a3da10db.gif" alt="" style="width: 100%; height: 130rpx"> -->
			<view class="functionaryJob" style="height: 340rpx;display: flex;overflow: auto;">
				<view class="directors" style="display: flex;width: auto;	" v-for="item of trailers" :key="item.id">
					<view style="width:340rpx;height: 192rpx;position: relative; margin-right: 18rpx;">
						<video controls :poster='item.medium' :src="item.resource_url" style="width:340rpx;height: 192rpx;border-radius: 14rpx 14rpx 0 0;">
						</video>

						<view style="display: flex;align-items: center;justify-content: center;width: 340rpx;height: 80rpx;line-height: 40rpx;background-color: #222;color: #fff;position: absolute;bottom: -80rpx;font-size: 30rpx;border-radius: 0 0 14rpx 14rpx;text-align: center;">
							<text>{{ item.title }}</text>
						</view>
						</view>
					</view>
					<view class="directors" style="display: flex;width: auto;	" v-for="item of photos" :key="item.id">
						<view style="width:250rpx;height: 192rpx;position: relative; margin-right: 20rpx;">
							<image  :src="item.cover" style="width:250rpx;height: 272rpx;border-radius: 10rpx">
							</image>
					
							<!-- <view style="display: flex;align-items: center;justify-content: center;width: 340rpx;height: 80rpx;line-height: 40rpx;background-color: #222;color: #fff;position: absolute;bottom: -80rpx;font-size: 30rpx;border-radius: 0 0 14rpx 14rpx;text-align: center;">
								<text>剧照</text>
							</view> -->
							</view>
						</view>
					</view>
				</view>

				

			</view>

		</view>
		<!-- #endif -->
	</view>
</template>

<script>
	import miLoading from '@/components/mi-loading/mi-loading.vue' // loading动画
	export default {
		components: {
			miLoading
		},
		onShareAppMessage: function(res) {
			var that = this;
			return {
				title: 'showcm.top',
				path: 'pages/detail/index' ,
				success: function(res) {
					console.log("转发成功:" + JSON.stringify(res));
					that.shareClick();
				},
				fail: function(res) {
					console.log("转发失败:" + JSON.stringify(res));
				}
			}
		},
		onLoad: function(option) {
			setTimeout(() => {
				that.$refs.loading.show() // 打开
			}, 10)
			var that = this
			// 获取本地存储的图片
			uni.getStorage({
				key: 'storage_bg',
				success: function(res) {
					that.viewBg = res.data
				}
			});
			uni.getStorage({
				key: 'storage_h1',
				success: function(res) {
					that.viewTitle = res.data
				}
			});
			// 获取电影详情信息
			var id = option.id
			uni.request({
				url: 'https://douban.uieee.com/v2/movie/subject/' + id,
				method: 'GET',
				header: {
					"Content-Type": "application/text",
				},
				success: function(res) {
					that.$refs.loading.hide() // 关闭
					that.info = res.data
					// res.data = {"rating":{"max":10,"average":5,"details":{"1":39,"2":74,"3":64,"4":22,"5":12},"stars":"25","min":0},"reviews_count":56,"videos":[{"source":{"literal":"iqiyi","pic":"https://img9.doubanio.com\\/f\\/movie\\/7c9e516e02c6fe445b6559c0dd2a705e8b17d1c9\\/pics\\/movie\\/video-iqiyi.png","name":"\\u7231\\u5947\\u827a\\u89c6\\u9891"},"sample_link":"http:\\/\\/www.iqiyi.com\\/v_19rygmp54o.html?vfm=m_331_dbdy&fv=4904d94982104144a1548dd9040df241","video_id":"19rygmp54o","need_pay":true}],"wish_count":721,"original_title":"\\u6211\\u5011\\u6c38\\u4e0d\\u8a00\\u68c4","blooper_urls":[],"collect_count":2114,"images":{"small":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2595882406.jpg","large":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2595882406.jpg","medium":"https://img9.doubanio.com\\/view\\/photo\\/s_ratio_poster\\/public\\/p2595882406.jpg"},"douban_site":"","year":"2020","popular_comments":[{"rating":{"max":5,"value":1,"min":0},"useful_count":21,"author":{"uid":"phoenix43","avatar":"https://img9.doubanio.com\\/icon\\/u3771212-59.jpg","signature":"\\u90a3\\u4e00\\u5207\\u5e76\\u4e0d\\u5c5e\\u4e8e\\u4ed6\\u7684\\u53ea\\u80fd\\u662f\\u5012\\u5f71","alt":"https:\\/\\/www.douban.com\\/people\\/phoenix43\\/","id":"3771212","name":"\\u963f\\u6696"},"subject_id":"30466885","content":"\\u592a\\u70c2\\u4e86\\uff0c\\u4e24\\u4e2a\\u5c0f\\u65f6\\u7684\\u7535\\u5f71\\uff0c\\u62f3\\u51fb\\u4e0d\\u77e5\\u9053\\u6709\\u6ca1\\u6709\\u62cd\\u5230\\u534a\\u4e2a\\u5c0f\\u65f6\\uff0c\\u5176\\u4ed6\\u5168\\u90fd\\u662f\\u83ab\\u540d\\u5176\\u5999\\u6c34\\u5f97\\u4e0d\\u884c\\u7684\\u5e9f\\u620f\\u3002\\u5c0f\\u5973\\u5b69\\u5403\\u4e86\\u4e2a\\u8fa3\\u6912\\uff0c\\u7136\\u540e\\u53d1\\u73b0\\u81ea\\u5df1\\u5f97\\u4e86\\u6025\\u6027\\u767d\\u8840\\u75c5\\uff1f\\uff1f\\uff1f\\u8fd9\\u8c01\\u60f3\\u51fa\\u6765\\u7684\\u5267\\u672c\\uff1f\\uff1f\\uff1f","created_at":"2020-04-20 17:07:55","id":"2351528047"},{"rating":{"max":5,"value":1,"min":0},"useful_count":20,"author":{"uid":"208636290","avatar":"https://img9.doubanio.com\\/icon\\/u208636290-31.jpg","signature":"","alt":"https:\\/\\/www.douban.com\\/people\\/208636290\\/","id":"208636290","name":"\\u5c0f\\u53ee\\u5f53\\u5a03\\u5a03\\u673a"},"subject_id":"30466885","content":"\\u5c31\\u4e0d\\u80fd\\u597d\\u597d\\u6253\\u62f3\\u5417","created_at":"2020-04-20 10:27:44","id":"2343571048"},{"rating":{"max":5,"value":1,"min":0},"useful_count":7,"author":{"uid":"48953772","avatar":"https://img9.doubanio.com\\/icon\\/u48953772-16.jpg","signature":"\\u56de\\u5f52\\u6b63\\u5e38\\u4f5c\\u606f\\u7684\\u8d4b\\u95f2\\u4eba\\u5458","alt":"https:\\/\\/www.douban.com\\/people\\/48953772\\/","id":"48953772","name":"Xaviera"},"subject_id":"30466885","content":"\\u62cd\\u51fa\\u300a\\u5927\\u8ffd\\u6355\\u300b\\u548c\\u300a\\u7f6a\\u4e0e\\u7f5a\\u300b\\u7684\\u5468\\u663e\\u626c\\u592b\\u5987\\u7adf\\u7136\\u80fd\\u591f\\u62cd\\u51fa\\u8fd9\\u6837\\u4e00\\u90e8\\uff0c\\u5267\\u672c\\u6beb\\u65e0\\u903b\\u8f91\\u4e14\\u60c5\\u8282\\u9648\\u8150\\uff0c\\u5b8c\\u5168\\u9760\\u717d\\u60c5\\uff0c\\u91d1\\u94b1\\u89c2\\u81f3\\u4e0a\\uff0c\\u5e03\\u666f\\u6beb\\u65e0\\u751f\\u6d3b\\u611f\\uff0c\\u8fde\\u573a\\u9762\\u8c03\\u5ea6\\u90fd\\u6beb\\u65e0\\u60f3\\u6cd5\\u7684\\u5eb8\\u4f5c\\uff0c\\u5b9e\\u5728\\u4ee4\\u4eba\\u5927\\u8dcc\\u773c\\u955c\\u3002\\u97e9\\u5e9a\\u7684\\u6f14\\u6280\\u4ecd\\u7136\\u505c\\u7559\\u5728\\u201c\\u6211\\u8981\\u800d\\u5e05\\u201d\\u7684\\u9636\\u6bb5\\uff0c\\u4e1d\\u6beb\\u611f\\u89c9\\u4e0d\\u5230\\u8001\\u5a46\\u79bb\\u4e16\\uff0c\\u5973\\u513f\\u7edd\\u75c7\\u65f6\\u7684\\u771f\\u60c5\\u5b9e\\u611f\\uff0c\\u7528\\u7684\\u8fd8\\u662f\\u540c\\u4e00\\u79cd\\u9762\\u762b\\u6f14\\u6280\\u6765\\u6f14\\u7ece\\uff0c\\u8bf4\\u5b9e\\u8bdd\\uff0c\\u5982\\u679c\\u7537\\u4e3b\\u6362\\u6210\\u59dc\\u7693\\u6587\\uff0c\\u59dc\\u7693\\u6587\\u7684\\u89d2\\u8272\\u6362\\u97e9\\u5e9a\\u6f14\\uff0c\\u8fd9\\u7247\\u5b50\\u53ef\\u80fd\\u8fd8\\u6709\\u6551\\uff0c\\u4f46\\u53ef\\u60dc\\u4ece\\u5934\\u5230\\u5c3e\\u90fd\\u662f\\u69fd\\u70b9\\uff0c\\u5b9e\\u5728\\u592a\\u4ee4\\u4eba\\u5931\\u671b\\u4e86\\u3002","created_at":"2020-04-20 21:51:35","id":"2351891285"},{"rating":{"max":5,"value":2,"min":0},"useful_count":19,"author":{"uid":"54628981","avatar":"https://img9.doubanio.com\\/icon\\/u54628981-5.jpg","signature":"\\u7267\\u9a6c\\u4eba","alt":"https:\\/\\/www.douban.com\\/people\\/54628981\\/","id":"54628981","name":"NatureLB"},"subject_id":"30466885","content":"\\u597d\\u597d\\u6f14\\u620f\\uff0c\\u62f3\\u738b\\u4e0d\\u4e00\\u5b9a\\u9700\\u8981\\u72c2\\u5984\\u7684\\u3001\\u66f4\\u4e0d\\u9700\\u8981\\u90aa\\u9b45\\u3002\\u5982\\u679c\\u662f\\u4e2a\\u7eaf\\u52a8\\u4f5c\\u5546\\u4e1a\\u7247\\uff0c\\u88ab\\u6355\\u5165\\u72f1\\u8dbe\\u9ad8\\u6c14\\u6602\\u5f97\\u8d3c\\u72c2\\u6d6a\\u6ca1\\u95ee\\u9898\\uff0c\\u53ef\\u4f60\\u8fd9\\u4e00\\u4e2a\\u591a\\u949f\\u5934\\u6e29\\u541e\\u620f\\u7684\\u5c31\\u522b\\u4e86\\u5427\\u2026\\u2026","created_at":"2020-04-20 14:08:28","id":"2351303741"}],"alt":"https:\\/\\/movie.douban.com\\/subject\\/30466885\\/","id":"30466885","mobile_url":"https:\\/\\/movie.douban.com\\/subject\\/30466885\\/mobile","photos_count":103,"pubdate":"2020-04-20","title":"\\u6211\\u4eec\\u6c38\\u4e0d\\u8a00\\u5f03","do_count":null,"has_video":true,"share_url":"https:\\/\\/m.douban.com\\/movie\\/subject\\/30466885","seasons_count":null,"languages":["\\u6c49\\u8bed\\u666e\\u901a\\u8bdd"],"schedule_url":"","writers":[{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997657.52.jpg","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997657.52.jpg","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997657.52.jpg"},"name_en":"Roy Chow Hin-Yeung","name":"\\u5468\\u663e\\u626c","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1316512\\/","id":"1316512"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997715.22.jpg","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997715.22.jpg","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997715.22.jpg"},"name_en":"Chi-long To","name":"\\u675c\\u81f4\\u6717","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1275226\\/","id":"1275226"},{"avatars":null,"name_en":"","name":"\\u6587\\u8427","alt":null,"id":null},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1590779039.47.jpg","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1590779039.47.jpg","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1590779039.47.jpg"},"name_en":"Peijun Zou","name":"\\u90b9\\u4f69\\u541b","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1424999\\/","id":"1424999"}],"pubdates":["2020-04-20(\\u4e2d\\u56fd\\u5927\\u9646)"],"website":"","tags":["\\u52b1\\u5fd7","\\u62f3\\u51fb","\\u97e9\\u5e9a","\\u4eb2\\u60c5","\\u52a8\\u4f5c","\\u5267\\u60c5","\\u4f53\\u80b2","\\u4e2d\\u56fd","\\u5468\\u663e\\u626c","\\u5f20\\u94a7\\u871c"],"has_schedule":false,"durations":["120\\u5206\\u949f"],"genres":["\\u5267\\u60c5","\\u52a8\\u4f5c"],"collection":null,"trailers":[{"medium":"https://img9.doubanio.com\\/img\\/trailer\\/medium\\/2597118074.jpg?","title":"\\u9884\\u544a\\u7247 (\\u4e2d\\u6587\\u5b57\\u5e55)","subject_id":"30466885","alt":"https:\\/\\/movie.douban.com\\/trailer\\/260955\\/","small":"https://img9.doubanio.com\\/img\\/trailer\\/small\\/2597118074.jpg?","resource_url":"http:\\/\\/vt1.doubanio.com\\/202006021100\\/dd87d82ef54d4218f1bf99923cc1f878\\/view\\/movie\\/M\\/302600955.mp4","id":"260955"},{"medium":"https://img9.doubanio.com\\/img\\/trailer\\/medium\\/2595955246.jpg?","title":"\\u9884\\u544a\\u7247 (\\u4e2d\\u6587\\u5b57\\u5e55)","subject_id":"30466885","alt":"https:\\/\\/movie.douban.com\\/trailer\\/260825\\/","small":"https://img9.doubanio.com\\/img\\/trailer\\/small\\/2595955246.jpg?","resource_url":"http:\\/\\/vt1.doubanio.com\\/202006021100\\/534658b4727b7f7d5bd7b80701e5c856\\/view\\/movie\\/M\\/302600825.mp4","id":"260825"},{"medium":"https://img9.doubanio.com\\/img\\/trailer\\/medium\\/2551102155.jpg?","title":"\\u5148\\u884c\\u7248\\uff1a\\u524d\\u5bfc\\u9884\\u544a","subject_id":"30466885","alt":"https:\\/\\/movie.douban.com\\/trailer\\/244597\\/","small":"https://img9.doubanio.com\\/img\\/trailer\\/small\\/2551102155.jpg?","resource_url":"http:\\/\\/vt1.doubanio.com\\/202006021100\\/1bfe20ef79e6cc529dd6feca9003050a\\/view\\/movie\\/M\\/302440597.mp4","id":"244597"}],"episodes_count":null,"trailer_urls":["http:\\/\\/vt1.doubanio.com\\/202006021100\\/dd87d82ef54d4218f1bf99923cc1f878\\/view\\/movie\\/M\\/302600955.mp4","http:\\/\\/vt1.doubanio.com\\/202006021100\\/534658b4727b7f7d5bd7b80701e5c856\\/view\\/movie\\/M\\/302600825.mp4","http:\\/\\/vt1.doubanio.com\\/202006021100\\/1bfe20ef79e6cc529dd6feca9003050a\\/view\\/movie\\/M\\/302440597.mp4"],"has_ticket":false,"bloopers":[],"clip_urls":[],"current_season":null,"casts":[{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1519976356.71.jpg","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1519976356.71.jpg","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1519976356.71.jpg"},"name_en":"Geng Han","name":"\\u97e9\\u5e9a","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1275667\\/","id":"1275667"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p31577.jpg","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p31577.jpg","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p31577.jpg"},"name_en":"Vivian Wu","name":"\\u90ac\\u541b\\u6885","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1004773\\/","id":"1004773"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1537856155.35.jpg","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1537856155.35.jpg","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1537856155.35.jpg"},"name_en":"Elena Cai","name":"\\u8521\\u4e66\\u7075","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1401290\\/","id":"1401290"},{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1488637169.38.jpg","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1488637169.38.jpg","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1488637169.38.jpg"},"name_en":"Philip Keung Ho-Man","name":"\\u59dc\\u7693\\u6587","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1317884\\/","id":"1317884"}],"countries":["\\u4e2d\\u56fd\\u9999\\u6e2f"],"mainland_pubdate":"2020-04-20","photos":[{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2599014469.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2599014469.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2599014469.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2599014469\\/","id":"2599014469","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2599014469.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2599014468.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2599014468.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2599014468.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2599014468\\/","id":"2599014468","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2599014468.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2599014464.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2599014464.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2599014464.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2599014464\\/","id":"2599014464","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2599014464.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2599014463.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2599014463.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2599014463.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2599014463\\/","id":"2599014463","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2599014463.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2597866849.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2597866849.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2597866849.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2597866849\\/","id":"2597866849","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2597866849.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2597866848.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2597866848.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2597866848.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2597866848\\/","id":"2597866848","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2597866848.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2597866845.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2597866845.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2597866845.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2597866845\\/","id":"2597866845","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2597866845.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2597866844.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2597866844.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2597866844.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2597866844\\/","id":"2597866844","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2597866844.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2597866507.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2597866507.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2597866507.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2597866507\\/","id":"2597866507","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2597866507.jpg"},{"thumb":"https://img9.doubanio.com\\/view\\/photo\\/m\\/public\\/p2597866363.jpg","image":"https://img9.doubanio.com\\/view\\/photo\\/l\\/public\\/p2597866363.jpg","cover":"https://img9.doubanio.com\\/view\\/photo\\/sqs\\/public\\/p2597866363.jpg","alt":"https:\\/\\/movie.douban.com\\/photos\\/photo\\/2597866363\\/","id":"2597866363","icon":"https://img9.doubanio.com\\/view\\/photo\\/s\\/public\\/p2597866363.jpg"}],"summary":"\\u62f3\\u575b\\u4e0a\\u6240\\u5411\\u62ab\\u9761\\u7684\\u201c\\u6b7b\\u795e\\u201d\\u5468\\u59cb\\u56e0\\u4e00\\u6b21\\u610f\\u5916\\u88ab\\u5224\\u5165\\u72f1\\u4e94\\u5e74\\uff0c\\u51fa\\u72f1\\u540e\\u59bb\\u5b50\\u79bb\\u4e16\\uff0c\\u53ea\\u5269\\u5973\\u513f\\u5468\\u81ea\\u5728\\u7b49\\u4ed6\\u3002\\u5e74\\u5e7c\\u7684\\u5973\\u513f\\u88ab\\u53d1\\u73b0\\u60a3\\u4e0a\\u767d\\u8840\\u75c5\\uff0c\\u5468\\u59cb\\u4e3a\\u5b8c\\u6210\\u5973\\u513f\\u5fc3\\u613f\\uff0c\\u4ee536\\u5c81\\u9ad8\\u9f84\\u91cd\\u8fd4\\u64c2\\u53f0\\uff0c\\u4e0e\\u62f3\\u738b\\u5c55\\u5f00IBF\\u91d1\\u8170\\u5e26\\u4e89\\u593a\\u6218\\u3002\\u5373\\u4f7f\\u4f53\\u80fd\\u6d88\\u8017\\u6b86\\u5c3d\\u3001\\u5934\\u7834\\u8840\\u6d41\\u3001\\u89c6\\u7ebf\\u6a21\\u7cca\\u3001\\u8840\\u8089\\u98de\\u6e85\\uff0c\\u4ea6\\u4f1a\\u7ee7\\u7eed\\u6253\\u4e0b\\u53bb\\uff0c\\u76f4\\u81f3\\u5206\\u51fa\\u80dc\\u8d1f\\u2026","clips":[],"subtype":"movie","directors":[{"avatars":{"small":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997657.52.jpg","large":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997657.52.jpg","medium":"https://img9.doubanio.com\\/view\\/celebrity\\/s_ratio_celebrity\\/public\\/p1534997657.52.jpg"},"name_en":"Roy Chow Hin-Yeung","name":"\\u5468\\u663e\\u626c","alt":"https:\\/\\/movie.douban.com\\/celebrity\\/1316512\\/","id":"1316512"}],"comments_count":753,"popular_reviews":[{"rating":{"max":5,"value":2,"min":0},"title":"\\u4e0d\\u6ce1\\u599e\\u97e9\\u5e9a\\u7684\\u7535\\u5f71\\u5c31\\u4e0d\\u884c\\u4e86\\u5417\\uff1f","subject_id":"30466885","author":{"uid":"JerryVan","avatar":"https://img9.doubanio.com\\/icon\\/u49129096-5.jpg","signature":"\\u54fc\\u54fc\\u5527\\u5527","alt":"https:\\/\\/www.douban.com\\/people\\/JerryVan\\/","id":"49129096","name":"\\u589f\\u91cc\\u70df"},"summary":"\\u7531\\u4e8e\\u75ab\\u60c5\\u7684\\u6301\\u7eed\\uff0c\\u7ee7\\u300a\\u56e7\\u5988\\u300b\\u3001\\u300a\\u5927\\u8d62\\u5bb6\\u300b\\u4e4b\\u540e\\uff0c\\u53c8\\u4e00\\u90e8\\u9662\\u7ebf\\u7535\\u5f71\\u81ea\\u5e9f\\u6b66\\u529f\\u8f6c\\u6218\\u7f51\\u7edc\\uff0c\\u8fd1\\u65e5\\u97e9\\u5e9a\\u4e3b\\u6f14\\u65b0\\u7535\\u5f71\\u300a\\u6211\\u4eec\\u6c38\\u4e0d\\u8a00\\u5f03\\u300b\\u5728\\u7231\\u5947\\u827a\\u72ec\\u5bb6\\u64ad\\u51fa\\u3002\\u4e0d\\u540c\\u4e8e\\u957f\\u89c6\\u9891\\u5e73\\u53f0\\u884c\\u4e1a\\u7684\\u65b0\\u8fdb\\u548c\\u6405\\u5c40\\u8005\\u7684\\u897f\\u74dc\\u89c6\\u9891\\u4e3a\\u4e86\\u62a2\\u5360\\u5e02\\u573a\\u4efd\\u989d\\uff0c\\u4e0a\\u6765\\u5c31...","alt":"https:\\/\\/movie.douban.com\\/review\\/12531468\\/","id":"12531468"},{"rating":{"max":5,"value":4,"min":0},"title":"\\u7f8e\\u4e2d\\u4e0d\\u8db3\\u5374\\u6709\\u8bda\\u610f\\u7684\\u4e2d\\u56fd\\u7248\\u300a\\u94c1\\u62f3\\u300b\\uff08\\u4e00\\u4f4d3\\u5e74\\u62f3\\u51fb\\u7ec3\\u4e60\\u8005\\u548c\\u591a\\u5e74\\u52a8\\u4f5c\\u7247\\u5f71\\u8ff7\\u7684\\u89c2\\u540e\\u611f\\uff0c\\u6709\\u62f3\\u51fb\\u79d1\\u666e\\u6709\\u5267\\u900f\\uff09","subject_id":"30466885","author":{"uid":"190571086","avatar":"https://img9.doubanio.com\\/icon\\/u190571086-1.jpg","signature":"","alt":"https:\\/\\/www.douban.com\\/people\\/190571086\\/","id":"190571086","name":"LI"},"summary":"\\u8c8c\\u4f3c\\u662f\\u4eca\\u5929\\u624d\\u4e0a\\u6620\\u7684\\u7535\\u5f71\\uff0c\\u7531\\u4e8e\\u81ea\\u5df1\\u5728\\u4e1a\\u4f59\\u65f6\\u95f4\\u5e08\\u4ece\\u7701\\u961f\\u4e13\\u4e1a\\u62f3\\u51fb\\u961f\\u5458\\u7ec3\\u8fc7\\u51e0\\u5e74\\uff0c\\u4e5f\\u7b97\\u6709\\u4e2a\\u7968\\u53cb\\u6c34\\u5e73\\uff0c\\u6240\\u4ee5\\u4e00\\u5411\\u5bf9\\u52a8\\u4f5c\\u7535\\u5f71\\u5c24\\u5176\\u662f\\u53cd\\u6620\\u73b0\\u4ee3\\u640f\\u51fb\\u7684\\u7535\\u5f71\\uff0c\\u8fd8\\u662f\\u86ee\\u6311\\u7684\\uff0c6\\u5757\\u94b1\\u7684\\u7231\\u5947\\u827a\\u89c2\\u5f71\\u8d39\\u5012\\u6ca1\\u4ec0\\u4e48\\uff0c\\u5c31\\u662f\\u6015\\u82b1\\u4e86\\u94b1\\u53c8\\u770b\\u4e86\\u4e00\\u90e8...","alt":"https:\\/\\/movie.douban.com\\/review\\/12527422\\/","id":"12527422"},{"rating":{"max":5,"value":1,"min":0},"title":"\\u4e3a\\u4ec0\\u4e48\\u6ca1\\u6709\\u5931\\u5fc6\\uff1f\\uff1f\\uff1f\\u4e0d\\u53ca\\u683c","subject_id":"30466885","author":{"uid":"Popdai","avatar":"https://img9.doubanio.com\\/icon\\/u104034484-2.jpg","signature":"\\u770b\\u5c3d\\u5929\\u4e0b\\u70c2\\u7247","alt":"https:\\/\\/www.douban.com\\/people\\/Popdai\\/","id":"104034484","name":"502\\u91cd\\u88c5\\u7532\\u8425"},"summary":"\\u8f66\\u7978\\u764c\\u75c7\\u5931\\u5fc6\\u8fd9\\u4e09\\u5927\\u5b9a\\u5f8b\\uff0c\\u8001\\u5a46\\u8f66\\u7978\\uff0c\\u5973\\u513f\\u7edd\\u75c7\\uff0c\\u591a\\u7ecf\\u5178\\u554a\\uff0c\\u4f60\\u4e3a\\u5565\\u4e0d\\u5931\\u5fc6\\uff1f\\u4f60\\u6253\\u67b6\\u8fdb\\u76d1\\u72f1\\u4e00\\u70b9\\u6ca1\\u6709\\u8868\\u73b0\\u51fa\\u4e3b\\u4eba\\u516c\\u7684\\u60e8\\uff0c\\u77e5\\u9053\\u5417\\uff1f\\u4e3a\\u4e86\\u6551\\u5973\\u513f\\uff0c\\u5e94\\u8be5\\u6253\\u9ed1\\u62f3\\u6253\\u5230\\u5931\\u5fc6\\uff0c\\u6700\\u540e\\u4e00\\u573a\\u88ab\\u6253\\u5f97\\u4e34\\u6b7b\\u524d\\u7a81\\u7136\\u60f3\\u8d77\\u5973\\u513f\\uff0c\\u6700\\u540e\\u5916\\u5a46\\u5728\\u4e09\\u4eba\\u90fd...","alt":"https:\\/\\/movie.douban.com\\/review\\/12546533\\/","id":"12546533"},{"rating":{"max":5,"value":1,"min":0},"title":"\\u5267\\u60c5\\u592a\\u8001\\u5957\\u4e86\\uff0c\\u6ca1\\u6709\\u521b\\u65b0\\uff0c\\u4e0d\\u597d\\u770b\\u3002","subject_id":"30466885","author":{"uid":"169468903","avatar":"https://img9.doubanio.com\\/icon\\/u169468903-3.jpg","signature":"","alt":"https:\\/\\/www.douban.com\\/people\\/169468903\\/","id":"169468903","name":"law_whr"},"summary":"\\u65e2\\u7136\\u5305\\u542b\\u62f3\\u51fb\\u9898\\u6750\\uff0c\\u62cd\\u6444\\u7f16\\u5267\\u771f\\u7684\\u61c2\\u62f3\\u51fb\\u6216\\u683c\\u6597\\u5417\\uff1f\\u62cd\\u62f3\\u51fb\\u7247\\u4e0d\\u4e00\\u5b9a\\u975e\\u5f97\\u8981\\u53bb\\u5750\\u4e00\\u6b21\\u7262\\uff0c\\u5931\\u53bb\\u4eb2\\u4eba\\u540e\\u5e61\\u7136\\u9192\\u609f\\uff0c\\u5267\\u60c5\\u592a\\u8001\\u5957\\u4e86\\uff0c\\u5927\\u591a\\u6570\\u7ec3\\u62f3\\u6253\\u6bd4\\u8d5b\\u8981\\u4e0d\\u662f\\u4e3a\\u4e86\\u517b\\u5bb6\\u7cca\\u53e3\\uff0c\\u5c31\\u662f\\u4e3a\\u4e86\\u7406\\u60f3\\u5728\\u575a\\u6301\\u3002\\u8fd9\\u65b9\\u9762\\u53ef\\u4ee5\\u53bb\\u54a8\\u8be2\\u54a8\\u8be2\\u5f20\\u4f1f\\u4e3d\\u3001...","alt":"https:\\/\\/movie.douban.com\\/review\\/12527345\\/","id":"12527345"},{"rating":{"max":5,"value":5,"min":0},"title":"\\u201c\\u4ece\\u7231\\u8c46\\u5230\\u6f14\\u5458\\u201d\\u97e9\\u5e9a\\u65b0\\u5267\\u5f71\\u8bc4","subject_id":"30466885","author":{"uid":"216826888","avatar":"https://img9.doubanio.com\\/icon\\/u216826888-1.jpg","signature":"","alt":"https:\\/\\/www.douban.com\\/people\\/216826888\\/","id":"216826888","name":"."},"summary":"\\u521a\\u521a\\u770b\\u5b8c\\u8fd9\\u90e8\\u7535\\u5f71\\uff0c\\u611f\\u89c9\\u770b\\u5b8c\\u8fd9\\u90e8\\u7535\\u5f71\\u540e\\u5fc3\\u91cc\\u5f88\\u6c89\\u91cd\\uff0c\\u8fd9\\u90e8\\u620f\\u6f14\\u5458\\u9009\\u7684\\u7279\\u522b\\u597d\\uff0c\\u97e9\\u5e9a\\u7684\\u6f14\\u6280\\u662f\\u5927\\u5bb6\\u6709\\u76ee\\u5171\\u7779\\u7684\\uff0c\\u50cf18\\u5e74\\u97e9\\u5e9a\\u4e3b\\u6f14\\u7684\\u7231\\u60c5\\u7535\\u5f71\\u300a\\u524d\\u4efb3\\u518d\\u89c1\\u524d\\u4efb\\u300b\\uff0c\\u4ed6\\u6f14\\u51fa\\u4e86\\u5bf9\\u5973\\u53cb\\u7684\\u7231\\u4e0e\\u4e0d\\u820d\\u548c\\u5bf9\\u8fd9\\u6bb5\\u611f\\u60c5\\u7684\\u7559\\u604b\\uff0c\\u603b\\u80fd\\u6233\\u4e2d\\u6211\\u7684...","alt":"https:\\/\\/movie.douban.com\\/review\\/12606308\\/","id":"12606308"},{"rating":{"max":5,"value":2,"min":0},"title":"\\u6c38\\u4e0d\\u8a00\\u5f03\\uff0c\\u6807\\u9898\\u8fc7\\u5927\\uff0c\\u5185\\u5bb9\\u592a\\u8f7b","subject_id":"30466885","author":{"uid":"189743772","avatar":"https://img9.doubanio.com\\/icon\\/u189743772-1.jpg","signature":"","alt":"https:\\/\\/www.douban.com\\/people\\/189743772\\/","id":"189743772","name":"\\u601d\\u5ff5\\u6211\\u7684\\u4eba\\u513f"},"summary":"\\u5f00\\u5934\\u662f\\u4e0d\\u9519\\u7684\\uff0c\\u7b80\\u5355\\u4ea4\\u4ee3\\u5468\\u59cb\\u7684\\u62f3\\u738b\\u7ecf\\u5386\\uff0c\\u66fe\\u7ecf\\u5341\\u5206\\u8f89\\u714c\\uff0c\\u51fa\\u72f1\\u540e\\u7684\\u60c5\\u51b5\\u4e0d\\u5bb9\\u4e50\\u89c2\\uff0c\\u4e2d\\u6bb5\\u7684\\u7410\\u788e\\u5c0f\\u4e8b\\u5b9e\\u5728\\u592a\\u590d\\u6742\\uff0c\\u5bf9\\u4e8e\\u63a8\\u52a8\\u5267\\u60c5\\u7684\\u5e2e\\u52a9\\u592a\\u5c0f\\uff0c\\u5468\\u59cb\\u7684\\u56f0\\u96be\\u5168\\u7529\\u7ed9\\u4e86\\u9001\\u5916\\u5356\\u8fd9\\u4efd\\u804c\\u4e1a\\uff0c\\u4ee5\\u53ca\\u5973\\u513f\\u5916\\u5a46\\u7684\\u8003\\u9a8c\\uff0c\\u4f9d\\u7136\\u5e26\\u5973\\u513f\\u5728\\u62f3\\u9986\\u5de5...","alt":"https:\\/\\/movie.douban.com\\/review\\/12566359\\/","id":"12566359"},{"rating":{"max":5,"value":0,"min":0},"title":"\\u5728\\u8fd9\\u91cc\\u803b\\u7b11\\u8089\\u53d4\\u7535\\u5f71\\u8fd9\\u4e2a\\u53f7","subject_id":"30466885","author":{"uid":"143160247","avatar":"https://img9.doubanio.com\\/icon\\/user_normal.jpg","signature":"","alt":"https:\\/\\/www.douban.com\\/people\\/143160247\\/","id":"143160247","name":"mad dog"},"summary":"\\u4e0a\\u4e2a\\u6708\\u770b\\u5230\\u8fd9\\u4e2a\\u53f7\\u63a8\\u8350\\uff0c\\u6211\\u7559\\u8a00\\u6240\\u8c46\\u74e3\\u4e0d\\u4f1a\\u8fc7\\u4e94\\u5206\\uff0c\\u56e0\\u4e3a\\u8fd9\\u4e2a\\u53f7\\u63a8\\u8350\\u7535\\u5f71\\u8e29\\u96f7\\u673a\\u7387\\u6781\\u5927\\uff0c\\u8fd9\\u4e2a\\u4e0d\\u662f\\u91cd\\u70b9\\u91cd\\u70b9\\u662f\\u4f60\\u6536\\u94b1\\u505a\\u5ba3\\u4f20\\u4e5f\\u4e0d\\u8981\\u592a\\u8089\\u9ebb\\uff0c\\u54ce\\u5927\\u5bb6\\u81ea\\u5df1\\u770b\\u5427 \\u8fd9\\u90e8\\u7247\\uff0c\\u6bcf\\u4e00\\u62f3\\u90fd\\u6253\\u4e2d\\u4f60\\u6cea\\u817a \\u539f\\u521b \\u8089\\u53d4  \\u8089\\u53d4\\u7535\\u5f71  1\\u5468\\u524d \\u5168\\u8c01\\u4e5f...","alt":"https:\\/\\/movie.douban.com\\/review\\/12563093\\/","id":"12563093"},{"rating":{"max":5,"value":0,"min":0},"title":"\\u80fd\\u5403\\u5f97\\u4e0b\\u62f3\\u5934\\u7684\\u4eba\\uff0c\\u624d\\u6709\\u673a\\u4f1a\\u6210\\u4e3a\\u5f3a\\u8005","subject_id":"30466885","author":{"uid":"192674765","avatar":"https://img9.doubanio.com\\/icon\\/u192674765-2.jpg","signature":"","alt":"https:\\/\\/www.douban.com\\/people\\/192674765\\/","id":"192674765","name":"\\u7bab\\u7bab\\u68a7\\u53f6"},"summary":"\\u97e9\\u5e9a\\u7684\\u8fd9\\u90e8\\u300a\\u6211\\u4eec\\u6c38\\u4e0d\\u8a00\\u5f03\\u300b\\uff0c\\u6309\\u7c7b\\u522b\\u6765\\u7b97\\uff0c\\u7b97\\u52a8\\u4f5c\\u52b1\\u5fd7\\u7247\\u3002 \\u6545\\u4e8b\\u7684\\u4e3b\\u7ebf\\u8bb2\\u8ff0\\u4e86\\u7537\\u4e3b\\u5468\\u59cb\\u521a\\u767b\\u4e0a\\u4eba\\u751f\\u7684\\u5dc5\\u5cf0\\uff0c\\u6210\\u4e3aIBF\\u7684\\u4e2d\\u91cf\\u7ea7\\u7684\\u62f3\\u738b\\u4e4b\\u540e\\uff0c\\u53c8\\u8dcc\\u843d\\u8c37\\u5e95\\u7684\\u6545\\u4e8b\\u3002 \\u62f3\\u738b\\u5468\\u59cb\\u5148\\u662f\\u56e0\\u6253\\u67b6\\u88ab\\u5224\\u5165\\u72f1\\uff0c\\u516d\\u5e74\\u540e\\u51fa\\u72f1\\u53c8\\u5f97\\u77e5\\u59bb\\u5b50\\u56e0\\u8f66\\u7978...","alt":"https:\\/\\/movie.douban.com\\/review\\/12562351\\/","id":"12562351"},{"rating":{"max":5,"value":4,"min":0},"title":"\\u62ab\\u7740\\u62f3\\u51fb\\u5916\\u8863\\u7684\\u7236\\u5973\\u4eb2\\u60c5\\u7535\\u5f71\\uff0c\\u6709\\u70ed\\u8840\\u6709\\u5171\\u60c5","subject_id":"30466885","author":{"uid":"raineetian","avatar":"https://img9.doubanio.com\\/icon\\/u84339620-2.jpg","signature":"","alt":"https:\\/\\/www.douban.com\\/people\\/raineetian\\/","id":"84339620","name":"\\u6885\\u6735"},"summary":"\\u4f1a\\u5458\\u5230\\u671f\\u4e86\\uff0c\\u82b1\\u4e8612\\u5757\\u770b\\u7684\\uff0c2\\u4e2a\\u5c0f\\u65f6\\u770b\\u5b8c\\u8fd8\\u610f\\u72b9\\u672a\\u5c3d\\uff0c\\u4e1d\\u6beb\\u6ca1\\u89c9\\u5f97\\u65f6\\u957f\\u8fc7\\u957f\\u3002\\u53ea\\u662f\\u53ef\\u60dc\\u7535\\u5f71\\u56e0\\u4e3a\\u75ab\\u60c5\\u7684\\u5173\\u7cfb\\u4e0d\\u80fd\\u8fdb\\u7535\\u5f71\\u9662\\uff0c\\u6f14\\u5458\\u3001\\u5bfc\\u6f14\\u548c\\u4e3b\\u521b\\u4eec\\u5e94\\u8be5\\u90fd\\u4f1a\\u9057\\u61be\\u5427\\u3002\\u4e0e\\u5176\\u8bf4\\u8fd9\\u662f\\u4e00\\u90e8\\u62f3\\u51fb\\u4e3b\\u9898\\u7535\\u5f71\\uff0c\\u4e0d\\u5982\\u8bf4\\u5b83\\u662f\\u4e00\\u90e8\\u62ab\\u7740\\u62f3\\u51fb\\u5916\\u5957...","alt":"https:\\/\\/movie.douban.com\\/review\\/12543126\\/","id":"12543126"},{"rating":{"max":5,"value":1,"min":0},"title":"\\u8fd9\\u56fd\\u4ea7\\u300c\\u88f8\\u300d\\u7247\\u70c2\\u6210\\u8fd9\\u6837\\uff0c\\u8fd8\\u597d\\u610f\\u601d\\u6536\\u94b1\\uff1f","subject_id":"30466885","author":{"uid":"154504197","avatar":"https://img9.doubanio.com\\/icon\\/u154504197-1.jpg","signature":"\\u6700\\u706b\\u7684\\u7535\\u5f71\\u7535\\u89c6\\u7efc\\u827a\\uff0c\\u5728\\u8fd9\\u91cc","alt":"https:\\/\\/www.douban.com\\/people\\/154504197\\/","id":"154504197","name":"\\u7535\\u5f71\\u5934\\u6761"},"summary":"\\u7279\\u6b8a\\u65f6\\u671f\\uff0c\\u90fd\\u4e0d\\u5bb9\\u6613\\u3002 \\u53cd\\u5012\\u662f\\u7f51\\u7edc\\uff0c\\u628a\\u5927\\u5bb6\\u7a7a\\u524d\\u56e2\\u7ed3\\u5728\\u4e00\\u8d77\\u3002 \\u4e0d\\u5c11\\u5f71\\u7247\\u4e5f\\u4ece\\u8ba1\\u5212\\u4e2d\\u7684\\u9662\\u7ebf\\u8f6c\\u6295\\u7f51\\u7edc\\u4e0a\\u6620\\u3002 \\u6bd4\\u5982\\u4e4b\\u524d\\u7684\\u300a\\u80a5\\u9f99\\u8fc7\\u6c5f\\u300b\\u2014\\u2014 \\u628a\\u5c4e\\u5c3f\\u5c41\\u548c\\u7740\\u8001\\u6897\\u4e0b\\u9505\\uff0c\\u7092\\u51fa\\u4e00\\u76c6\\u998a\\u996d\\u3002 \\u300a\\u80a5\\u9f99\\u8fc7\\u6c5f\\u300b\\u5267\\u7167 \\u65e0\\u72ec\\u6709\\u5076\\u3002 \\u6700\\u8fd1\\u53c8\\u4e00\\u90e8...","alt":"https:\\/\\/movie.douban.com\\/review\\/12547793\\/","id":"12547793"}],"ratings_count":1940,"aka":["Knockout"]}
					that.viewBanner = res.data.images //电影封面
					that.genres = res.data.genres[0] + ' / ' //电影类型
					that.countries = res.data.countries[0] + ' / ' // 上映国家
					that.pubdates = (res.data.pubdates[0] ? res.data.pubdates[0] : res.data.pubdates) + ' / ' // 上映时间
					that.durations = '片长: ' + res.data.durations[0] ? res.data.durations[0] : res.data.durations // 片长
					that.summary = res.data.summary // 剧情简介
					that.directors = res.data.directors // 导演
					that.casts = res.data.casts // 演员
					// #ifdef MP-WEIXIN || MP-BAIDU 
					that.photos = res.data.photos // 剧照
					that.trailers = res.data.trailers // 预告片
					if(that.trailers.length < 1) {
						that.trailersState = false
					}
					// #endif
					that.popular_comments = res.data.popular_comments // 评论

					that.summarStatu = true
					if (res.data.videos[0]) {
						that.isVideosLook = true
						that.movieUrl = res.data.videos[0].sample_link
					} else {
						that.isVideosLook = false
					}
				}
			})
		},
		data() {
			return {
				info: {}, // 电影信息
				genres: '', // 电影类型
				countries: '', // 上映国家
				pubdates: '', // 上映时间
				durations: '', // 片长
				viewBg: '', // 背景img
				viewTitle: '', // 电影title
				viewBanner: {}, //电影封面
				summary: '', // 剧情简介
				casts: [], // 演员
				directors: [], // 导演
				summarStatu: false, // 剧情简介是否显示
				movieUrl: '', // 电影视频
				trailers: [], // 预告片
				photos: [], // 剧照
				trailersState: true, //预告片状态
				popular_comments: [], // 精选评论
				isVideosLook: false, // 是否可以观看
				showText: false, // 展开收起
			}
		},
		methods: {
			toggleDescription(num) {
				this.showText = !this.showText
			}
		},
	}
</script>

<style scoped>
	/* 文字遮罩 */
	.textMark {
		margin-top: 20rpx;
		border-radius: 80rpx 80rpx 14rpx 14rpx;
		background-color: rgba(0, 0, 0, .2);
	}

	.detailBox {
		width: 100%;
		height: 100vh;
		position: relative;
	}

	.detailBgMax {
		position: fixed;
		overflow: hidden;
		width: 100%;
		height: 100vh;
		top: 0;
		left: 0;
		z-index: 0;
	}

	.detailBg {
		width: 100%;
		height: 100vh;
		background-size: 100% 100%;
		filter: blur(60rpx);
		transform: scale(1.2);
		transition: 0.3s;
	}

	/* 导航tab */
	.tarbar {
		font-weight: 700 !important;
		color: #fff !important;
	}

	/* 大图 */
	.bigImgBox {
		height: 700rpx;
		/* background: pink; */
		transition: 1s;
		text-align: center;
	}

	/* 滚动视图窗口 */
	.scrollBox {}

	/* 图片从上至下动画 */
	@keyframes mymove {
		0% {
			margin-top: -200px
		}

		100% {
			margin-top: 50px
		}
	}

	.bigImg {
		width: 440rpx;
		height: 600rpx;
		margin-top: 50px;
		border-radius: 10rpx;
		animation: mymove 0.8s cubic-bezier(.41, -0.6, .68, .81) 1;
		/* -moz-box-shadow: 1px 1px 1px #dddddd;
		-webkit-box-shadow: 1px 1px 1px #dddddd;
		box-shadow: 1px 1px 1px #dddddd; */
	}

	.bigtitleBox {
		width: 90%;
		margin: 0 auto;
		text-align: center;
		line-height: 80rpx;
		font-weight: 700;
		color: #F7F7F7;
	}

	.textSty {
		width: 90%;
		margin: 0 auto;
	}

	/* 剧情简介 */
	.summarybox {
		width: 90%;
		margin: 40rpx auto 0;
		line-height: 60rpx;
		font-size: 40rpx;
		color: #EDE1D9;
		font-weight: 700;
	}

	/* 标题设置 */
	.hTitle {
		font-size: 40rpx;
		color: #EDE1D9;
		font-weight: 700;
		margin-left: 3%;
	}
	/* 演职员设置 */
	.castsBox {
		width: 90%;
		display: flex;
		margin: 30rpx auto 0;
		overflow: auto;
	}

	.castsBox-li {
		margin-right: 30rpx;
		text-align: center;
		;
	}

	.castsBox-li img {
		width: 200rpx;
		height: 260rpx;
		border-radius: 10rpx;
	}

	.summary {
		color: #fff;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 40rpx;
		margin-top: 20rpx;
		/* max-height: 160upx; */
	}

	.summaryColor {
		color: #D3BBA7;
	}

	.summaryVColor {
		color: #fff;
		font-weight: 700;
	}

	/* 视频设置 */
	@-webkit-keyframes masked-animation {
		0% {
			background-position: 0 0
		}

		to {
			background-position: -100% 0
		}
	}

	.videoUrl {
		margin-top: 30rpx;
	}

	.play {
		color: #FFFFFF;
		font-size: 50rpx;
		font-weight: 700;
		line-height: 140rpx;
		text-align: center;
		background-image: -webkit-linear-gradient(left, #02ab6c, #DD524D 25%, #3499cd 50%, #DD524D 75%, #3499cd);
		-webkit-text-fill-color: transparent;
		-webkit-background-clip: text;
		-webkit-background-size: 200% 100%;
		-webkit-animation: masked-animation 1s infinite linear;
	}
</style>
