<style lang="less">
  @import url(~assets/css/variable.less);
  @__: ;
  #page-account {
    display:flex; flex-direction:column;
    // .bg(@__,'~assets/img/funds/bg-account',@__,@__,@__);
    .bg-area {
      color:#fff;
      background:url(~assets/img/funds/bg-account.png) no-repeat center/cover;
    }
    .placeholder { flex:1; }
    #user-info {
      // display:flex;
      line-height:35px;
      text-align:right;
    }
    #account-info {
      margin:40px 25px 60px 25px;
      line-height:30px;
      font-size:14px;
      h2 span { font-size:18px; color:@color-warning; }
    }
    .btn-group { 
      display:flex; 
      padding:10px;
      .btn { flex:1; margin:6px; .radius(); }
      .btn:nth-child(2) { .bgc(@color-warning); color:#fff; }
      // .btn:first-child { margin-right:1px; }
    }
    #tips { 
      padding:25px;
      p { margin-bottom:10px; line-height:1.6; }
    }
  }
</style>

<template>
  <div id="page-account">
    <div class="bg-area">
      <div id="user-info">
        <!-- <p>{{id}}</p> -->
        {{name}}
        <input type="button" class="btn text primary" value="退出登录" @click="logout">
      </div>
      <div id="account-info">
        <h2>我的钱包余额 : <span>{{balance}} ETH ( ≈ {{cny}}￥ )</span></h2>
        <h2>邀请各获30VPP : <span>{{mobile}}</span></h2>
      </div>
    </div>

    <div class="btn-group">
      <input type="button" class="btn block primary" value="转入" @click="$router.push('/funds/recharge')"/>
      <input type="button" class="btn block warning" value="提现" @click="$router.push('/funds/withdraw')"/>
    </div>
    <div id="tips">
      <p>1. 请勿向该地址充值除ETH之外的资产，任何充入ETH地址的非ETH资产将不可找回。</p>
      <p>2. ETH充值需要网络确认才能到账。具体到账时间将以网络繁忙度而定，一般在30min以内。</p>
      <p>3. 最小充值金额为0.1 ETH</p>
    </div>
    <div class="placeholder"></div>
    <h3 class="website">
      <a href="http://valp.io?lang=zh-tw" class="text-left">官网 http://valp.io</a>
      <a href="https://t.me/ValPromise" class="text-right">电报群 https://t.me/ValPromise</a>
    </h3>
  </div>
</template>

<script>
export default {
  layout:'funds',
  data () {
    return {
      id:'',
      name:'',
      balance:0,
      mobile:'',
      cny:0,
    }
  },
  methods: {
    logout() {
      if ( this.blockRequest ) return;
      this.blockRequest = true;
      this.$http.post('/customer/loginOut', {token:localStorage.getItem('token')})
      .then(resp=>{
        this.blockRequest = false;
        resp=resp.data;
        if ( resp.state !== 1 ) return this.$store.commit('showMessageDialog', {
          type:'failure', text:'退出登录失败'}
        );
        this.$store.commit('showMessageDialog', {type:'failure', text:'退出登录成功, 即将跳转'});
        localStorage.setItem('token', '');
        setTimeout(()=>{
          this.$router.push('/funds');
        }, 1000);
      })
      .catch(err=>{
        this.blockRequest = false;
      })
    }
  },
  mounted() {
    if ( !localStorage.getItem('token') ) {
      this.$router.replace('/funds/login');
      return;
    }

    this.$http.post('/customer/customerDetail', {token:localStorage.getItem('token')})
    .then(resp=>{
      resp=resp.data;
      if ( resp.state !== 1 ) return this.$store.commit('showMessageDialog', {
        type:'failure', 
        text:resp.message
      });
      this.id = resp.data.id;
      this.name = resp.data.name;
      this.mobile = resp.data.mobile;
      this.balance = resp.data.balance;
      this.cny = resp.data.cny;
    })
  }
}
</script>
