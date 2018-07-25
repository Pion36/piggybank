<template>
  <div class='piggy'>
    <div class="header">
      <section class="hero is-default is-bold">
        <div class="hero-head">
          <nav class="navbar">
            <div class="container">
              <div class="navbar-brand">
                <a class="navbar-item title" href="../">
                  Piggy Bank (α版)
                </a>
                <span class="navbar-burger burger" data-target="navbarMenu">
                  <span></span>
                  <span></span>
                  <span></span>
                </span>
              </div>
              <!-- <div id="navbarMenu" class="navbar-menu">
                <div class="navbar-end">
                  <div class="tabs is-right">
                    <ul>
                      <li class="is-active"><a>Home</a></li>
                      <li><a href="">使い方</a></li>
                      <li><a href="">当サイトについて</a></li>
                      <li><a href="">CONTACT</a></li>
                      <li><a href="">Help</a></li>
                    </ul>
                  </div>
                </div>
              </div> -->
            </div>
          </nav>
        </div>
      </section>
    </div>
    <div class="container">
      <div class="columns">
        <div class="column is-5">
          <div class="box" style="text-align:left;">
            <h2 class="subtitle">Pig(貯金箱)を作る</h2>
            <p>Pigの名前(貯金の目的や、可愛い名前でも！)</p>
            <input v-model="name" placeholder="名前を入れてね">
            <p>貯金の目標額(いくら貯めますか？)</p>
            <input v-model="target" placeholder="目標額を記入" number> Ether
            <p>最初にいくら入れますか？</p>
            <input v-model="amount" placeholder="投入額を記入" number> Ether
            <p>(オプション)</p>
            <p>どのくらいの期間で貯金する予定ですか？(デフォルトだと1年です)</p>
            <select v-model="limit">
              <option v-for="(limoption, index) in limoptions" :key="index" v-bind:value="limoption.value">
                {{ limoption.text }}
              </option>
            </select>
            <p>本気度%(デフォルトだと0%です)</p>
            <select v-model="penalty" number>
              <option>0</option>
              <option>5</option>
              <option>10</option>
              <option>25</option>
              <option>50</option>
              <option>75</option>
              <option>100</option>
            </select>
            <p>設定した期間までに貯金できなかった場合に、Pigの中の残金のうち、設定したパーセンテージが投げ銭としてPiggy Bank作成者に送られます。</p>
            <a class="button is-info is-outlined" v-if="web3.networkId == 1" v-on:click="MakeModal = true">作成</a>
            <p v-if="web3.networkId != 1" style="color:red">MainNetでの接続をお願いします。</p>
          </div>
        </div>
        <div class="column is-7">
          <div class="box">
            <h2 class="subtitle">あなたのPigs</h2>
            <div id="#pigs">
              <div v-for="(value, index) in Pigglist" :key="index">
                <div v-if="index % 3 == 0" class="columns">
                  <div class="column is-4 is-desktop box" style="margin:0 0px 12px 0px;">
                    <p>{{ Pigglist[index].name }}</p>
                    <img src="../assets/money_chokinbako.png">
                    <p>目標額:{{ Pigglist[index].target }} Ether</p>
                    <p>達成率:{{ Pigglist[index].amount / Pigglist[index].target * 100}}%</p>
                    <a class="button is-info is-outlined" v-on:click="DetailPigModal = true, showname = Pigglist[index].name, showindex = Pigglist[index].id, showtarget = Pigglist[index].target, showamount = Pigglist[index].amount, showlimit = Pigglist[index].limittime, showpenalty = Pigglist[index].penalty">詳細</a>
                  </div>
                  <div v-if="Pigglist[index+1]" class="column is-4 is-desktop box" style="margin:0 0px 12px 0px;">
                    <p>{{ Pigglist[index+1].name }}</p>
                    <img src="../assets/money_chokinbako.png">
                    <p>目標額:{{ Pigglist[index+1].target }} Ether</p>
                    <p>達成率:{{ Pigglist[index+1].amount / Pigglist[index+1].target * 100}}%</p>
                    <a class="button is-info is-outlined" v-on:click="DetailPigModal = true, showname = Pigglist[index+1].name, showindex = Pigglist[index+1].id, showtarget = Pigglist[index+1].target, showamount = Pigglist[index+1].amount, showlimit = Pigglist[index+1].limittime, showpenalty = Pigglist[index+1].penalty">詳細</a>
                  </div>
                  <div v-if="Pigglist[index+2]" class="column is-4 is-desktop box" style="margin:0 0px 12px 0px;">
                    <p>{{ Pigglist[index+2].name }}</p>
                    <img src="../assets/money_chokinbako.png">
                    <p>目標額:{{ Pigglist[index+2].target }} Ether</p>
                    <p>達成率:{{ Pigglist[index+2].amount / Pigglist[index+2].target * 100}}%</p>
                    <a class="button is-info is-outlined" v-on:click="DetailPigModal = true, showname = Pigglist[index+2].name, showindex = Pigglist[index+2].id, showtarget = Pigglist[index+2].target, showamount = Pigglist[index+2].amount, showlimit = Pigglist[index+2].limittime, showpenalty = Pigglist[index+2].penalty">詳細</a>
                  </div>
                </div>
              </div>
            </div>
            <a class="button is-info is-outlined" v-on:click='displayPigs(web3.coinbase)'>表示</a>
          </div>
        </div>
      </div>
    </div>
    <b-modal :active.sync="DetailPigModal">
      <div class="modal-card" style="width: auto">
           <header class="modal-card-head">
             <p class="modal-card-title">{{ showname }}</p>
           </header>
           <section class="modal-card-body">
             <img src="../assets/money_chokinbako.png">
             <p>目標額:{{ showtarget }} Ether</p>
             <p>合計:{{ showamount }} Ether</p>
             <p>達成率:{{ showamount / showtarget * 100}}%</p>
             <p>本気度:{{ showpenalty }}%</p>
             <p>期限：{{unixtodate(showlimit)}}</p>
             <p v-if="unixcurrent() <= showlimit * 1000">期限内です</p>
             <p v-if="unixcurrent() > showlimit * 1000" style="color:red;">期限を超えてしまいました</p>
           </section>
           <footer class="modal-card-foot">
             <a class="button is-info is-outlined" v-if="unixcurrent() <= showlimit * 1000 && showamount / showtarget * 100 < 100" v-on:click="DepositModal = true, exertid = showindex,exertname = showname">貯金</a>
             <a class="button is-info is-outlined" v-if="showamount / showtarget * 100 >= 100 && WithdrawActive" v-on:click="WithdrawModal = true, exertid = showindex,exertname = showname">割る</a>
             <a class="button is-info is-outlined" v-if="showamount / showtarget * 100 < 100 && WithdrawActive" v-on:click="PenaltyWithdrawModal = true, exertid = showindex,exertname = showname">割る</a>
             <button class="button" type="button" @click="DetailPigModal = false">閉じる</button>
           </footer>
       </div>
    </b-modal>
    <b-modal :active.sync="MakeModal">
      <div class="modal-card" style="width: auto">
           <header class="modal-card-head">
             <p class="modal-card-title">以下の条件で貯金箱を作成します。</p>
           </header>
           <section class="modal-card-body">
             <p>名前: {{ this.name }}</p>
             <p>目標額: {{ this.target }} Ether</p>
             <p>初期投入額: {{ this.amount }} Ether</p>
           </section>
           <footer class="modal-card-foot">
             <button class="button" type="button" @click="make()">作成実行</button>
             <button class="button" type="button" @click="MakeModal = false">閉じる</button>
           </footer>
       </div>
    </b-modal>
    <b-modal :active.sync="DepositModal">
      <div class="modal-card" style="width: auto">
           <header class="modal-card-head">
             <p class="modal-card-title">PiggにEtherを入れます</p>
           </header>
           <section class="modal-card-body">
             <p>名前: {{exertid}}:{{ exertname }}</p>
             <input v-model="amount" placeholder="目標額を記入" number> Ether
           </section>
           <footer class="modal-card-foot">
             <button class="button" type="button" @click="deposit(exertid,amount)">Pigに入れる</button>
             <button class="button" type="button" @click="DepositModal = false">閉じる</button>
           </footer>
       </div>
    </b-modal>
    <b-modal :active.sync="WithdrawModal">
      <div class="modal-card" style="width: auto">
           <header class="modal-card-head">
             <p class="modal-card-title">Pigを割ってEtherを引き出します</p>
           </header>
           <section class="modal-card-body">
             <p>名前: {{ exertname }}</p>
             <p>Pigいっぱいにお金が溜まりましたね。おめでたいです！</p>
           </section>
           <footer class="modal-card-foot">
             <button class="button" type="button" @click="withdraw(exertid)">Pigを割る</button>
             <button class="button" type="button" @click="WithdrawModal = false">閉じる</button>
           </footer>
       </div>
    </b-modal>
    <b-modal :active.sync="PenaltyWithdrawModal">
      <div class="modal-card" style="width: auto">
           <header class="modal-card-head">
             <p class="modal-card-title">Pigを割ってEtherを引き出します</p>
           </header>
           <section class="modal-card-body">
             <p>名前: {{ exertname }}</p>
             <p v-if="unixcurrent() <= showlimit * 1000">目標額溜まってないですが本当に割りますか？</p>
             <p v-if="unixcurrent() > showlimit * 1000">期限をすぎてしまったため、投げ銭が発生します。</p>
           </section>
           <footer class="modal-card-foot">
             <button class="button" type="button" @click="penaltywithdraw(exertid)">Pigを割る</button>
             <button class="button" type="button" @click="PenaltyWithdrawModal = false">閉じる</button>
           </footer>
       </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'Top',
  data () {
    return {
      name: null,
      target: null,
      limoptions: [
        {text: '10分', value: '600'},
        {text: '1週間', value: '604800'},
        {text: '1ヶ月', value: '2592000'},
        {text: '3ヶ月', value: '7776000'},
        {text: '6ヶ月', value: '15552000'},
        {text: '1年', value: '31536000'}
      ],
      limit: 31536000,
      amount: null,
      penalty: 0,
      Pigglist: [],
      showindex: null,
      showname: null,
      showtarget: null,
      showamount: null,
      showlimit: null,
      showpenalty: null,
      WithdrawActive: true,
      DetailPigModal: false,
      MakeModal: false,
      DepositModal: false,
      WithdrawModal: false,
      PenaltyWithdrawModal: false,
      exertid: null,
      exertname: null
    }
  },
  computed: {
    web3 () {
      return this.$store.state.web3
    }
  },
  mounted () {
    this.$store.dispatch('getContractInstance')
    var burger = document.querySelector('.burger')
    var menu = document.querySelector('#' + burger.dataset.target)
    burger.addEventListener('click', function () {
      burger.classList.toggle('is-active')
      menu.classList.toggle('is-active')
    })
    console.log(this.web3.coinbase)
    this.displayPigs(this.web3.coinbase)
  },
  methods: {
    unixcurrent: function () {
      var current = Date.now()
      return current
    },
    unixtodate: function (unixtime) {
      var d = new Date(unixtime * 1000 + 32400)
      var year = d.getFullYear()
      var month = d.getMonth() + 1
      var day = d.getDate()
      var hour = ('0' + d.getHours()).slice(-2)
      var min = ('0' + d.getMinutes()).slice(-2)
      var sec = ('0' + d.getSeconds()).slice(-2)
      return (year + '-' + month + '-' + day + ' ' + hour + ':' + min + ':' + sec)
    },
    getPigDetails: function (id) {
      var self = this
      return new Promise(function (resolve, reject) {
        self.$store.state.contractInstance().pigs.call(id, function (err, result) {
          if (!err) {
            console.log(result)
            resolve(result)
          }
        })
      })
    },
    pigToOwner: function (id) {
      this.$store.state.contractInstance().pigToOwner.call(id, function (err, result) {
        if (!err) {
          console.log(result)
          return result
        }
      })
    },
    getPigByOwner: function (owner) {
      var self = this
      return new Promise(function (resolve, reject) {
        self.$store.state.contractInstance().getPigByOwner.call(owner, function (err, result) {
          if (!err) {
            var array = new Array(result.length)
            for (var i = 0; i < result.length; i++) {
              array[i] = result[i].c[0]
            }
            return array
          }
        })
      })
    },
    displayPigs: function (owner) {
      var self = this
      self.Pigglist = []
      Promise.resolve(owner)
        .then(function (value) {
          return new Promise((resolve, reject) => { //  getPigByOwner
            self.$store.state.contractInstance().getPigByOwner.call(owner, function (err, result) {
              if (!err) {
                var array = new Array(result.length)
                for (var i = 0; i < result.length; i++) {
                  array[i] = result[i].c[0]
                }
                resolve(array)
              }
            })
          })
        })
        .then(function (array) {
          return Promise.all([
            new Promise((resolve, reject) => {
              var loop = Promise.resolve()
              for (var i = 0; i < array.length; i++) {
                (function (i) {
                  loop = loop.then(function () {
                    return new Promise(function (resolve, reject) {
                      self.$store.state.contractInstance().pigs.call(array[i], function (err, result) {
                        if (!err) {
                          var kari = {}
                          kari['name'] = result[0]
                          kari['target'] = result[1].c[0] / 10000
                          kari['amount'] = result[2].c[0] / 10000
                          kari['starttime'] = result[3].c[0]
                          kari['limittime'] = result[4].c[0]
                          kari['penalty'] = result[5].c[0]
                          kari['id'] = array[i]
                          self.Pigglist.push(kari)
                          // オブジェクトを作って
                          // そのオブジェクトを配列の中に入れていく
                          resolve()
                        }
                      })
                    })
                  })
                })(i)
              }
              console.log('表示')
              resolve()
            })
          ])
        })
    },
    make: function () {
      var self = this
      Promise.resolve()
        .then(function () {
          return new Promise((resolve, reject) => {
            self.$store.state.contractInstance().makepig(self.name, self.target * 1000000000000000000, self.limit, self.penalty, {
              gas: 300000,
              value: self.$store.state.web3.web3Instance().toWei(self.amount, 'ether'),
              from: self.$store.state.web3.coinbase
            }, (err, result) => {
              if (err) {
                console.log(err)
              } else {
                self.name = null
                self.target = null
                self.limit = null
                self.amount = null
                self.penalty = null
                resolve()
              }
            })
          })
        })
      self.MakeModal = false
    },
    deposit: function (id, amount) {
      console.log(id)
      console.log(amount)
      var self = this
      Promise.resolve()
        .then(function () {
          return new Promise((resolve, reject) => {
            self.$store.state.contractInstance().deposit(id, {
              gas: 300000,
              value: self.$store.state.web3.web3Instance().toWei(amount, 'ether'),
              from: self.$store.state.web3.coinbase
            }, (err, result) => {
              if (err) {
                console.log(err)
              } else {
                console.log(self.amount)
                self.amount = null
                resolve()
              }
            })
          })
        })
      self.exertid = null
      self.exertname = null
      self.DepositModal = false
      self.DetailPigModal = false
      console.log('貯金する')
    },
    withdraw: function (id) {
      this.WithdrawActive = false
      console.log(id)
      var self = this
      Promise.resolve()
        .then(function () {
          return new Promise((resolve, reject) => {
            self.$store.state.contractInstance().withdraw(id, {
              gas: 300000,
              value: 0,
              from: self.$store.state.web3.coinbase
            }, (err, result) => {
              if (err) {
                console.log(err)
              } else {
                self.WithdrawActive = true
                resolve()
              }
            })
          })
        })
      self.exertid = null
      self.exertname = null
      self.WithdrawModal = false
      self.DetailPigModal = false
      console.log('引き出す')
    },
    penaltywithdraw: function (id) {
      console.log(id)
      var self = this
      Promise.resolve()
        .then(function () {
          return new Promise((resolve, reject) => {
            self.$store.state.contractInstance().penaltywithdraw(id, {
              gas: 300000,
              value: 0,
              from: self.$store.state.web3.coinbase
            }, (err, result) => {
              if (err) {
                console.log(err)
              } else {
                resolve()
              }
            })
          })
        })
      self.exertid = null
      self.exertname = null
      self.PenaltyWithdrawModal = false
      self.DetailPigModal = false
      console.log('引き出す')
    }
  }
}
</script>

<style>
.title {
  color:white;
}
</style>

<!-- Add 'scoped' attribute to limit CSS to this component only -->
<style scoped>
</style>
