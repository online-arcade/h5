<template>
	<view class="content">
		<view class="header">
			<view class="group">
				<view class="box">
					<image class="photo" :src="url" @click="handleModel(1)"></image>

				</view>

				<view class="group money">
					<image src="../../static/coin.png" style="transform: translateX(-25%);"></image>
					<span> {{user.balance}} </span>

					<image class="icon" src="../../static/add.png" style="transform: translateX(25%);"
						@click="handleRecharge(1)"></image>
				</view>
				<image class="" src="../../static/start.png" style="z-index: 2;">
				</image>
				<span class="start-bar"> {{user.integral}}
				</span>
			</view>


			<view class="group">

				<span @click="handleGetData(1)" class="group-flex">
					<image class="" src="../../static/chat.png" class="chat-icon"></image>
					<span>聊天</span>
				</span>
				<span style="width: 10px;"></span>
				<span @click="handleSet(1)" class="group-flex">
					<image class="" src="../../static/set.png"></image>
					<span>设置</span>
				</span>

			</view>

		</view>

		<view :class="{'main':true,'main-pad':!show}">

			<uni-transition custom-class="transition" :mode-class="modeClass" :show="show" :class="{'row':!series }">
				<view :class="{'col':true,'col-wid1':true}" v-for="(item,index) of game">
					<view class="box">
						<view class="box-content" @click="toggle('center',item.type)">
							<image :src="'../../static/'+item.icon" style=" height: 100%;">
							</image>
							<span class="num">{{item.desc}}</span>

							<span class="font" style="font-size: 45px;color: yellow;">games</span>
							<span class="font">{{item.name}}</span>
						</view>
					</view>
				</view>

			</uni-transition>
			<uni-popup ref="popup" :is-mask-click="false">
				<view class="modal">
					<view class="modal-title">{{modal.title}}</view>
					<view class="modal-content">
						<view v-for="(item,index) of modal.content">
							({{index+1}}) {{item}}
						</view>

					</view>
					<view class="modal-foot">

						<!-- <view class="modal-foot-btn " @click="handleOk()">
							接受
						</view> -->
						<!-- 	<text style="position: absolute;color: black">

							<uni-countdown :show-day="false" :second="5"   />
						</text> -->
						<uni-load-more iconType="snow" status="loading"></uni-load-more>
						<!-- <image class=" progress" src="../../static/loading.png"> -->
						<!-- <view class="modal-foot-btn red" @click="handleClose">
								拒绝</view> -->

					</view>
				</view>
			</uni-popup>

			<uni-popup ref="report">
				<view class="report">
					<view class="report-title">{{report.title}}</view>
					<view class="report-content">
						<view>
							{{report.msg}}
						</view>

						<view v-for="(item,index) of report.content">
							({{index+1}}) {{item}}
						</view>

					</view>
					<view class="report-foot">
						<view class="report-foot-btn " @click="handleReportOk()">
							确认
						</view>



					</view>
				</view>
			</uni-popup>
			<!-- <image src="../../static/return.png" v-show="!show" @click=" show = ! show" class="returnIcon">
			</image> -->

			<!-- 	<uni-transition custom-class="transition" :mode-class="modeClass" :show="!show" :class="{'row':!series }">

				<view :class="{'col':true,'col-wid2':true}" v-for="(item,dex) of box">
					<view class="box">
						<view class="box-content" @click="goto(item.type)">
							<image src="../../static/gamebox.jpg" style=" height: 100%;">
							</image>
							<span class="num">{{item.desc}}</span>
							<span class="font">游戏机{{dex+1}}</span>
						</view>
					</view>
				</view>

				<view v-if="!box"
					style="color: rgb(255,244,38) ;font-size: 30px;display: flex;justify-content: center;align-items: center;width: 100%;height: 100%;">
					请上传游戏数据！</view>

			</uni-transition> -->



		</view>

		<showToast class="showToast" :msg="msg" v-show="showToast"></showToast>

		<!-- <view class="loading" v-show="loading">
			<image src="../../static/load.png" style="width: 20%; " mode="widthFix"></image>
			<image class="progress" src="../../static/loading.png">
			</image>
			<span> 注意 : 若累计游戏时间如超过5小时 ，游戏内的收益（经验、金钱）直接为0</span>
		</view> -->




		<!-- <view v-show="mask" class="showModel"> </view> -->
		<!-- v-show="chatShow" -->
		<!-- <chat class="chat" v-show="true" @receiveData="handleGetData(0)"></chat> -->
		<uni-transition custom-class="transition" :mode-class="modeClass" :show="mask" class="showModel">
			<chat class="chat" v-show="chatShow" @receiveData="handleGetData(0)"></chat>
			<setting class="chat" v-show="setShow" @receiveData="handleSet(0)"></setting>
			<userInfo class="model" v-show="modelShow" :user="user" @receiveData="handleModel(0)"></userInfo>
			<recharge class="chat" v-show="rechargeShow" :user="user" @receiveData="handleRecharge(0)"></recharge>

		</uni-transition>




	</view>
