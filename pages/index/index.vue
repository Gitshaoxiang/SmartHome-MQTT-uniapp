<template>
	<view class="content">
		<u-notify ref="uNotify"></u-notify>

		<u-popup :show="showPop" mode="bottom" @open="openPop" @close="closePop">
			<view class="pop-container">
				<view class="pop-input-group">
					<u--input placeholder="Device Topic" border="surround" v-model="newTopic"></u--input>
				</view>
				<view class="pop-btn-group">
					<u-button type="primary" text="添加" @click="addDevice"></u-button>
					<u-button type="primary" :plain="true" text="取消" @click="closePop"></u-button>
				</view>
			</view>
		</u-popup>

		<view class="body-contariner">
			<view class="body-title">
				<p>HOME</p>
			</view>
			<view class="controller-items">
				<controllerItem :config="item" v-for="item in config" :key="item.name" @buttonChange="buttonHandler">
				</controllerItem>
			</view>
			<view class="controller-add">
				<view class="controller-add-title" @click="showPop = true">
					Add new device
				</view>
				<u-icon name="plus" color="#2d5cf7" size="25"></u-icon>
			</view>
		</view>
	</view>
</template>

<script>
	import controllerItem from '@/components/controller-item/controller-item.vue'
	// import mqtt from 'mqtt'
	var mqtt = require('mqtt/dist/mqtt.js')
  
  
	export default {
		components: {
			controllerItem
		},
		data() {
			return {
				showPop: false,
				newTopic: '',
				config: [{
						icon: {
							on: "ac_on",
							off: "ac_off"
						},
						name: "AC"
					},
					{
						icon: {
							on: "fridge_on",
							off: "fridge_off"
						},
						name: "Fridge"
					},
					{
						icon: {
							on: "light_on",
							off: "light_off"
						},
						name: "Light"
					},
					{
						icon: {
							on: "tv_on",
							off: "tv_off"
						},
						name: "TV"
					}
				],
				connection: {
					host: 'broker.emqx.io',
					port: 8083,
					endpoint: '/mqtt',
					clean: false, // 保留会话
					connectTimeout: 4000, // 超时时间
					reconnectPeriod: 4000, // 重连时间间隔
					// 认证信息
					clientId: 'mqttjs_idididididid',
					username: 'emqx_test',
					password: 'emqx_test',
				},
				subscription: {
					topic: 'topic/mqttx',
					qos: 0,
				},
				receiveNews: '',
				qosList: [{
						label: 0,
						value: 0
					},
					{
						label: 1,
						value: 1
					},
					{
						label: 2,
						value: 2
					},
				],
				client: {
					connected: false,
				},
				subscribeSuccess: false
			}
		},
		created() {
		},
		mounted() {
			this.createConnection()
		},
		onReady() {},
		methods: {
			// Add device Info
			addDevice() {
				this.config.push({
					icon: {
						on: "device_on",
						off: "device_off"
					},
					name: this.newTopic
				})
				this.closePop()
				this.newTopic = ''
			},
			openPop() {

			},
			closePop() {
				this.showPop = false
			},
			// Button Event Handler
			buttonHandler(device, status) {
				console.log(status)
				let data = {
					topic: `topic/${device}`,
					qos: 2,
					payload: `{ "msg": "${status}" }`
				}
				console.log(data)
				this.doPublish(data)
			},
			// 创建连接
			createConnection() {
				const {
					host,
					port,
					endpoint,
					...options
				} = this.connection
				
				
				// #ifdef H5
				const connectUrl = `ws://${host}:${port}${endpoint}`
				// #endif
				// #ifdef MP-WEIXIN||APP-PLUS
				const connectUrl = `wx://${host}:${port}${endpoint}`
				// #endif

				
				try {
					this.client = mqtt.connect(connectUrl, options)
				} catch (error) {
					console.log('mqtt.connect error', error)
				}
				this.client.on('connect', () => {
					console.log('Connection succeeded!')
				})
				this.client.on('error', error => {
					console.log('Connection failed', error)
				})
				this.client.on('message', (topic, message) => {
					this.receiveNews = this.receiveNews.concat(message)
					console.log(`Received message ${message} from topic ${topic}`)
				})
			},
			// 订阅主题
			doSubscribe() {
				const {
					topic,
					qos
				} = this.subscription
				this.client.subscribe(topic, {
					qos
				}, (error, res) => {
					if (error) {
						console.log('Subscribe to topics error', error)
						return
					}
					this.subscribeSuccess = true
					console.log('Subscribe to topics res', res)
				})
			},
			// 取消订阅
			doUnSubscribe() {
				const {
					topic
				} = this.subscription
				this.client.unsubscribe(topic, error => {
					if (error) {
						console.log('Unsubscribe error', error)
					}
				})
			},
			// 发送消息
			doPublish(data) {
				this.client.publish(data.topic, data.payload, data.qos, error => {
					if (error) {
						console.log('Publish error', error)
					}
				})
			},
			// 断开连接
			destroyConnection() {
				if (this.client.connected) {
					try {
						this.client.end()
						this.client = {
							connected: false,
						}
						console.log('Successfully disconnected!')
					} catch (error) {
						console.log('Disconnect failed', error.toString())
					}
				}
			}
		}
	}
</script>

<style lang="scss">
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		height: 100%;
		// background: linear-gradient(to bottom right, #adadad, #1d2e37);
		background-image: url(@/static/img/background.jpg);
		background-size: cover;
	}

	.body-contariner {
		display: flex;
		flex-direction: column;
		justify-content: start;
		align-items: center;
		height: 100%;
		padding: 10rpx 50rpx;
		background-color: #00000061;
		width: 100%;

		.body-title {
			color: white;
			height: 180rpx;
			line-height: 180rpx;
			margin-top: 50rpx;
			font-weight: bold;
		}

		.controller-items {
			display: flex;
			flex-wrap: wrap;
			justify-content: space-around;
			padding: 10rpx;
			width: 100%;

			.item-card {
				display: flex;
				padding: 0rpx 30rpx;
				height: max-content;
				justify-content: left;
				min-height: 120rpx;
				margin: 10rpx;
				background-color: #ffffff12;
				border-radius: 5rpx;
				align-items: center;
				color: white;
				font-size: 22rpx;

				.u-icon {
					margin-right: 10rpx;
				}

				.value {
					margin-left: 30rpx;
					color: #16dc00;
					word-break: break-all;
				}

			}
		}

		.controller-add {
			width: 80%;
			background-color: white;
			height: 90rpx;
			position: absolute;
			bottom: 30rpx;
			border-radius: 30rpx;
			display: flex;
			justify-content: center;

			.controller-add-title {
				line-height: 90rpx;
				font-weight: bold;
				margin: 0 30rpx;
			}
		}
	}

	.pop-container {
		height: 300rpx;
		display: flex;
		flex-direction: column;

		.pop-input-group {
			flex: 1;
			display: flex;
			flex-direction: row;
			justify-content: center;
			.u-input {
				width: 80%;
			}
		}

		.pop-btn-group {
			display: flex;
			justify-content: center;
			height: 80rpx;
			margin-bottom: 40rpx;

			.u-button {
				margin: 20rpx;
				width: 190rpx;
				height: 50rpx;
				line-height: 50rpx;
				display: inline-block;
			}
		}
	}
</style>
