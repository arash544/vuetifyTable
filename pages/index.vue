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
              v-model="menu2"
              :close-on-content-click="false"
              transition="scale-transition"
              offset-y
              max-width="290px"
              min-width="290px"
            >
              <template v-slot:activator="{ on }">
                <v-text-field
                  v-model="dates"
                  label="Date range"
                  persistent-hint
                  readonly
                  range
                  v-on="on"
                ></v-text-field>
              </template>
              <v-date-picker v-model="dates" no-title @input="menu2 = false"></v-date-picker>
            </v-menu>
          </v-col>
        </v-row>
      </v-container>

      <v-row align="center">
        <!-- <v-col class="d-flex" cols="12" sm="3">
        <v-date-picker v-model="date"></v-date-picker>
      </v-col>
      <v-col class="d-flex" cols="12" sm="3">
        <v-date-picker v-model="dates" range></v-date-picker>
        </v-col>-->

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
          <v-select :items="selectlist" v-model="state" filled label="Filled style"></v-select>
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
      search: "",
      dates: ["2019-09-10", "2021-09-10"],
      date: "",
      state: "",
      selectlist: ["","completed", "waitinglist", "enrolled"],
      headers: [
        {
          text: "firstname",
          align: "start",
          sortable: false,
          value: "firstname",
          filterable: false
        },
        { text: "LastName", value: "lastname", filterable: false },
        { text: "Email", value: "email", filterable: true },
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
            if (!this.date || this.dates.lnegth == 0) return true;
            else if (this.date) {
              return value > this.date;
            } else if (this.dates) {
              return value > this.dates[0] && value < this.dates[1];
            }

            //console.log(this.dates)
          }
        }
      ],
      items: []
    };
  },
  created() {
    return fetch("http://localhost:3000/users")
    // return fetch("db.json")
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