<template>
    <view class="content">
        <view class="content_item" v-for="(item,index) in dataList" :key="item.id">
            <view class="content_check_box">
                <van-checkbox use-icon-slot :value="item.check"
                              @change="onCheckChange($event,index)">
                    <image class="select_img" slot="icon" :src="item.check ? activeIcon : inactiveIcon"></image>
                </van-checkbox>
            </view>

            <view class="content_img">
                <image src="../../static/vva.png"></image>
            </view>

            <view class="content_info">
                <view class="content_title">{{item.title}}</view>
                <view class="iconfont content_size">8英寸 &#xe601;</view>
                <view class="content_money">¥{{item.money}}</view>

            </view>

            <view class="content_stepper">
                <van-stepper :value="item.number"
                             @change="onStepperChange($event,index)"></van-stepper>
            </view>
        </view>

        <view>

            <van-submit-bar
                    :price="sumPrice"
                    button-text="立即支付"
                    @submit="onCommentOrder"
                    :tip="true"
                    :loading="showLoading">
                <view>
                    <van-checkbox use-icon-slot :value="isAllSelect" @change="onAllCheck">
                        <image class="select_img" slot="icon"
                               :src="isAllSelect ? activeIcon : inactiveIcon"></image>
                        全选
                    </van-checkbox>
                </view>
            </van-submit-bar>

        </view>
    </view>
</template>

<script>
  export default {
    data () {
      return {
        dataList: [
          {
            id: 1,
            check: false,
            image: '',
            title: '薇薇安娜榴莲千层',
            size: 1,
            size_title: '8英寸',
            money: 100.00,
            number: 1,

          },
          {
            id: 2,
            check: false,
            image: '',
            title: '薇薇安娜榴莲万层',
            size: 1,
            size_title: '8英寸',
            money: 200.00,
            number: 2,

          },
          {
            id: 3,
            check: false,
            image: '',
            title: '薇薇安娜榴莲百层',
            size: 1,
            size_title: '8英寸',
            money: 300.00,
            number: 3,
          }
        ],
        activeIcon: '../../static/icon-active.png',
        inactiveIcon: '../../static/icon-normal.png',
        isAllSelect: false,
        sumPrice: 0,
        showLoading : false
      }
    },
    onLoad () {

    },
    methods: {
      //选择一个item
      onCheckChange (event, index) {
        this.dataList[index].check = event.detail   //点亮打勾

        //判断是选中还是取消选中
        if (event.detail) {
          this.sumPrice += (this.dataList[index].money * this.dataList[index].number) * 100
        } else {
          this.sumPrice -= (this.dataList[index].money * this.dataList[index].number) * 100
        }

        //判断是否已经全部选完，点亮全选按钮
        let sum = 0
        for (let i = 0 ; i < this.dataList.length ; i ++){
          if (this.dataList[i].check){
            sum ++
          }
        }
        this.isAllSelect = sum === this.dataList.length
      },
      //商品数量加1或减1
      onStepperChange (event, index) {
        this.dataList[index].number = event.detail
        //没有选中的item，如果点击了+1 不会影响总价
        if (!this.dataList[index].check){
             return
        }
        this.sumPrice = 0   //重置总价，重新计算
        for (let i = 0; i < this.dataList.length; i ++){
          if (this.dataList[i].check) {
            //如果是已经选中的，再加到总价上
            this.sumPrice += this.dataList[i].money * this.dataList[i].number * 100
          }
        }

      },
      //提交订单
      onCommentOrder () {
        if (this.sumPrice === 0){
          uni.showToast({
            title : '请先选择物品',
            icon : 'none'
          })
          return
        }
        this.showLoading = true
        let that = this
        setTimeout(function()
        {
          uni.showToast({
            title : '已支付' + that.sumPrice / 100 + '元',
          })
          that.showLoading = false
          }
        , 1500);

      },
      //全选
      onAllCheck (event) {
        this.isAllSelect = event.detail
        this.sumPrice = 0
        for (let i = 0; i < this.dataList.length; i++) {
          if (event.detail) {
            //全选
            this.dataList[i].check = true
            this.sumPrice += this.dataList[i].money * this.dataList[i].number * 100
          }else {
            //取消全选
            this.dataList[i].check = false
            this.sumPrice = 0
          }
        }
      }
    }
  }
</script>

<style lang="scss" scoped>

    .select_img {
        width: 24rpx;
        height: 24rpx;
    }

    .content {
        margin-top: 40rpx;
        .content_item {
            display: flex;
            padding: 0 40rpx 40rpx 32rpx;

            .content_check_box{
                display: flex;
                align-items: center;
            }
            .content_img {
                margin: 0 24rpx;
                image {
                    width: 160rpx;
                    height: 160rpx;
                    border-radius:16rpx;
                }

            }

            .content_info {
                flex: 1;
                .content_title {
                    color: #505050;
                    font-size: 28rpx;
                    font-weight:400;
                }

                .content_size {
                    margin: 20rpx 0 24rpx 0;
                    width: 128rpx;
                    height: 40rpx;
                    font-size: 22rpx;
                    color: #505050;
                    background-color: #F5F5F5;
                    border-radius:8rpx;
                    text-align: center;
                    line-height: 40rpx;
                    font-weight:400;
                }

                .content_money {
                    color: #F64851;
                    font-size: 32rpx;
                    font-weight:500;
                }

            }

            .content_stepper {
                display: flex;
                align-items: flex-end;
            }

        }
    }

</style>
