<template>
  <v-app id="inspire" >
    <div @click="closeform">
    <v-toolbar color="deep-purple darken-4" dark fixed app>
      <v-toolbar-title><div id="logo"><img src= "./logo.jpg"/></div></v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon>
      <v-icon>fa fa-search</v-icon>
      </v-btn>
      <v-btn color="deep-purple darken-4" dark>
        Login
      </v-btn>
    </v-toolbar>
    
    <v-content id="bg-img" style="background-image: url('https://image.shutterstock.com/image-photo/set-badminton-shuttlecock-feather-professional-450w-1030859371.jpg');" @click="closeform" >
    <v-container grid-list-md text-xs-center class="bg-text" @click="closeform"> 
      <v-layout row wrap>
      <v-flex xs4>
        <h2>Manage Badminton League/Tournaments</h2>
        <pre>     </pre>
        <p>
          Register for free and try our features to schedule, manage and monitor badminton leagues in few easy steps online, PlayChamps is built for computer, Tablets and mobiles.
        </p>
      </v-flex>
      <v-flex xs4>
        <h3>Top Ranking Teams</h3>
        <pre> </pre>
        <v-data-table
          light
          hide-actions
          hide-headers
          :headers="top_ranking_headers"
          :items="top_ranks_data"
          id = "top_ranking"
        >
          <template slot="items" slot-scope="props">
            <tr style="height: 1%;">
            <td class="text-xs-right">{{props.item.rank}}</td>
            <td class="text-xs-left">{{ props.item.name }}</td>
            </tr>
          </template>
        </v-data-table>
      </v-flex>
      <v-flex xs4>
        <h3>Top Ranking Players</h3>
        <pre> </pre>
        <v-layout
          align-center
          justify-space-around
          wrap
        >
        <div  v-for="item in ['https://cdn.vuetifyjs.com/images/lists/3.jpg']" :key="item">
          <v-avatar color="indigo">
          <img
            :src= item
          >
        </v-avatar>
        </div>
        
        </v-layout>
      </v-flex>
      <v-flex xs12 @click.stop>
        <v-card id="leagues_table">
        <v-card-title>
          Leagues
          <v-spacer></v-spacer>
          <v-text-field
            v-model="search"
            append-icon="search"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
         <v-data-table
          :headers="headers"
          :items="leagues_data"
          class="elevation-1"
          :search="search"
        >
          <template slot="items" slot-scope="props">
            <tr @click="showTeams(props.item)">
            <td class="text-xs-left">{{ props.item.name }}</td>
            <td class="text-xs-left">{{ props.item.organizer }}</td>
            <td class="text-xs-left">{{ props.item.venue }}</td>
            <td class="text-xs-left">{{ props.item.leagueType }}</td>
            <td class="text-xs-left">{{ props.item.city }}</td>
            <td class="text-xs-left">{{ props.item.status }}</td>
            <td class="text-xs-left">{{ moment(props.item.startDate).format('MMM Do YYYY, h:mma') }}</td>
            <td> <div @click.stop> <v-btn small round color="primary" dark @click="register()">Register</v-btn></div></td>
            </tr>
          </template>
          <v-alert slot="no-results" :value="true" color="error" icon="warning">
            Your search for "{{ search }}" found no results.
          </v-alert>
        </v-data-table>
        </v-card>
      </v-flex>
      <v-container @click.stop>
        <div @click.stop class="form">
            <Teams v-if="flag" :pass_data = "pass_data" class="pop"></Teams>
        </div>
      </v-container>
      </v-layout>
    </v-container>
    <!-- <v-container> -->
        <!-- <v-layout @click="closeform">
          <v-flex>
            <v-img
                id = "bg-img"
                class="white--text"
                height="100%"
                src="https://image.shutterstock.com/image-photo/set-badminton-shuttlecock-feather-professional-450w-1030859371.jpg"
              > </v-img>
          </v-flex>
        </v-layout> -->
      <!-- </v-container> -->
    </v-content>
    <v-footer color="deep-purple darken-4" app height="auto">
        <v-spacer></v-spacer>
          <v-btn
            v-for="icon in icons"
            :key="icon"
            class="mx-3"
            dark
            icon
          >
            <v-icon size="24px">{{ icon }}</v-icon>
          </v-btn>
    </v-footer>
    </div>
  </v-app>
