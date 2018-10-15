<template lang="jade">
.looterAll
  ul
    router-link(v-for='(item,index) in lotteryListAll', :key='index', tag='li', :to="{path:'/'+item.groupId,query:{id:item.id,name:item.name,group:item.groupId}}")
      span
        i.iconfont(:class='"icon-"+item.groupId')
      h3 {{item.name}}
      h4(style='color:#ccc;') {{item.time}}
</template>
<script>
export default {
  data() {
    return {
      lotteryListAll: ''
    };
  },
  mounted(){
    this.lotter();
  },
  methods:{
    lotter(){
      if(JSON.parse(localStorage.getItem("lotteryListAll")) !== null){
        this.lotteryListAll = JSON.parse(localStorage.getItem("lotteryListAll"));
      }else {
        this.$axios.get(this.$store.state.url + "api/lottery/getLotteryList",{params:{type:"all"}}).then(res => {
          this.lotteryListAll = res.data.data;
          for (let i = 0; i < this.lotteryListAll.length; i++) {
            this.lotteryListAll[i]["time"] = time[this.lotteryListAll[i].id];
          }
          localStorage.setItem("lotteryListAll",JSON.stringify(this.lotteryListAll));
        })
        .catch(error => {
          console.log("getLotteryListNo");
        });
      }
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../../assets/scss/lotter-list/lotterAll.scss";
</style>