</template>

<script>
	import screenfull from "screenfull";
	// import Peer from 'peerjs';
	export default {

		data() {
			return {
				showToast: false,
				msg: {
					name: '提交成功!'
				},
				time: '',
				gotoGame: '',
				url: '1',
				token: '',
				user: {},
				mask: false,
				showAll: [false, true],
				dialog: false,
				timer: null,
				loading: true,
				index: 0,
				series: false,
				chatShow: false,
				modelShow: false,
				rechargeShow: false,
				activityShow: false,
				setShow: false,
				modeClass: ['fade', 'slide-left'],
				show: true,
				row1: [],
				row2: [],
				data: [{
						url: '../../static/game1.jpg',
						name: '系列1',
						mess: '4人在玩',
						series: "test"
					},
					{
						url: '../../static/game2.jpg',
						name: '系列2',
						mess: '4人在玩',
						series: "test"
					},
					{
						url: '../../static/game3.jpg',
						name: '系列3',
						mess: '4人在玩',
						series: "test"
					},
					{
						url: '../../static/game4.jpg',
						name: '系列4',
						mess: '4人在玩',
						series: "demo"
					},
					{
						url: '../../static/game5.jpg',
						name: '系列5',
						mess: '4人在玩',
						series: "demo"
					},
					{
						url: '../../static/game6.jpg',
						name: '系列6',
						mess: '4人在玩',
						series: "demo"
					},
					{
						url: '../../static/game6.jpg',
						name: '系列7',
						mess: '4人在玩',
						series: "demo"
					},


					{
						url: '../../static/game4.jpg',
						name: '系列8',
						mess: '4人在玩',
						series: "demo"
					},
					{
						url: '../../static/game5.jpg',
						name: '系列9',
						mess: '4人在玩',
						series: "demo"
					},

					{
						url: '../../static/game4.jpg',
						name: '系列10',
						mess: '4人在玩',
						series: "demo"
					},
					{
						url: '../../static/game5.jpg',
						name: '系列11',
						mess: '4人在玩',
						series: "demo"
					},
				],
				modal: {
					title: "游戏须知",
					content: ['机台1币=200分，开始游戏即自动投10币=2000分;',
						'系统总分上限100万分，超过上限后的投币与打中boss的加分不计数，游玩时请注意台面分数，避免无效上分;',
						'有任何问题，请加客服微信咨询!'
					]
				},
				report: {
					title: '平台公告',
					msg: '欢迎来到物联网游戏体验中心，为响应文市发[2016]26号文件，鼓励游戏游艺场所积极应用合法合规的新设备、改造服务环境、创新经营模式，支持其增设上网服务项目成为多种经营业务的城市文化娱乐综合体。顺应“互联网+”发展趋势，鼓励娱乐场所与互联网结合发展，实现场内场外、线上线下互动，增强娱乐场所体验式服务,不断拓展新型文化产业业态健康的娱乐理念。特此声明:',
					content: ['游戏内金币仅为游戏所用，游戏内不能交易不能转让', '游戏内钻石为奖励道具，不能兑换金币等有价证券', '本电玩体验中心对用户拥有的虚拟货币和票均不提供任何形式的回购']
				},

				series: [],
				game: [],
				box: [],
				recharge: [],
				signin: [],
				email: [],
				image: [{
						url: '../../static/sign.png',
						name: '签到',
						show: false
					},
					{
						url: '../../static/activity.png',
						name: '活动',
						show: false
					},
					{
						url: '../../static/email.png',
						name: '邮件',
						show: false
					},
					{
						url: '../../static/safe.png',
						name: '账户',
						show: false
					},
					{
						url: '../../static/report.png',
						name: '公告',
						show: false
					}
				]
			}
		},
		onLoad() {
			this.series = false

		},
		onShow() {
			//this.screenFull()
		},
		mounted() {
			this.$refs.report.open('center');
			this.token = uni.getStorageSync('token')
			this.resourcesLoaded();
			this.load()
			this.row1 = this.data.slice(0, (this.data.length + 1) / 2)
			this.row2 = this.data.slice((this.data.length + 1) / 2)


			window.addEventListener('resize', this.handleResize);
		},

		methods: {

			handleResize() {
				const {
					innerWidth,
					innerHeight
				} = window;
				const aspectRatio = innerWidth / innerHeight;

				if (aspectRatio < 1) {
					// 竖屏显示
					document.body.classList.add('landscape');
					document.body.classList.remove('portrait');
				} else {
					// 横屏显示
					document.body.classList.add('landscape');
					document.body.classList.remove('portrait');
				}
			},
			toggle(type, msg) {

				// open 方法传入参数 等同在 uni-popup 组件上绑定 type属性
				this.$refs.popup.open('center');
				this.gotoGame = msg

				this.time = setInterval(() => {
					this.$refs.popup.close()
					this.goto(this.gotoGame)
					clearInterval(this.time)
				}, 5000)
			},
			screenFull() {

				const element = document.documentElement;

				// 根据不同的浏览器获取全屏方法
				const requestFullScreen =
					element.requestFullscreen ||
					element.webkitRequestFullscreen ||
					element.mozRequestFullScreen ||
					element.msRequestFullscreen;

				// 请求全屏
				if (requestFullScreen) {
					requestFullScreen.call(element);
				}
				this.textShow = false

			},
			load() {

				uni.request({ //用户
					url: `http://gamebox.zgwit.cn:8082/api/user/${uni.getStorageSync('id')}`,
					method: 'GET',
					header: {
						'Content-Type': 'application/json;charset=UTF-8',
						'Authorization': 'Bearer ' + this.token
					},
					success: (item) => {

						this.user = item.data.data || ''
						this.url = 'http://gamebox.zgwit.cn:8082' + this.user.avatar
						console.log(this.url)
					}
				});

				uni.request({ //游戏厅
					url: 'http://gamebox.zgwit.cn:8082/api/game/list',
					method: 'GET',
					header: {
						'Content-Type': 'application/json;charset=UTF-8',
						'Authorization': 'Bearer ' + this.token
					},
					success: (item) => {
						this.game = item.data.data
					}
				});



			},

			resourcesLoaded() {
				var time = setTimeout(() => {
					if (document.readyState === 'complete') {
						this.loading = false
					} else {
						this.resourcesLoaded()
					}
				}, 2000)
			},
			handleGetData(show) {
				this.mask = this.chatShow = this.showAll[show];
			},
			handleModel(show) {
				this.mask = this.modelShow = this.showAll[show];
				if (!show) {
					this.load()
				}
			},
			handleSet(show) {
				this.mask = this.setShow = this.showAll[show];
			},
			handleRecharge(show) {
				this.mask = this.rechargeShow = this.showAll[show];

			},
			handleIcon(index, show) {
				this.mask = this.image[index].show = this.showAll[show];

			},
			handleOk() {
				let time = setInterval(() => {}, 1000)
				uni.showToast({
					title: '游戏加载中!',
				});
				// this.$refs.popup.close()
				// this.goto(this.gotoGame)

			},
			handleClose() {
				clearInterval(this.time)
				this.$refs.popup.close()
			},
			chooseSeries(mess) {

				uni.request({ //游戏厅
					url: `http://gamebox.zgwit.cn:8082/api/box/search`,
					method: 'POST',
					header: {
						'Content-Type': 'application/json;charset=UTF-8',
						'Authorization': 'Bearer ' + this.token
					},
					data: {
						filter: {
							game_id: mess
						}
					},
					success: (item) => {
						this.box = item.data.data
					}
				});

				this.index = mess
				this.show = !this.show
			},
			loader() {
				this.loading = !this.loading
			},
			handleReportOk() {
				this.$refs.report.close()
			},
			goto(name) {

				// uni.showToast({
				// 	title: '更新中!',
				// });
				uni.navigateTo({
					url: '/pages/play/play?name=' + name
				});
			}

		}
	}
