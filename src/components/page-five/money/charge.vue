<template lang="jade">
.listStyle
  .listStyle-top
    i.iconfont.icon-left(@click='listStyleToPayway')
    p {{title}}充值
    span
  .listStyle-content
    ul.listStyle-VI(v-if='waytype==2')
      li(style='background:#ffeeee;height:42px;')
        p 收款银行
        span {{receiveBankName}}
      li(style='background:#ffeeee;height:42px;')
        p 收款人姓名
        span {{receiveNiceName}}
      li(style='background:#ffeeee;height:46px;')
        p 收款卡号
        span(style='font-size:20px;') {{receiveCard}}
      li(style='background:#ffeeee;height:42px;',v-if='receiveAddress!=""')
        p 收款银行地址
        span {{receiveAddress}}
      li
        p 充值金额
        div
          input(type='number', placeholder='请输入充值金额', v-model='chargeamount', value='chargeamount', clearable='')
      li
        p 充值人姓名
        div
          input(placeholder='请输入充值人姓名', v-model='niceName', value='niceName', clearable='')
      //- li(v-if='attempty==1')
      li
        p {{attfirst}}
        div
          input(:placeholder='attsecond', v-model='checkCode', value='checkCode', clearable='')
      li
        .button
          button.button1(@click="isshow3") 充值申请
      .warning
        p 1、{{attthird}}
        br
        p 2、请正确填写转账银行卡的持卡人姓名和充值金额，以便及时核对。
        br
        p 3、转账1笔提交1次，请勿重复提交订单。
        br
        p
          | 4、请务必转账后再提交订单，否则无法及时查到您的款项！
        br
    ul.listStyle-VI(v-if='waytype==1')
      div(style='text-align:center;height:auto;min-height:160px;')
        img(:src='$store.state.url+this.QRCodeUrl', style='width:40%;height:auto;')
      li
        p 充值金额
        div
          input(type='number', placeholder='请输入充值金额', v-model='chargeamount', value='chargeamount', clearable='')
      li
        p 充值人姓名
        div
          input(placeholder='请输入充值人姓名', v-model='niceName', value='niceName', clearable='')
      //- li(v-if='attempty==1')
      li
        p {{attfirst}}
        div
          input(:placeholder='attsecond', v-model='checkCode', value='checkCode', clearable='')
      li
        .button
          button.button1(@click='isshow3') 充值申请
      //- ul
      //-   li(style='background-color:#ddd;height:40px')
      //-     .button
      //-       button.button1(@click='popup()',style='background-color:#ddd;color:#888') 支付教程
      .warning
        p 1、{{attthird}}
        br
        p 2、请正确填写姓名和充值金额，以便及时核对。
        br
        p 3、转账1笔提交1次，请勿重复提交订单。
        br
        p 4、请务必转账后再提交订单，否则无法及时查到您的款项！
        br
    ul.listStyle-III(v-if='waytype==3')
      li
        p 申请金额
        div
          input(type="number" @focus="parseIntAmount" @blur='parseIntAmount' placeholder='请输入金額', v-model='amount', value='amount', clearable='')
      li
        p 支付渠道
        div
          | {{selectBank}}
      li
        .button
          button.button1(@click='sendReq2()') 支付申请
  actionSheet(v-model='show2',@hide='hide')
    ul.listStyle-II
      li
        .center
          p 您所输入的资讯
      li
        p 充值金额
        span {{chargeamount}}
      li
        p 充值人姓名
        span {{niceName}}
      //- li(v-if='attempty==1')
      li
        p {{attfirst}}
        span {{checkCode}}
      li(style='text-align:center;background:#fff;')
        .center
          p 请确认上列信息正确！
      li
        .button
          button.button2(@click='sendReq()') 确定
          button.button3(@click='show2 = !show2') 取消
  actionSheet.mIcode-go(v-model='show1', :actions='payway', cancel-text='取消',@hide='hide')
  div.show(v-show='show4')
    ul
      .title
        p 温馨提示！
      .cont
        p {{content}}
      .but
        button(@click='goBack()') 确定
