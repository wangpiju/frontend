<template lang="jade">
.listStyle
  .listStyle-top
    i.iconfont.icon-left(@click='listStyleToSafety')
    p 代理说明
    span
  .listStyle-content
    .listStyle-content-top
      img(src='@/assets/img/one/agentIntro.png')
    ul.intro
      p 当您能看到这个页面，说明您的账号即是玩家账号也是代理账号，即可以自己投注，也可以发展下级玩家，赚取返点佣金。
      span 如何赚取返点？
      p 可获得的返点，等于自身返点与下级返点的差值，如自身返点5，下级返点3，你将能获得下级投注金额2%的返点，如下级投注100元，你将会获得2元。
      p 点击下级开户，可查看自身返点，也可为下级设置返点。
      span 如何为下级开户？
      p 点击下级开户，先为您的下级设置返点，设置成功后会生成一条邀请码，将邀请码发送给您的下级注册，注册后他就是您的下级，点击会员管理，就能查看他注册的账号；如果您为下级设置的是代理类型的账号，那么您的下级就能继续发展下级，如果设置的是玩家类型，那么您的下级只能投注，不能再发展下级，也看不到代理中心；
      span 温馨提示：
      p 返点不同赔率也不同，点击返点赔率表，可查看返点赔率； 返点越低，赔率就越低，建议为下级设置的返点不要过低；可在代理报表、投注明细、交易明细查看代理的发展情况；建议开设的下级也是代理类型，无论发展了几级，您都能获得返点。
</template>
<script>
export default {
  methods:{
    listStyleToSafety(){
      this.$router.push('/agency')
    },
  }
}
</script>

<style lang="scss" scoped>
@import "../../../assets/scss/listStyle.scss";
</style>