</script>

<style lang="scss">
	.content {
		font-family: font;
		// width: 100vh;
		// height: 100vw;
		display: flex;
		flex-direction: column;
		position: relative;


		width: 100vh;
		height: 100vw;
		margin-left: 100vw;
		transform: rotate(90deg);
		transform-origin: left top;


		.showModel {
			background-color: rgba(0, 0, 0, 0.3);
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			height: 100%;
		}

		.header {
			width: 100%;
			height: 50px;
			color: white;
			//border: 1px solid gray;
			background-color: transparent;
			background-image: linear-gradient(to bottom, rgb(234, 56, 202), rgb(62, 0, 72));
			border-bottom: 2px solid rgb(99, 14, 78);
			position: fixed;
			top: 0;
			display: flex;
			align-items: center;
			justify-content: space-between;
			box-sizing: border-box;
			padding: 0 20px;
			font-size: 13px;

			.money {
				margin: 0 50px;

				box-shadow: 0 0 0 transparent, 0 0 8px whitesmoke inset, 0 0 0 transparent, 0 0 10px rgb(143, 0, 125);
				color: white;
				border-radius: 50px;
				background-color: rgb(39, 2, 55);

				span {
					padding: 0 15px;
				}

				image {
					width: 26px;
					height: 26px;
					box-shadow: 1px 1px rgba(0, 0, 0, 0.3), 2px 2px rgba(0, 0, 0, 0.3);
					border-radius: 50%;
					position: relative;
					box-sizing: border-box;
					padding: -1px;
				}
			}

			.start-bar {
				box-shadow: 0 0 3px white;
				width: 120px;
				background-color: rgb(43, 0, 39);
				text-align: center;
				color: white;
				border-radius: 10px;
				position: relative;
				left: -20px;
				//z-index: 1;
				box-sizing: border-box;
				text-shadow: 1px 1px rgba(0, 0, 0, 0.3), 2px 2px rgba(0, 0, 0, 0.3);
				//padding: 1px 0;
				position: relative;

				&::before {
					content: ''
					;
					border-radius: 10px;
					position: absolute;
					width: 40%;
					height: 100%;
					left: 0;
					top: 0;
					background-color: rebeccapurple;
					background-image: linear-gradient(to bottom, rgb(10, 247, 251), rgb(1, 125, 173), rgb(10, 247, 251));
				}
			}

			.group {
				display: flex;
				align-items: center;

				.group-flex {
					//min-width: 56px;
					overflow: hidden;
					display: flex;
					align-items: center;

					.chat-icon {

						width: 28px;
						height: 28px;
						margin-right: 3px;
					}

					span {
						min-width: 26px;
					}
				}

				.box {
					padding: 3px;
					box-sizing: border-box;
					border-radius: 25px;
					background: linear-gradient(to bottom, rgb(146, 27, 135), rgb(193, 43, 117));
					display: flex;
					justify-content: center;
					align-items: center;

					.photo {

						width: 37px;
						height: 37px;
						border-radius: 25px;
					}
				}
			}

			image {
				width: 30px;
				height: 30px;
			}
		}

		.mess {}



		.main {

			//z-index: -1;
			//margin: 50px 0;
			box-sizing: border-box;
			padding: 52px 10px 0px;
			// padding-top: 52px;
			// padding-bottom: 52px;
			//background-color: red;
			background-image: linear-gradient(to bottom, rgb(39, 15, 32), rgb(150, 59, 71));
			flex: 1;
			width: 100%;
			height: 100%;

			.returnIcon {
				position: fixed;
				left: 12px;
				//top: 55px;
				top: 50%;
				transform: translateY(-50%);
				width: 25px;
				height: 25px;
				background-color: pink;
				border-radius: 50%;
				box-sizing: border-box;
				padding: 5px;
			}

			.row {

				//background-color: red;
				width: 100%;
				height: 100%;
				box-sizing: border-box;
				// display: grid;
				// grid-template-rows: repeat(2, 1fr);
				// padding: 3px 0 7px;
				// grid-template-columns: repeat(3, 1fr);
				// grid-auto-flow: row;
				// grid-gap: 10px 0px;
				//overflow: scroll; 
				overflow-y: scroll;
				display: flex;
				flex-flow: row wrap;
				//box-shadow: inset 4px 0px 5px rgba(172, 36, 155, 0.2);
				//padding-right: 10px;
				position: relative;
				border-radius: 5px;

				&::after {
					content: '';
					position: absolute;
					width: 5px;
					height: 96%;
					top: 50%;
					transform: translateY(-50%);

				}

				.col-wid1 {
					width: 33%;
					padding: 5px 0px 8px;
				}

				.col-wid2 {
					width: 24%;
					//margin-right: 10px;
					padding: 15px 5px;

				}

				.col {

					box-sizing: border-box;

					display: flex;
					justify-content: center;
					//width: 22%;
					height: 50%;
					//flex-direction: row;
					//flex-wrap: nowrap;
					//overflow: hidden;


					.box {
						display: flex;
						justify-content: center;
						align-items: center;
						width: 90%;
						box-sizing: border-box;

						.box-content {
							box-sizing: border-box;
							width: 100%;
							height: 100%;
							position: relative;

							.num {

								position: absolute;
								top: 0px;
								right: 0;
								color: white;
								text-shadow: 1px 1px rgb(39, 15, 32), 2px 2px rgb(39, 15, 32);
								//font-size: 20px;
							}

							image {
								width: 100%;
								//	height: 100%;
								border: 3px solid rgb(255, 244, 38);
								border-radius: 7px;
								position: relative;
							}

							.font {
								width: 100%;
								text-align: center;
								color: white;
								position: absolute;
								//bottom: 10px;
								left: 50%;
								bottom: 0;
								transform: translateX(-50%);
								font-size: 22px;
								//text-shadow: 0px 0px 5px black;
								text-shadow: 1px 1px rgb(39, 15, 32), 2px 2px rgb(39, 15, 32);
							}
						}
					}

				}
			}

			.report {
				box-sizing: border-box;
				padding: 10px;
				//改
				width: 70vh;
				border-radius: 5px;
				background: white;

				.report-title {
					font-weight: bold;
					font-size: 20px;
					padding: 5px;
					text-align: center
				}

				.report-content {
					background: rgb(202, 223, 254);

					margin-bottom: 10px;
					padding: 5px 5px 20px
				}

				.report-foot {
					display: flex;
					justify-content: center;
					color: white;

					.report-foot-btn {

						display: flex;
						justify-content: center;
						border-radius: 20px;
						box-sizing: border-box;
						padding: 5px 40px;
						background: rgb(54, 120, 233);
						box-shadow: 1px 1px rgba(0, 0, 0, 0.6), 0 2px rgba(0, 0, 0, 0.6);

					}
				}
			}

			.modal {
				box-sizing: border-box;
				padding: 10px;
				//改
				width: 50vh;
				border-radius: 5px;
				background: white;

				.modal-title {
					font-weight: bold;
					font-size: 18px;
					padding: 5px;
					text-align: center
				}

				.modal-content {
					background: rgb(242, 242, 242);

					margin-bottom: 10px;
					padding: 5px 5px 20px
				}

				.modal-foot {
					display: flex;
					justify-content: space-around;
					color: white;
					align-items: center;

					.progress {
						width: 25px;
						height: 25px;
						animation: spin 2s linear infinite;
					}


					.modal-foot-btn {

						display: flex;
						justify-content: center;
						align-items: center;

						// text-shadow: 2px 2px 2px rgba(0, 0, 0, 0.5), -2px -2px 2px rgba(0, 0, 0, 0.5);
						// ;

						border-radius: 6px;
						box-sizing: border-box;
						padding: 5px 20px;
						background-image: linear-gradient(to bottom, rgb(40, 175, 255), rgb(29, 111, 255));
						box-shadow: 1px 1px rgba(0, 0, 0, 0.6), 0 2px rgba(0, 0, 0, 0.6);

					}

					.red {
						color: rgb(173, 52, 77)
					}
				}
			}

		}

		.main-pad {
			padding-left: 40px
		}

		.foot {
			position: fixed;
			bottom: 0;
			width: 100vh;
			height: 50px;
			line-height: 50px;
			//background-color: transparent;
			background-image: linear-gradient(to bottom, rgb(252, 44, 213), rgb(67, 0, 71));
			display: flex;
			align-items: center;
			box-sizing: border-box;
			padding: 0 20px;
			color: white;
			font-size: 15px;
			// /justify-content: space-between;
			margin-right: 20px;

			.tag {
				min-width: 83px;
				height: 100%;
				display: flex;
				justify-content: center;
				align-items: center;
				box-sizing: border-box;
				padding: 10px 0px;
				overflow: hidden;

				.image-border {
					box-sizing: border-box;
					padding: 1px;
					border-radius: 50%;
					margin-right: 3px;
					background-image: linear-gradient(to bottom, rgb(146, 27, 135), rgb(193, 43, 117));
					;

					display: flex;
					justify-content: center;
					align-items: center;

					image {
						width: 30px;
						height: 30px;
						border-radius: 50%;
						//box-shadow: 0 0 10px white;

					}

				}


			}


		}

		.showToast {

			position: absolute;
			transform: rotate(0deg);
			//改
			width: 100px;
			height: 100px;
			border-radius: 5px;
			left: 50%;
			top: 50%;
			// background-color: rgb(57, 6, 15);
			margin-left: 0vw;
			transform: translate(-50%, -49%);
		}

		.chat {

			position: absolute;
			transform: rotate(0deg);
			//改
			width: 90vh;
			height: 90vw;
			left: 50%;
			top: 50%;
			background-color: rgb(57, 6, 15);
			margin-left: 0vw;
			transform: translate(-50%, -49%);
		}

		.model {

			position: absolute;
			transform: rotate(0deg);
			//改 
			width: 60vh;
			height: 90vw;
			left: 50%;
			top: 50%;
			background-color: rgb(57, 6, 15);
			margin-left: 0vw;
			transform: translate(-50%, -49%);
		}

		@keyframes spin {
			0% {
				transform: rotate(0deg);
			}

			100% {
				transform: rotate(360deg);
			}
		}


		.loading {

			width: 100%;
			height: 100%;
			//background-color: rgba(0, 0, 0, 0.3);
			position: absolute;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;

			&::before {
				content: '';
				width: 100%;
				height: 100%;
				background-image: linear-gradient(to right, rgb(44, 13, 23), rgb(74, 21, 39), rgb(44, 13, 23));
				position: absolute;
				left: 0;
			}



			.progress {
				width: 30px;
				height: 30px;
				animation: spin 2s linear infinite;
				margin: 20px 0;
			}

			span {
				text-align: center;
				color: white;
				width: 70%;
				z-index: 1
			}
		}

	}
</style>