</template>
<script>
import actionSheet from "../../public/actionSheet";
export default {
  components: {
    actionSheet
  },
  data() {
    return {
      amount:'', //金額
      waytype:'', //渠道類型
      title:'',  //標題
      chargeamount: "",  //線下充值金額
      niceName: "",   //使用者名稱
      content: "",   //提示窗內容文字
      checkCode: "",  //附言
      show1: false,
      show2: false,
      show4: false,
      QRCodeUrl: "",  //QRcode連結
      receiveAddress: "",
      receiveBankId: "",
      receiveBankName: "",
      receiveCard: "",
      receiveNiceName: "",
      attfirst:"访问点连接中...",
      attsecond:"访问点连接中...",
      attthird:"访问点连接中...",
      selectBank:"连接中..",
      bankUserId:'',  //第三方線上渠道id
      payway: [],   //渠道列表
      jId:"",
      attempty:'',
    };
  },
  mounted() {
    this.rechargeEntrance();
  },
  methods: {
    hide(){
      this.show2=!this.show2;
    },
    //返回payway页面
    listStyleToPayway(){
      this.$router.push("/payway");
    },
    parseIntAmount(){
      if(this.amount !== ''){
        this.amount = parseInt(this.amount);
      }
    },
    //獲取渠道資訊
    rechargeEntrance() {
      this.$axios
        .get(this.$store.state.url + "api/proxy/rechargeEntrance", {
          params: { rechargeWay: this.$route.query.id }
        })
        .then(res => {
          this.waytype = res.data.data.waytype;
          this.title = this.$route.query.alias;
          if (this.waytype === 3) {
            //若waytype為3則為線上支付渠道
            this.sToJ()
            //id轉換判斷
          } else {
          this.attempty = res.data.data.attempty;
          this.QRCodeUrl = res.data.data.QRCodeUrl;
          this.receiveAddress = res.data.data.receiveAddress;
          this.receiveBankId = res.data.data.receiveBankId;
          this.receiveBankName = res.data.data.receiveBankName;
          this.receiveCard = res.data.data.receiveCard;
          this.receiveNiceName = res.data.data.receiveNiceName;
          this.attfirst = res.data.data.attfirst;
          this.attsecond = res.data.data.attsecond;
          this.attthird = res.data.data.attthird;
          }
          
        })
        .catch(error => {
          // this.$pop.show({
          //   title: "温馨提示",
          //   content: "数据加载失败，请重新加载",
          //   number: 6
          // });
          console.log("获取列表Error");
        });
    },
    //執行傳入之js跳轉（線上渠道）
    excecuteJS(){
      setTimeout("document.getElementById('frm1').submit();",100);
    },
    //id轉換判斷 stephen -> jason
    sToJ(){
      if(this.title.match('支付宝') !== null) {
        this.jId = 2;
      } else if (this.title.match('微信') !== null) {
        this.jId = 3;
      } else if (this.title.match('QQ') !== null) {
        this.jId = 4;
      } else {
        this.jId = 1;
      }
      this.getBanks()
    },
    //獲取渠道資訊（線上用）
    getBanks() {
      this.$axios
        .get(this.$store.state.url + "api/pay/thirdparty/getBanks", {
          params: {type: this.jId}
        })
        .then(res => {
          if (this.jId === 1) {
            for (let i = 0; i < res.data.data.length; i++) {
              this.payway.push({
                name: res.data.data[i].name,
                code: res.data.data[i].code,
                callback: this.onClick
              });
            }
          } else {
            this.bankUserId = res.data.data[0].code;
            this.selectBank = res.data.data[0].name;
          }
        })
        .catch(error => {
          // this.$pop.show({
          //   title: "温馨提示",
          //   content: "数据加载失败，请重新加载",
          //   number: 6
          // });
          console.log("获取列表Error");
        });
    },
    goBack() {
      this.$router.push({ path: "/five" });
    },
    isshow3(){
      if(this.chargeamount === ""){
        this.$pop.show({error:'',title:'温馨提示',content:'请输入金额!',content1:'',content2:'',number:2});
      }else if(this.niceName === ''){
        this.$pop.show({error:'',title:'温馨提示',content:'请输入姓名!',content1:'',content2:'',number:2});
      }else{
        this.show2 = !this.show2;
      }
    },
    //線上渠道支付申請
    sendReq2() {
      if (this.amount === "") {
        this.$pop.show({error:'',title:'温馨提示',content:'请输入金額!',content1:'',content2:'',number:2});
      }else if(this.selectBank === "请选择银行"){
        this.$pop.show({error:'',title:'温馨提示',content:'请选择银行卡!',content1:'',content2:'',number:2});
      }else {
        let config = {
          headers: { "Content-Type": "application/x-www-form-urlencoded" },
          withCredentials: true
        };
        let formData = new FormData();
        formData.append("amount", parseInt(this.amount));
        formData.append("bankId", this.bankUserId);
        this.$axios
          .post(
            this.$store.state.url + "api/pay/thirdparty/deposit",
            formData,
            config
          )
          .then(res => {
            if (res.data.code === 1) {
              console.log(res.data.data);
              var z = document.createElement('p');
              z.innerHTML = res.data.data;
              //z.innerHTML='<script>alert("12345")<'+'/script>';
              document.body.appendChild(z);
              this.excecuteJS();
              console.log('成功');
            } else {
              this.content = res.data.content;
              this.$pop.show({error:'',title:'温馨提示',content:res.data.content,content1:'',content2:'',number:2});
            }
          })
          .catch(error => {
            console.log(error);
          });
      }
    },
    //線下支付申請
    sendReq() {
      if(this.attempty==1) {
        if(this.checkCode =="") {
          this.$pop.show({error:'',title:'温馨提示',content:'请依页面提示输入资讯,以利迅速审核',content1:'',content2:'',number:2});
          return false;
        }
      };
      let config = {
        headers: { "Content-Type": "application/x-www-form-urlencoded" },
        withCredentials: true
      };
      let formData = new FormData();
      formData.append("rechargeWay", this.$route.query.id);
      formData.append("receiveBankId", this.receiveBankId);
      formData.append("chargeamount", this.chargeamount);
      formData.append("niceName", this.niceName);
      formData.append("checkCode", this.checkCode);
      this.$axios
        .post(
          this.$store.state.url + "api/proxy/setPayRequest",
          formData,
          config
        )
        .then(res => {
          if (res.data.code === 1) {
            this.content = "申请完成，资讯可至充值信息页面查询。";
            this.show2 = !this.show2;
            this.show4 = !this.show4;
          } else if (res.data.code === 0) {
            this.content = res.data.data.message;
            this.show2 = !this.show2;
            this.$pop.show({error:'',title:'温馨提示',content:res.data.content,content1:'',content2:'',number:2});
          } else {
            this.content = res.data.content;
            this.show2 = !this.show2;
            this.$pop.show({error:'',title:'温馨提示',content:res.data.content,content1:'',content2:'',number:2});
          }
        })
        .catch(error => {
          console.log("setPayApplicationNo");
        });
    }
  }
};
</script>

<style lang="scss" scoped>
@import "../../../assets/scss/listStyle.scss";
@import "../../../assets/scss/popcorn.scss";
@import "../../../assets/scss/page-five/public.scss";
</style>