<template>
	<view class="flyer-actionsheet__mask" :class="{ 'flyer-actionsheet__mask-show': shouldShow }"
		@tap="handleClickMask"></view>
	<view class="flyer-actionsheet__wrap flyer-as__bg-light flyer-actionsheet__radius"
		:class="{ 'flyer-actionsheet__show': shouldShow}"
		ref="fui_as_ani" :style="{ zIndex: zIndex }">
		<text class="flyer-actionsheet__tips flyer-as__btn-light flyer-actionsheet__radius flyer-as__divider-light"
			:style="{ fontSize: formatFontSize(size), color: color }" v-if="tips">{{ tips }}</text>
		<view :class="{ 'flyer-actionsheet__operate-box': isCancel }">
			<view class="flyer-actionsheet__btn flyer-as__btn-light"
				:style="{ color: item.color, fontSize: formatFontSize(item.size) }"
				:class="{ 'flyer-actionsheet__btn-last': btnLast(index), 'flyer-as__safe-weex': safeAreaClass(index), 'flyer-actionsheet__radius': tips == null && index === 0}"
				v-for="(item, index) in itemList" :key="index" @tap="handleClickItem(index)">
				<text>{{ item.text }}</text>
			</view>
		</view>
		<view :style="{ color: itemColor, fontSize: cancelFontSize }"
			class="flyer-actionsheet__btn flyer-as__btn-light"
			:class="{ 'flyer-as__safe-weex': safeArea }"
			v-if="isCancel" @tap="handleClickCancel">取消</view>
	</view>
</template>


<script lang="uts">
	type ItemListType = { text : string, color : string, size : number }

	export default {
		name: 'FlyerPopup',

		props: {
			visible: {
				type: Boolean,
				required: true
			},
			setVisible: {
				type: Function as PropType<(bool : boolean) => void>,
				required: true
			},
			itemList: {
				type: Array as ItemListType[],
				required: true
			},
			//提示信息
			tips: {
				type: String,
				default: ""
			},
			//提示信息文本颜色
			color: {
				type: String,
				default: "#7F7F7F"
			},
			//菜单按钮字体大小 rpx
			itemSize: {
				type: Number,
				default: 32
			},
			//v2.4.0+
			itemColor: {
				type: String,
				default: '#181818'
			},
			//提示文字大小 rpx
			size: {
				type: Number,
				default: 26
			},
			//是否需要取消按钮
			isCancel: {
				type: Boolean,
				default: true
			},
			//v2.4.0+
			cancelSize: {
				type: [Number, String],
				default: 32
			},
			//点击遮罩 是否可关闭
			maskClosable: {
				type: Boolean,
				default: true
			},
			zIndex: {
				type: [Number, String],
				default: 996
			},
			//是否适配底部安全区
			safeArea: {
				type: Boolean,
				default: true
			}
		},

		data() {
			return {
				basicItems: [
					{ text: '选项一', color: '#FF0000', size: 32 },
					{ text: '选项二', color: '#00FF00', size: 32 },
					{ text: '选项三', color: '#0000FF', size: 32 }
				] as ItemListType[]
			}
		},

		computed: {
			// 计算是否应该显示弹窗
			shouldShow() : boolean {
				return this.visible
			},

			// 计算弹窗显示状态的类名
			showClass() : boolean {
				return this.visible
			},

			cancelFontSize(): string {
				if(typeof this.cancelSize == 'number') {
					return "".repeat(this.cancelSize) + 'rpx'
				}
				return "".repeat(this.itemSize) + 'rpx'
			}
		},

		onShow() {
		},

		methods: {
			formatFontSize(size: number) {
				return "".repeat(size) + 'rpx'
			},
			btnLast(index: number): boolean {
				return !this.isCancel && index == this.itemList!!.length - 1
			},
			safeAreaClass(index: number): boolean {
				let len = 0
				if(typeof this.itemList!!.length == 'number') {
					len = this.itemList!!.length - 1
				}
				return !this.isCancel && index == len && this.safeArea
			},
			dividerLight(index: number): boolean {
				if(index == 0) {
					return false
				}
				if(this.tips == null || this.tips === "") {
					return false
				}
				return true
			},
			// 事件处理函数
			handleClickMask() {
				if (!this.maskClosable) return;
				this.handleClickCancel();
			},
			handleClickItem(index: number) {
				if (!this.visible) return;

				if(typeof this.itemList!![index] === 'object') {
					this.$emit('click', {
						index: index,
						...this.itemList!![index]
					});
				}
			},
			handleClickCancel() {
				this.setVisible(false);
			},
		}
	}
</script>

<style scoped>
.flyer-actionsheet__wrap {
	width: 100%;
	visibility: hidden;
	min-height: 100rpx;
	position: fixed;
	left: 0;
	right: 0;
	bottom: 0;
	transform-origin: center center;
	transform: translate3d(0, 100%, 0);
	transition: all 0.25s ease-in-out;
}

.flyer-as__bg-light {
	background-color: #F8F8F8;
}

.flyer-as__bg-dark {
	background-color: #111111;
}

.flyer-actionsheet__radius {
	border-top-left-radius: 24rpx;
	border-top-right-radius: 24rpx;
}

.flyer-actionsheet__show {
	transform: translate3d(0, 0, 0);
	visibility: visible;
}

.flyer-actionsheet__tips {
	flex: 1;
	padding: 40rpx 60rpx;
	text-align: center;
	align-items: center;
	justify-content: center;
	font-weight: normal;
}

.flyer-as__btn-light {
	background-color: #FFFFFF;
}

.flyer-as__btn-dark {
	background-color: #222222;
}

.flyer-actionsheet__operate-box {
	padding-bottom: 12rpx;
}

.flyer-actionsheet__btn {
	height: 100rpx;
	line-height: 100rpx;
	flex: 1;
	align-items: center;
	justify-content: center;
	text-align: center;
	/* font-size: 32rpx; */
	font-weight: normal;
	position: relative;
	border-bottom-width: 0.5px;
	border-bottom-style: solid;
	border-bottom-color: #EEEEEE;
}

.flyer-actionsheet__btn:active {
	background-color: rgba(0, 0, 0, 0.2);
}

.flyer-as__divider-light {
	border-bottom-width: 0.5px;
	border-bottom-style: solid;
	border-bottom-color: #EEEEEE;
}

.flyer-as__safe-weex {
	height: 168rpx;
	padding-bottom: 34px;
}

.flyer-actionsheet__mask {
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	background-color: rgba(0, 0, 0, 0.3);
	transition: opacity 0.3s ease-in-out;
	opacity: 0;
	visibility: hidden;
}

.flyer-actionsheet__mask-show {
	visibility: visible;
	opacity: 1;
}
</style>