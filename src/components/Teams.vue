<template>
    <v-app id="teams" v-if="show">
      <h2 style="color:deepskyblue">{{leagueName}}</h2>
 <v-data-table :headers="mainHeaders"
               :items="mainItems"
               item-key="ranking"
               expand
               class="elevation-1"
               dark
               >
  <template slot="items" slot-scope="props">
    <tr @click="props.expanded = !props.expanded; get_team_details(props.item)">
      <td class="text-xs-left">{{ props.item.ranking }}</td>
      <td class="text-xs-left">
        <v-avatar >
          <v-img v-bind:src= "'http://playchamps.in/assets/teamIcons/' + props.item.teamIcon" ></v-img>
        </v-avatar> {{ props.item.teamCode }}
        </td>
      <td class="text-xs-left">{{ props.item.totalMatchesPlayed }}</td>
      <td class="text-xs-left">{{ props.item.netPointsScored }}</td>
      <td class="text-xs-left">{{ props.item.winPercentage }}</td>
      <td class="text-xs-left">{{ props.item.totalScore }}</td>
    </tr>
  </template>
  <template slot="expand" slot-scope="props">
    <v-container grid-list-md text-xs-center>
    <v-layout row wrap>
      <v-flex xs2>
          <!-- <div  id="charts" > -->
          <h3>Net Points Scored :: {{props.item.netPointsScored}}</h3>
          <echart  id="echart1"/>
          <!-- </div> -->
      </v-flex>
      <v-flex xs10>
        <v-card>
          <v-card-text>
            <v-data-table :headers="subHeaders"
                      :items="subItems.teamMatches"
                      item-key="result"
                      class="elevation-10"
                      dark
                      id="expanded_table"
                      >
          <template slot="items" slot-scope="props">
            <td class="text-xs-left">
              <div v-if="props.item.teamOne == props.item.winnerTeam">{{ "Won"}}</div>
              <div v-if="props.item.teamOne != props.item.winnerTeam">{{ "Lost"}}</div>
            </td>
            <td class="text-xs-left">{{ props.item.teamTwo }}</td>
            <td class="text-xs-left"> Suheduled on: {{moment(props.item.matchScheduleDate).format('MMM Do YYYY, h:mma')}} <br> at Court: {{props.item.courtName}}</td>
            <td class="text-xs-left">{{ moment(props.item.matchCompletetionDate).format('MMM Do YYYY, h:mma') }}</td>
            <td class="text-xs-left">{{ props.item.matchScores[0].setOneScore }}</td>
          </template>
        </v-data-table>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
    </v-container>
      
  </template>
 </v-data-table>  </v-app>
</template>

<script>
import axios from 'axios'
import 'echarts/lib/chart/pie';
import echarts from 'echarts'
var moment = require('moment');
export default {
    props : ['pass_data'],
    data () {
    return {
      moment : moment,
      nps : '',
      option :{
            title : {
                left: 50,
                text: '',
                // subtext: 'chart',
                top:'0'
            },
            tooltip : {
                show: true,
                trigger: 'item',
                formatter: "{a} <br/>{b} : {c} ({d}%)"
            },
             legend: {
              orient: 'vertical', // 'vertical'
              x: 'left', // 'center' | 'left' | {number},
              y: 'top', // 'center' | 'bottom' | {number}
              backgroundColor: '#eee',
              borderColor: 'rgba(178,34,34,0.8)',
              borderWidth: 4,
              padding: 5,    // [5, 10, 15, 20]
              itemGap: 2,
              textStyle: {color: 'red'},
            },
            toolbox: {
                show : false,
                feature : {
                    mark : {show: true},
                    dataView : {show: true, readOnly: false},
                    restore : {show: true},
                    saveAsImage : {show: true}
                }
            },
            calculable : true,
            series : [
                {
                    name:'chart',
                    type:'pie',
                    radius : '55%',
                    center: ['50%', '60%'],
                    data:[],
                    labelLine: {
                            show: false
                        },
                    label:{
                            show : false,
                            padding :[10,-20,1,-35]
                        }
                },
            ]
      },
      teamId : '',
      leagueName : '',
      show : true,
     mainHeaders: [
        { text: 'Position', value: 'ranking', sortable : false },
        { text: 'Team', value: 'teamCode', sortable:false },
        { text: 'Matches Played', value: 'totalMatchesPlayed', sortable:false },
        { text: 'NPS', value: 'netPointsScored', sortable:false },
        { text: 'Win%', value: 'winPercentage', sortable:false },
        { text: 'Score', value: 'totalScore', sortable:false }
      ],
      subHeaders: [
        { text: 'Result', value: 'result', sortable : false},
        { text: 'Opponent Team', value: 'teamOne', sortable : false },
        { text: 'Match Details', value: 'matchScheduleDate', sortable : false },
        { text: 'Completes On', value: 'matchCompletetionDate', sortable : false },
        { text: 'Match Score', value: 'match_score', sortable : false },
      ],
      mainItems: [],
      subItems: []
    }
  },
  methods:{
        closeform(){
                this.show=false
        },
        get_team_details(a){
          this.teamId = a.teamId
          axios.get('http://api.playchamps.in/api/matches/leaderboard/' + this.pass_data + '/' + this.teamId)
          .then((res)=>{
            this.nps = res.data.netPointsScored
            this.subItems=res.data
            for(var i=0; i< res.data.teamMatches.length ; i++){
            if (res.data.teamCode === res.data.teamMatches[i].teamTwo){
              this.subItems.teamMatches[i].teamTwo = res.data.teamMatches[i].teamOne 
              this.subItems.teamMatches[i].teamOne = res.data.teamCode
            }
            if (res.data.teamCode === res.data.teamMatches[i].teamOne) {
              this.subItems.teamMatches[i].teamTwo = res.data.teamMatches[i].teamTwo
              this.subItems.teamMatches[i].teamOne = res.data.teamCode
            } 
            }
            var chart = echarts.init(document.getElementById('echart1'));
            if (chart) {
              if(res.data.totalWins == '0' && res.data.totalMatchesPlayed == '0'){
                this.option.series[0].data = [{value : '100' ,name:'Nil',itemStyle: {color: 'black'}}]
                chart.setOption(this.option)
              }
              else{
                this.option.series[0].data = [{value : res.data.totalWins ,name:'Win',itemStyle: {color: 'green'}},{value : (res.data.totalMatchesPlayed-res.data.totalWins), name : 'Lost',itemStyle: {color: 'red'}}]
                chart.setOption(this.option)
              }
            }
          })
          
        }
  },
  mounted: function (){
    axios.get('http://api.playchamps.in/api/leagues/leaderboard/'+ this.pass_data)
    .then((res)=>{
     this.mainItems= res.data.groups[0].teams
     this.leagueName = res.data.leagueName
    })
  }
}
</script>

<style>
/* .theme--dark.v-table tbody td, .theme--light.v-table tbody th{
    color: #7CFC00;
}
.theme--dark.v-table {
    background-color: transparent;
}
.theme--dark.v-table thead td, .theme--light.v-table thead th{

} */
#teams{
  background-color: #424242;
}
#charts{
    left: 0%;
    margin: 25px 0px;
    /* padding: auto auto; */
    height :185px;
    width:75%;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
}
#echart1{
    width: 150px;
    height: 150px;
    left: 0%;
    top: -15px;
}
#expanded_table{
  /* margin-left: 20%; */
  /* margin-top: -22%; */
}
#avatar{
  max-height:400px;
}
</style>