</template>

<script>
import axios from 'axios'
import Teams from './Teams'
var moment = require('moment')
  export default {
    components:{Teams},
    data: () => ({
      moment : moment,
      search : '',
      pass_data : '',
      flag : false,
      drawer: null,
      icons: [
        'mdi-facebook',
        'mdi-twitter',
        'mdi-linkedin',
        'mdi-github-circle'
      ],
      top_ranking_headers: [
        {
          text: 'Top Ranking Teams',
          align: 'left',
          sortable: false,
          value: 'name'
        },
      ],
      top_ranks_data: [],
      headers: [
        {
          text: 'League Name',
          align: 'left',
          value: 'name'
        },
        { text: 'Organizer', value: 'organizer'},
        { text: 'Venue', value: 'venue' },
        { text: 'Type', value: 'leagueType' },
        { text: 'City', value: 'city' },
        { text: 'Status', value: 'status' },
        { text: 'Date', value: 'startDate' }
      ],
      leagues_data: []
    }),
    methods: {
      showTeams(a){
        if (event.target.classList.contains('btn__content')) return;
        this.pass_data = a.id
        this.flag = true

      },
      closeform(){
        this.flag = false
      },
      register(){
        alert("coming Soon")
      }
    },
    mounted: function () {
          window.addEventListener("keydown", (e) => {
              if (this.flag && e.keyCode == 27) {
                  this.flag=false;
              }
          });
          axios.get('http://api.playchamps.in/api/leagues')
          .then((res)=>{
            this.leagues_data = res.data
          })
          axios.get('http://localhost:3000/top')
          .then((res)=>{
            this.top_ranks_data = res.data
          })
      },
      props: {
        source: String
      }
  }
</script>
<style>

h3{
  color: #7CFC00;
}
#avatar{
  /* margin-left: 20%; */
}
.pop {
  position: absolute;
  top: 0;
  /* bottom :10%; */
  left: 0%;
  width: 95%;
  margin: 0px auto;
  max-height: 96%;
  padding: 20px 30px;
  padding-right: -50px;
  background-color: #fff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  margin: 5% 5%;

}
#top_ranking tbody td {
    height: 19px;
}
table.v-table tbody td, table.v-table tbody th {
  color: deepskyblue;
  cursor: pointer;
}
table.v-table thead td, table.v-table thead th {
  color: red;
}
#bg-img{
  /* filter: blur(8px); */
  /* -webkit-filter: blur(8px); */
  height: 100%; 
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;
}

.bg-text {
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0, 0.4); /* Black w/opacity/see-through */
  color: white;
  position: relative;
  /* top: 32%; */
  /* left: 50%; */
  /* transform: translate(-50%, -50%); */
  /* z-index: 2; */
  width: 100%;
  text-align: center;
}
.theme--light.v-table {
    background-color: transparent;
}
.theme--light.v-datatable .v-datatable__actions {
  color: #7CFC00;
  background-color: transparent;
}
.theme--light.v-table thead td, .theme--light.v-table thead th{
    color: #7CFC00;
    font: bold;
    font-size: 15px;
}
.v-datatable__actions__select .v-select__selections .v-select__selection--comma {
    font-size: 12px;
    color: #7CFC00 !important;
}
.theme--light.v-datatable thead th.column.sortable.active, .theme--light.v-datatable thead th.column.sortable.active .v-icon, .theme--light.v-datatable thead th.column.sortable:hover {
    color: #7CFC00;
}
#logo{
  margin-top: 9%;
  /* max-height: 50px; */
}
img{
  max-height: 74px;
}
.leagues_table{
  max-width: 50%;
}
#leagues_table{
  background: transparent;
  color: deepskyblue;
}
.theme--light.v-input:not(.v-input--is-disabled) input, .theme--light.v-input:not(.v-input--is-disabled) textarea {
    color: deepskyblue;
}
</style>
