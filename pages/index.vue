<template>
  <v-layout column justify-center align-center>
    <v-flex xs12 sm8 md6>
      <v-container>
        <v-row>
          <v-col cols="12" lg="6">
            <v-menu
              ref="menu1"
              v-model="menu1"
              :close-on-content-click="false"
              transition="scale-transition"
              offset-y
              max-width="290px"
              min-width="290px"
            >
              <template v-slot:activator="{ on }">
                <v-text-field v-model="date" label="Date" persistent-hint v-on="on"></v-text-field>
              </template>
              <v-date-picker v-model="date" no-title @input="menu1 = false"></v-date-picker>
            </v-menu>
          </v-col>

          <v-col cols="12" lg="6">
            <v-menu
              :close-on-content-click="false"
              transition="scale-transition"
              offset-y
              max-width="290px"
              min-width="290px"
            >
              <template v-slot:activator="{ on }">
                <v-text-field
                  label="Date range"
                  readonly
                  v-on="on"
                  v-model="dates"
                ></v-text-field>
              </template>
              <v-date-picker range v-model="dates" no-title></v-date-picker>
            </v-menu>
          </v-col>
        </v-row>
      </v-container>

      <v-row align="center">
        <v-col class="d-flex" cols="12" sm="6">
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
        </v-col>

        <v-col class="d-flex" id="select" cols="12" sm="6">
          <v-select :items="selectlist" v-model="state" filled label="State"></v-select>
        </v-col>
      </v-row>

      <v-card>
        <v-card-title>
          Users
          <v-spacer></v-spacer>
        </v-card-title>

        <v-data-table :headers="headers" :items="items" :search="search" item-key="id"></v-data-table>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
export default {
  data() {
    return {
      menu1:'',
      menu2:'',
      search: "",
      dates: [],
      date: "",
      state: "",
      selectlist: ["completed", "waitinglist", "enrolled"],
      headers: [
        {
          text: "firstname",
          align: "start",
          sortable: false,
          value: "firstname",
          filterable: false
        },
        { text: "LastName", value: "lastname", filterable: false },
        { 
          text: "Email", value: "email", filter: (value,search,item) => {
            // return value != null &&
            // search != null &&
            // typeof value === 'string' &&
            // value.toString().toLocaleUpperCase().indexOf(search) !== -1
            if(!this.search) return true
            else if(this.search){
                return value == this.search
            }
          }
          },
        {
          text: "state",
          value: "state",
          filter: value => {
            if (!this.state) return true;
            return value == this.state;
          }
        },
        { text: "payment", value: "payment", filterable: false },
        {
          text: "created",
          value: "created",
          filter: value => {

            if(this.dates.length==0) return true
            else if(this.dates.length>0){
              return value> this.dates[0] && value<this.dates[1]
            }
            
          }
        },
        {
          text: "birthdate",
          value: "birthdate",
          filter: value => {
         
            if(!this.date) return true
            else if(this.date){
                return value == this.date
            }
           
          }
        }
      ],
      items: []
    };
  },
  created() {
    return fetch("http://localhost:3000/users")
      .then(res => res.json())
      .then(rs => {
        return (this.items = rs);
      });
  }
};
</script>
<style lang="css" scoped>
#select{
  margin-bottom:-20px;
}
</style>