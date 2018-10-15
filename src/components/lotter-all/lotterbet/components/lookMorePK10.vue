<template lang='jade'>
.lookAllDiv(v-show='lookAllUl')
  p.lookAllDivTitle
    i.iconfont.icon-left(@click='lookAllDivTitle')
    b.cont 查看更多
    span
  .lookAllUlBox
    ul.lookAllUl
      li
        p 期号
        p 开奖号码
        p 开奖时间
      li(v-for='(item,index) in getPastOpens', :key='index')
        p
          | {{item.seasonId.substring(4).split("-").join("")*1}}
          i.iconfont.icon-plus-minus
        p
          a {{item.n1|addZero}}
          a {{item.n2|addZero}}
          a {{item.n3|addZero}}
          a {{item.n4|addZero}}
          a {{item.n5|addZero}}
          a {{item.n6|addZero}}
          a {{item.n7|addZero}}
          a {{item.n8|addZero}}
          a {{item.n9|addZero}}
          a {{item.n10|addZero}}
        p {{item.addTime.substring(11)}}
</template>
<script>
export default {
  computed: {
    getPastOpens(){
      return this.$store.getters.getPastOpens;
    },
    lookAllUl(){
        return this.$store.state.lookAllUl;
    }
  },
  methods: {
    lookAllDivTitle() {
      this.$store.commit("LOOK_ALL_UL","reverse");
    }
  },
  filters:{
      addZero(v){
          if(v<10){
              return "0"+v;
          }else{
              return v;
          }
      }
  }
};
</script>
<style lang="scss" scoped>
@import "../../../../assets/scss/lotter-list/lotterbet/components/lookMorePK10.scss";
</style>

