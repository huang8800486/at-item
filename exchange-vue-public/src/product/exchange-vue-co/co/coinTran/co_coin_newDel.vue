<!-- 合约 - 最新成交 -->
<template>
  <div class="side-newDel item-block">
		<div class="newDel-title">
			<span>{{$t('trade.latestDeal')}}</span>
		</div>
		<ul class="tr-list newDel-list">
			<li class="tr-list-title">
				<span class="tr-mName thead-color">{{$t('trade.time')}}</span>
				<span class="tr-mPrice thead-color">{{$t('trade.price')}}(<s>{{symbolName.count}}</s>)</span>
				<span class="tr-mZdvol thead-color">{{$t('trade.dealVolume')}}(<s>{{symbolName.base}}</s>)</span>
			</li>
		</ul>
    <div class="tr-list newDel-list newDel-box" v-if="tradeListData">
    <happy-scroll color="#1c2b49" size="0" resize smaller-move-v='start' smaller-move-h='start' bigger-move-h='start' bigger-move-v='start' >
		<ul>
			<li class="baColor" v-for="(item, index) in tradeListData" :key="index">
				<span class="tr-mName thead-color">{{item.time}}</span>
				<span class="tr-mPrice" :class="item.side" @click="tradePriceHandel(item.price)">
					<s class="show-def">{{item.price | toFix('price', _that)}}</s>
          <!--  | toFC(_that) -->
					<s class="show-hover">{{item.price | toFC(_that)}}</s></span>
				<span class="tr-mZdvol" :class="item.side">{{item.vol | toFix('volume', _that)}}</span>
			</li>
		</ul>
  </happy-scroll>
  </div>
  </div>              
</template>
<script>
import bus from '@/apis/bus.js'
import { mapState } from 'vuex'
export default {
  name: 'newDel',
  components: {
  },
  data () {
    return {}
  },
  filters: {
    toFC (v, that) {
      let market = that.co_baseData._nowSymbol.split('/')[1].toUpperCase()
      let { _rate } = that.baseData
      return that._P.fixRate(v, _rate, market)
    },
    toFix (v, type, that) {
      let { _symbols, _nowSymbol } = that.co_baseData
      let market = _nowSymbol.split('/')[1]
      if (!_symbols[market]) return
      return that._P.fixD(v, _symbols[market][_nowSymbol][type])
    }
  },
  props: {
    tradeListData: {
      default: []
    },
    symbolName: {
      default: {}
    }
  },
  computed: {
    ...mapState({
      co_baseData ({coBaseData}) {
        return coBaseData
      },
      baseData ({baseData}) {
        return baseData
      }
    }),
    _that () { return this }
  },
  methods: {
    tradePriceHandel (price) {
      if (this.$store.state.baseData.isLogin) {
        bus.$emit('onevents', price)
      }
    }
  }
}
</script>
<style>
/* 在组件 非异步加载的时候 滚动条插件获取不到宽高  现在写死 后续一定记着回来优化*/
  .side-newDel .happy-scroll .happy-scroll-container {
    height: 409px !important;
    width: 303px !important;
  }
</style>

