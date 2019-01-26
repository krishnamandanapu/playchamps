<template>
  <v-app id="inspire">
    <div @click="closeform">
    <v-toolbar color="deep-purple darken-4" dark fixed app>
      <v-toolbar-title>Play Champs</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon>
      <v-icon>fa fa-search</v-icon>
      </v-btn>
      <v-btn color="deep-purple darken-4" dark>
        Login
      </v-btn>
    </v-toolbar>
    <!-- <v-content> -->
        <v-layout >
          <v-flex>
            <v-card>
              <!-- <v-img
                id = "im"
                class="white--text"
                height="700px"
                src="https://cdn.vuetifyjs.com/images/cards/docks.jpg"
              > -->
              <v-container id = "div1">
                <h2>Manage Badminton League/Tournaments</h2>
                <pre>     </pre>
                <p>
                  Register for free and try our features to schedule, manage and monitor badminton leagues in few easy steps online, PlayChamps is built for computer, Tsblets and mobiles.
                </p>
              </v-container>
              <v-container id="div2">
                <h2>Top Ranking Teams</h2>
                <v-data-table
                  hide-actions
                  hide-headers
                  :headers="headers"
                  :items="desserts"
                >
                  <template slot="items" slot-scope="props">
                    <td>{{props.item.rank}}</td>
                    <td>{{ props.item.name }}</td>
                  </template>
                </v-data-table>
              </v-container>
              <v-container id="div3">
                <h2>Top Ranking Players</h2>
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
              </v-container>
              <v-container id="div4" @click.stop>
                <v-data-table
                  :headers="headers"
                  :items="data"
                  class="elevation-1"
                >
                  <template slot="items" slot-scope="props">
                    <tr @click="showTeams(props.item)">
                    <td class="text-xs-left">{{ props.item.name }}</td>
                    <td class="text-xs-left">{{ props.item.organizer }}</td>
                    <td class="text-xs-left">{{ props.item.venue }}</td>
                    <td class="text-xs-left">{{ props.item.type }}</td>
                    <td class="text-xs-left">{{ props.item.city }}</td>
                    <td class="text-xs-left">{{ props.item.status }}</td>
                    <td class="text-xs-left">{{ props.item.date }}</td>
                    </tr>
                  </template>
                </v-data-table>
              </v-container>
              <v-container @click.stop>
                <div @click.stop class="form">
                    <Teams v-if="flag" class="pop"></Teams>
                </div>
              </v-container>
              <!-- </v-img> -->
            </v-card>
          </v-flex>
        </v-layout>
    <!-- </v-content> -->
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
  export default {
    components:{Teams},
    data: () => ({
      flag : false,
      drawer: null,
      icons: [
        'mdi-facebook',
        'mdi-twitter',
        'mdi-google-plus',
        'mdi-linkedin',
        'mdi-instagram'
      ],
      headers: [
        {
          text: 'Top Ranking Teams',
          align: 'left',
          sortable: false,
          value: 'name'
        },
      ],
      desserts: [
        {
          name: 'Frozen Yogurt',
          rank : 1
        },
        {
          name: 'Ice cream sandwich',
          rank : 2
        },
        {
          name: 'Eclair',
          rank : 3
        },
      ],
      headers: [
        {
          text: 'League Name',
          align: 'left',
          sortable: false,
          value: 'name'
        },
        { text: 'Organizer', value: 'organizer',sortable:false},
        { text: 'Venue', value: 'venue',sortable:false },
        { text: 'Type', value: 'type',sortable:false },
        { text: 'City', value: 'city',sortable:false },
        { text: 'Status', value: 'status',sortable:false },
        { text: 'Date', value: 'date',sortable:false }
      ],
      data: []
    }),
    methods: {
      showTeams(a){
        // if (event.target.classList.contains('btn__content')) return;
        // alert('Alert! \n' + a.name);
        this.flag = true

      },
      closeform(){
        this.flag = false
      }
    },
    mounted: function () {
          window.addEventListener("keydown", (e) => {
              if (this.flag && e.keyCode == 27) {
                  this.flag=false;
              }
          });
          axios.get('http://localhost:3000/leagues')
          .then((res)=>{
            this.data = res.data
          })
      },
      props: {
        source: String
      }
  }
</script>
<style>
#img{
  opacity: .4;
}
#div1{
  margin-left: -0px;
  max-width: 35%;

}
#div2{
  margin-left: 35%;
  max-width: 20%;
  margin-top: -12%;
}
#div3{
  margin-left: 40%;
  margin-top: -15%;
}
#avatar{
  margin-left: 25%;
}
#div4{
  margin-top: 5%;
  max-width: 80%;
  margin-left: 0%;
}
.pop {
  position: absolute;
  top: 0;
  /* bottom :10%; */
  left: 0%;
  width: 70%;
  margin: 0px auto;
  max-height: 50%;
  padding: 20px 30px;
  padding-right: -50px;
  background-color: #fff;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  margin: 12% 5%;

}
.form{
    /* height:40px; */
}

</style>
