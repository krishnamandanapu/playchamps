<template>
    <v-app id="inspire" v-if="show">
      <h2>WSC Advanced Players League</h2>
 <v-data-table :headers="mainHeaders"
               :items="mainItems"
               item-key="position"
               hide-actions
               expand
               class="elevation-1"
               >
  <template slot="items" slot-scope="props">
    <tr @click="props.expanded = !props.expanded">
      <td class="text-xs-left">{{ props.item.position }}</td>
      <td class="text-xs-left">{{ props.item.team }}</td>
      <td class="text-xs-left">{{ props.item.matches_played }}</td>
      <td class="text-xs-left">{{ props.item.nps }}</td>
      <td class="text-xs-left">{{ props.item.win_rate }}</td>
      <td class="text-xs-left">{{ props.item.score }}</td>
    </tr>
  </template>
  <template slot="expand" slot-scope="props">
    <v-card class="elevation-10">
      <v-card-text>
        <v-data-table :headers="subHeaders"
                      :items="subItems"
                      item-key="color"
                      hide-actions
                      class="elevation-10">
          <template slot="items" slot-scope="props">
            <td class="text-xs-left">{{ props.item.result }}</td>
            <td class="text-xs-left">{{ props.item.opponent_team }}</td>
            <td class="text-xs-left">{{ props.item.match_details }}</td>
            <td class="text-xs-left">{{ props.item.completed_on }}</td>
            <td class="text-xs-left">{{ props.item.match_score }}</td>
          </template>
        </v-data-table>

      </v-card-text>
    </v-card>
  </template>
 </v-data-table>  </v-app>
</template>

<script>
import axios from 'axios'
export default {
    data () {
    return {
      show : true,
     mainHeaders: [
        { text: 'Position', value: 'position', sortable : false },
        { text: 'Team', value: 'team', sortable:false },
        { text: 'Matches Played', value: 'matches_played', sortable:false },
        { text: 'NPS', value: 'nps', sortable:false },
        { text: 'Win%', value: 'win_rate', sortable:false },
        { text: 'Score%', value: 'score', sortable:false }
      ],
      subHeaders: [
        { text: 'Result', value: 'result', sortable : false},
        { text: 'Opponent Team', value: 'opponent_team', sortable : false },
        { text: 'Match Details', value: 'match_details', sortable : false },
        { text: 'Completed On', value: 'completed_on', sortable : false },
        { text: 'Match Score', value: 'match_score', sortable : false },
      ],
      mainItems: [],
      subItems: []
    }
  },
  methods:{
        closeform(){
                this.show=false
        }
  },
  mounted: function (){
    axios.get('http://localhost:3000/teams')
    .then((res)=>{
     this.mainItems= res.data
    })
     
    axios.get('http://localhost:3000/team')
    .then((res)=>{
      this.subItems = res.data
    })
  }
}
</script>

<style>

</style>
