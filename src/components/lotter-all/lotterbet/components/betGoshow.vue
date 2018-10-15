<template lang='jade'>
.betcBox(v-show='betGoshow')
  ul.betc
    li 投注确认
    li
      p
        span {{$route.query.name}} ：
        | {{seasonId}}期
      p
        span 投注金额：
        b {{money*zhu}}元
      p
        span 投注内容：
        span.popcon {{con}}
    li
      button(@click='betCancel') 取消
      button(@click='betGo') 确定
</template>
<script>
export default {
  data() {
    return {
      conTemp:"",
      zhuTemp:"",
      betFun: []
    };
  },
  computed: {
    betGoshow() {
      return this.$store.state.betGoshow;
    },
    zhu() {
      return this.$store.state.zhu;
    },
    con() {
      return this.$store.state.con;
    },
    money() {
      return this.$store.state.money;
    },
    seasonId() {
      return this.$store.getters.seasonId;
    },
    seasonId2() {
      return this.$store.state.seasonId2;
    },
    playBonusId() {
      return this.$store.getters.playBonusId;
    }
  },
  methods: {
    betCancel() {
      this.$store.commit("BET_GO_SHOW", "reverse");
    },
    bet(obj) {
      let config = {
        headers: { "Content-Type": "application/x-www-form-urlencoded" },
        withCredentials: true
      };
      let formData = new FormData();
      formData.append("order[0].content", obj.con);
      formData.append("order[0].betCount", obj.zhu);
      formData.append("order[0].price", this.money);
      formData.append("order[0].unit", 1);
      formData.append("order[0].playId", this.playBonusId);
      formData.append("count", obj.zhu);
      formData.append("traceOrders[0].price", this.money);
      formData.append("traceOrders[0].seasonId", this.seasonId2);
      formData.append("bounsType", 0);
      formData.append("traceWinStop", 0);
      formData.append("isTrace", 0);
      formData.append("lotteryId", this.$route.query.id);
      formData.append("amount", this.money * obj.zhu);
      return this.$axios.post(this.$store.state.url + "api/lottery/bet", formData, config)   
    },
    spli(str) {
      this.betFun.push(this.bet({con:str,zhu:1}));
      let con = this.conTemp.split(",");
      con.splice(con.lastIndexOf(str), 1);
      con=con.join(",");
      this.conTemp=con;
      this.zhuTemp-=1;
    },
    //投注
    betGo() {
      this.conTemp=this.con;
      this.zhuTemp=this.zhu;
      if(this.$route.query.group=="pk10"||this.$route.query.group=="ssc"){
        this.betFun.push(this.bet({ con: this.conTemp,zhu: this.zhuTemp }));
      }else{
        if (this.con.includes("大") && this.playBonusId!=="n11x5_odd_even_count") {
        this.spli("大");
        }if (this.con.includes("小") && this.playBonusId!=="n11x5_odd_even_count") {
        this.spli("小");
        }if (this.con.includes("单") && this.playBonusId!=="n11x5_odd_even_count") {
        this.spli("单");
        }if (this.con.includes("双") && this.playBonusId!=="n11x5_odd_even_count") {
        this.spli("双");
        }if(this.con.match(/\d/)||this.con.includes("龙")||this.con.includes("虎")||this.playBonusId==="n11x5_odd_even_count"){
        this.betFun.push(this.bet({ con: this.conTemp,zhu: this.zhuTemp }));
        }
      }
      this.$loading.show({ number: "a" });
      this.$store.commit("BET_GO_SHOW", false);
      this.$store.commit("BET_NOT", false);
      this.$axios.all([...this.betFun]).then(
        this.$axios.spread((...res) => {
        let showFlag=true;
        for (let item of res){
          if(item.data.code!=1){
            showFlag=false;
            break;
          }
        }
        if(showFlag){
          this.$store.commit("BET_SUCCESS",true);
        }
        this.betFun=[];
        this.$emit("iscreat");
        this.$store.commit("BET_NOT",true);
        this.$loading.hide();          
        })
      );
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../../../../assets/scss/lotter-list/lotterbet/components/betcBox.scss";
</style>