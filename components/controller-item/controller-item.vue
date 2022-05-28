<template>
	<view class="controller-container" :class="{'active': status}">
		<view class="controller-info">
			<div v-show="status" class="controller-device-icon" :class="config.icon.on" alt=""></div>
			<div v-show="!status" class="controller-device-icon" :class="config.icon.off" alt=""></div>
			<view class="controller-device-name" :class="{'active': status}">
				{{config.name}}
			</view>
		</view>
		<u-switch class="controller-btn" v-model="status" @change="change" size="20" inactiveColor="#002092"
			activeColor="#2f5bf6"></u-switch>
	</view>
</template>

<script>
	export default {
		data: function() {
			return {
				status: false
			}
		},
		props: {
			config: {
				type: Object
			}
		},
		mounted() {

		},
		methods: {
			change() {
				this.$emit("buttonChange", this.config.name, this.status?"on":"off")
			}
		}
	};
</script>

<style lang="scss">
	.controller-container {
		display: flex;
		justify-content: center;
		width: 220rpx;
		height: 220rpx;
		border-radius: 30px;
		padding: 30rpx 30rpx;
		margin: 10rpx;
		// background: linear-gradient(to bottom right, #adadad, #baced9);
		background-color: #2d5cf7;
		transition: all .2s ease;

		.controller-info {
			display: flex;
			flex-direction: column;
			justify-content: space-around;

			.controller-device-icon {
				height: 120rpx;
				background-size: contain;
				width: 120rpx;
				background-repeat: no-repeat;
				background-position: center;

			}

			.controller-device-name {
				color: white;
				text-align: center;
			}

			.controller-device-name.active {
				color: #2d5cf7;
			}
		}
	}

	.controller-container.active {
		// background: linear-gradient(to bottom right, #adadad, #0082c3);
		// background: linear-gradient(to bottom right, blue, pink);
		background-color: white;
	}


	.ac_on {
		background-image: url(@/static/icon/ac_on.png);
	}

	.ac_off {
		background-image: url(@/static/icon/ac_off.png);
	}

	.light_on {
		background-image: url(@/static/icon/light_on.png);
	}

	.light_off {
		background-image: url(@/static/icon/light_off.png);
	}

	.fridge_on {
		background-image: url(@/static/icon/fridge_on.png);
	}

	.fridge_off {
		background-image: url(@/static/icon/fridge_off.png);
	}

	.tv_on {
		background-image: url(@/static/icon/tv_on.png);
	}

	.tv_off {
		background-image: url(@/static/icon/tv_off.png);
	}
	.device_on {
		background-image: url(@/static/icon/device_on.png);
	}

	.device_off {
		background-image: url(@/static/icon/device_off.png);
	}
</style>
