<template>
  <div>
    <navbar></navbar>

  <div class="container-fluid">
    <div class="row sub-head">
      <div class="container">
        <div class="row" v-for="(value, name) in match_detail">
          <div class="col-9 ">
            <div class="match-title text-bold">{{value.matchname}}</div>
          </div>
          <div class="col-3 align-right">
              <span v-for="(value,name) in match_detail">  
                <router-link  tag="button" :to="{name: 'MatchManagement',query: { match_id: value.match_id}  }" class="success-button match-bt">
                  จัดการการแข่ง
                </router-link>
              </span>
          </div>
          <div class="col-12">
            <div class="match-date align-left">
              <h3 class="text-normal">รายละเอียดการแข่งขัน</h3><h5>{{value.match_desc}}</h5>
              <br>
            </div>
          </div>
          <div class="col-3">
            <div class="match-location align-left">
              <h6>สถานที่จัดงาน</h6>
              <h4 class="text-bold"><strong>{{value.match_location}}</strong></h4>
            </div>
          </div>
          <div class="col-3">
            <div class="match-date align-left">
              <h5>สถานะการแข่ง {{value.match_status}}</h5>
            </div>
          </div>
          <div class="col-3">
            <div class="match-date align-left">
              <h5>จำนวนทีมที่รับสมัคร {{value.match_size}}</h5>
            </div>
          </div>
          <div class="col-3">
            <div class="match-owner align-right">
              <h3><span>จัดโดย</span> <strong>{{value.matchowner}}</strong></h3>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="container">
      <div class="row mt-5">
        <div class="col-12 align-center">
          <h1>รายชื่อทีม</h1>
        </div>
        <div class="col-12">
          <table class="table table-dark">
            <thead>
              <tr>
                <th scope="col">ชื่อทีม</th>
                <th scope="col">เจ้าของทีม</th>
                <th scope="col">ข้อมูลติดต่อ</th>
              </tr>
            </thead>

            <tbody v-for="value in match_team"> 
              <tr>
                <td>{{value.team_name}}</td>
                <td>{{value.team_owner}}</td>
                <td>{{value.team_contact}}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <div class="container">
      <div class="row mt-5">
        <div class="col-12 align-center">
          <h1>ตารางการแข่งขัน</h1>
        </div>
        <div class="col-12">
          <table class="table table-dark">
            <thead>
              <tr>
                <th scope="col">Date</th>
                <th scope="col">Home</th>
                <th scope="col"></th>
                <th scope="col">vs</th>
                <th scope="col"></th>
                <th scope="col">Away</th>
                <th scope="col">Time</th>
              </tr>
            </thead>
            
            <tbody>
              <tr v-for="value in match_schedule">
                <td>{{value.date}}/{{value.month}}/{{value.year}}</td>
                <td>{{value.team_1_name}}</td>
                <td>{{value.team_1_score}}</td>
                <td>-</td>
                <td>{{value.team_2_score}}</td>
                <td>{{value.team_2_name}}</td>
                <td>{{value.hr}}:{{value.min}}</td>
              </tr>
            </tbody>
          </table>
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
  name: "MatchDetailPage",
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
      ".:: Home - ระบบ Matching! | จัดแข่งกีฬาฟุตบอล ::.";
      this.match_id = this.$route.query.match_id;
      this.getMatchDetail();
      this.GetTeam();
      this.GetSchedule();
      this.status = this.$route.query.status;
      console.log(status);
  },
  data() {
    return {
      match_detail : [],
      match_id: null,
      match_team : [],
      match_schedule : [],
      status: null
    };
  },
  methods: {
    async getMatchDetail(){
      const path = "http://35.240.225.238:3001/api/matchgateway/match/matchid/"+this.match_id;
      console.log(path)
      /*axios
        .get(path)
        .then(res => {
          var matchdetail = res.data;
          this.match_detail.push({
            match_id: matchdetail.match_id,
            matchname: matchdetail.matchname,
            matchowner: matchdetail.matchowner,
            match_desc: matchdetail.match_desc,
            match_location: matchdetail.match_location,
            match_status: matchdetail.match_status,
            match_size: matchdetail.match_size,
           });
        })
        .catch(error => {
          console.log(error);
        }); */
      const result = await axios.get(path);
      this.match_detail = result.data
    },
    async GetTeam(){
      const path = "http://35.240.225.238:3001/api/processgateway/process/"+this.match_id+"/allteam";
      const result = await axios.get(path);
      this.match_team = result.data
    },
    async GetSchedule(){
      const path = "http://35.240.225.238:3001/api/processgateway/process/"+this.match_id+"/allschedule";
      const result = await axios.get(path);
      this.match_schedule = result.data
    },
    setAccout(){
      this.account = accountObj;
    }
}
};
</script>

<style lang="scss" scoped>
table{
  text-align: center;
}
.sub-head{
    padding: 3rem;
    background-image: url('../assets/img/sub-head-bg.png');
    background-attachment: fixed;
    background-size: cover;
    color: #fff;
}
.match-title{
  font-size: 5rem;
  line-height: 5rem;
  margin-bottom: 2rem;
}
.match-bt {
  border: none;
  border-radius: 50px;
  padding: 15px;
  background-color: #C90B0B;
  color: #fff;
  &#regis-link {
    width: 100%;
  }
}
</style>