<template>
	<view class="content vs-row">
		<scroll-view scroll-y class="left-aside">
			<view v-for="item in flist" :key="item.id" class="f-item b-b vs-row vs-align-center vs-space-center" :class="{active: item.id === currentId}" @click="tabtap(item)">
				{{item.name}}
			</view>
		</scroll-view>
		<scroll-view scroll-with-animation scroll-y class="right-aside vs-flex-item" :scroll-into-view="scrollIntoView">
			<view v-for="item in slist" :key="item.id" class="s-list" :id="'main-'+item.pid">
				<text class="s-item vs-row vs-align-center">{{item.name}}</text>
				<view class="t-list vs-row">
					<view @click="navToList(item.id, titem.id)" v-if="titem.pid === item.id" class="t-item vs-column vs-align-center vs-space-center" v-for="titem in tlist" :key="titem.id">
						<image :src="titem.picture"></image>
						<text>{{titem.name}}</text>
					</view>
				</view>
			</view>
		</scroll-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				sizeCalcState: false,
				tabScrollTop: 0,
				currentId: 1,
				flist: [],
				slist: [],
				tlist: [],
        scrollIntoView: ''
			}
		},
		onLoad(){
			this.loadData();
		},
		methods: {
			async loadData(){
				let list = await this.$api.json('cateList');
				list.forEach(item=>{
					if(!item.pid){
						this.flist.push(item);  //pid为父级id, 没有pid或者pid=0是一级分类
					}else if(!item.picture){
						this.slist.push(item); //没有图的是2级分类
					}else{
						this.tlist.push(item); //3级分类
					}
				})
			},
			//一级分类点击
			tabtap(item){
        this.scrollIntoView = "main-" + item.id
				this.currentId = item.id;
			},
			navToList(sid, tid){
				uni.navigateTo({
					url: `/pages/product/list?fid=${this.currentId}&sid=${sid}&tid=${tid}`
				})
			}
		}
	}
</script>

<style lang='scss'>
	page,
	.content {
		height: 100%;
		background-color: #f8f8f8;
	}
	
	.left-aside {
		flex-shrink: 0;
		width: 200upx;
		height: 100%;
		background-color: #fff;
	}
	.f-item {
		width: 100%;
		height: 100upx;
		font-size: 28upx;
		color: $font-color-base;
		position: relative;
		&.active{
			color: $base-color;
			background: #f8f8f8;
			&:before{
				content: '';
				position: absolute;
				left: 0;
				top: 50%;
				transform: translateY(-50%);
				height: 36upx;
				width: 8upx;
				background-color: $base-color;
				border-radius: 0 4px 4px 0;
				opacity: .8;
			}
		}
	}

	.right-aside{
		overflow: hidden;
		padding-left: 20upx;
	}
	.s-item{
    position: sticky;
    top: 0;
    padding-left: 20rpx;
		height: 70upx;
		padding-top: 8upx;
		font-size: 28upx;
		color: $font-color-dark;
    background-color: #fff;
    z-index: 99;
	}
	.t-list{
		flex-wrap: wrap;
		width: 100%;
		background: #fff;
		padding-top: 12upx;
		&:after{
			content: '';
			flex: 99;
			height: 0;
		}
	}
	.t-item{
		flex-shrink: 0;
		width: 176upx;
		font-size: 26upx;
		color: #666;
		padding-bottom: 20upx;
		
		image{
			width: 140upx;
			height: 140upx;
		}
	}
</style>
