<template>
  <div>
  <navbar></navbar>
  <div class="container-fluid">
    <div class="row nav-margin">
      <div class="col-12 sub-head">
        <p style="font-size: 40px;">รวมแมตช์การแข่งขันที่ดีที่สุดสำหรับคุณ</p>
        <p style="font-size: 18px;">รวมการแข่งขันฟุตบอลหลากหลายรายการทั่วไทย</p>
      </div>
    </div>
    <div class="container">




<div class="row">
        <div class="col-12">
          <div class="showtype-bar">
            <div class="showtype-title">
              แมตช์<span>ที่คุณเข้าร่วม</span>
            </div>
          </div>
        </div>
        <div class="col-4" v-for="value in matchjoin"> <!-- type 1 -->
          <div class="show-box">
            <div class="red-box">
              <div class="red-box-title">
                <h2><strong>{{value.matchname}} </strong></h2>
              </div>
              <div class="red-box-owner">
                <h5><span>จัดโดย</span> <strong>{{value.matchowner}}</strong></h5>
              </div>  
            </div>
            <div class="white-box">
              <div class="white-box-location">
                <h6>สถานที่จัดงาน</h6>
                <h5><strong>{{value.match_location}}</strong></h5>
              </div>
              <div class="box-detail-bt">

                <router-link :to="{ name: 'MatchDetailPage',
                  query: { match_id: value.match_id} }">ดูรายละเอียด</router-link>
              </div>
            </div>
          </div>
        </div>
      </div>





    </div>
  </div>


</div>
</template>

<script>
import Navbar from '@/components/Navigation'
import router from "../router";
import axios from "axios";

var accountObj;

export default {
  name: "Showtime",
  components: {
    Navbar: Navbar
  },
  beforeCreate() {
    accountObj = JSON.parse(localStorage.getItem('account'));
    if (!accountObj){
      router.push({ name: "Login" });
    }
  },
  created() {
    document.title =
      ".:: การแข่งขันที่คุณเข้าร่วม - ระบบ Matching! | จัดแข่งกีฬาฟุตบอล ::.";
      this.setAccout();
      this.getMatchJoin();
    
  },
  data() {
    return {
      username : [],
      matchjoin : [],
      account: []
    };
  },
  methods: {

    setAccout(){
      this.account = accountObj;
    },
    getMatchJoin() {
      var joinusername = accountObj.username;
      const path = "http://35.240.225.238:3001/api/matchgateway/match/join/"+joinusername;
      axios
        .get(path)
        .then(res => {
              var joinArray = res.data;
          for (var joinIndex in joinArray) {
            this.matchjoin.push({
              match_id: joinArray[joinIndex].match_id,
              matchname: joinArray[joinIndex].matchname,
              matchowner: joinArray[joinIndex].matchowner,
              match_desc: joinArray[joinIndex].match_desc,
              match_location: joinArray[joinIndex].match_location,
              match_status: joinArray[joinIndex].match_status,
              match_size: joinArray[joinIndex].match_size,
            });
          }      

        })
        .catch(error => {
          console.log(error);
        });

    }


}




};
</script>

<style lang="scss" scoped>
.nav-margin{
  height: 300px;
}
.sub-head{
    padding-top: 100px;
    background-image: url(/static/img/sub-head-bg.28cb331.png);
    background-attachment: fixed;
    background-size: cover;
    text-align: center;
    color: #fff;
}
.show-box{
  margin-top: 2rem;
  border-radius: .5rem;
  display: grid;
  box-shadow: 0px 5px 20px rgba($color: #000000, $alpha: 0.2);
}
.red-box{
  background-image: url('../assets/img/card-bg.png');
  background-size: 200%;
  background-position: center;
  padding: 2rem;
  border-radius: .5rem .5rem 0rem 0rem;
  color: #fff;
}
.white-box{
  padding: 2rem;
}
.white-box-location{
  float: left;
}
.box-detail-bt{
  float: right;
  margin-top: 1rem;
  a{
      text-decoration: none;
      color: #ff7171;
  }
}
.showtype-bar{
    display: flow-root;
    padding-bottom: 1rem;
    border-bottom: 1px solid rgba($color: #000000, $alpha: 0.1);
    margin-top: 3rem;
}
.showtype-title{
  font-size: 2.5rem;
  float: left;
  span{
    color: #C90B0B;
  }
}
.detail-bt{
  float: right;
}
.red-box-owner{
    span{
        color: rgba($color: #fff, $alpha: 0.5)
    }
}
</style>

