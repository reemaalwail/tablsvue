<template>
  <v-container>
    <div class="border-black">
      <!-- todo list advince -->
      <v-row>
        <v-col lg="6" sm="4" cols="12">
          <div class="btn btn-total">
            <span>total Subscribers: {{ desserts.length }}</span>
          </div>
        </v-col>
      </v-row>
      <v-row :justify="justify">
        <v-col lg="6" sm="8" cols="12">
          <v-text-field
            v-model="Search"
            label="What are you search on?"
            solo
          ></v-text-field>
        </v-col>
        <v-col
          lg="4"
          sm="4"
          cols="12"
          @click="(showPopup = true), (display = !false)"
          ><!-- add new user -->
          <div class="btn btn-AddNew">
            <i class="fas fa-plus"></i><span> new Subscribers</span>
          </div>
        </v-col>
        <!-- START CARD ADD user  -->
        <transition name="slide">
          <div class="menuUserAdd-overlay" v-if="showPopup">
            <v-container fluid>
              <v-row :jusstify="justify">
                <v-col cols="12" lg="12">
                  <p>new detail</p>
                </v-col>
                <v-col cols="12" lg="12">
                  <v-text-field
                    label="Full Name"
                    solo
                    v-model="editedItem.name"
                  ></v-text-field>
                </v-col>
                <v-col lg="8">
                  <v-menu
                    ref="menu"
                    v-model="menu"
                    :close-on-content-click="false"
                    :return-value.sync="dates"
                    transition="scale-transition"
                    offset-y
                    min-width="290px"
                  >
                    <template v-slot:activator="{ on }">
                      <v-combobox
                        solo
                        v-model="editedItem.dates"
                        label=" data starting "
                        readonly
                        v-on="on"
                      ></v-combobox>
                    </template>
                    <v-date-picker
                      v-model="editedItem.dates"
                      no-title
                      scrollable
                    >
                      <v-spacer></v-spacer>
                      <v-btn text color="primary" @click="menu = false"
                        >Cancel</v-btn
                      >
                      <v-btn
                        text
                        color="primary"
                        @click="$refs.menu.save(dates)"
                        >OK</v-btn
                      >
                    </v-date-picker>
                  </v-menu>
                </v-col>

                <v-col cols="4" lg="4">
                  <v-text-field
                    label="Calories"
                    solo
                    v-model="editedItem.calories"
                  ></v-text-field>
                </v-col>
                <v-col cols="4" lg="4">
                  <v-text-field
                    label="Fat"
                    solo
                    v-model="editedItem.fat"
                  ></v-text-field>
                </v-col>
                <v-col cols="4" lg="4">
                  <v-text-field
                    label="Iron"
                    solo
                    v-model="editedItem.iron"
                  ></v-text-field>
                </v-col>
                <v-col cols="4" lg="4">
                  <v-text-field
                    label="Current Weight"
                    solo
                    v-model="editedItem.CurrentWeight"
                  ></v-text-field>
                </v-col>

                <v-col lg="4">
                  <div
                    class="btn btn-save pointer bold-weight capitalize"
                    :class="{ none: display }"
                    @click="save((showPopup = false))"
                  >
                    save
                  </div>
                  <div
                    class="btn btn-Add pointer bold-weight capitalize"
                    @click="Add((showPopup = false))"
                  >
                    Add
                  </div>
                </v-col>
                <v-col lg="4">
                  <div
                    class="btn btn-cancel pointer bold-weight capitalize"
                    @click="close"
                  >
                    cancle
                  </div>
                </v-col>
              </v-row>
            </v-container>
          </div>
        </transition>
        <!-- END CARD ADD user  -->
      </v-row>
      <v-data-table
        :headers="headers"
        :items="resultSearch"
        class="elevation-1"
        :single-select="singleSelect"
        v-model="selected"
        show-select
      >
        <template v-slot:item.CurrentWeight="{ item }">
          <v-chip
            :color="getColor(item.CurrentWeight)"
            :text-color="Color(item.CurrentWeight)"
          >
            {{ item.CurrentWeight }}
          </v-chip>
        </template>
        <template v-slot:item.action="{ item }" class="display">
          <div class="Card-Action" :class="{ showAction: item.showAction }">
            <ul>
              <li v-on:click="RemoveRow(item)">
                <!-- remove item -->
                <div class="delet--item">
                  <svg
                    id="i-trash"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 32 32"
                    width="18"
                    height="18"
                    fill="none"
                    stroke="currentcolor"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                  >
                    <path
                      d="M28 6 L6 6 8 30 24 30 26 6 4 6 M16 12 L16 24 M21 12 L20 24 M11 12 L12 24 M12 6 L13 2 19 2 20 6"
                    />
                  </svg>
                </div>
              </li>
              <li @click="editItem(item), (display = false)">
                <!-- edit item -->
                <div class="edit--item">
                  <svg
                    id="i-edit"
                    xmlns="http://www.w3.org/2000/svg"
                    viewBox="0 0 32 32"
                    width="18"
                    height="18"
                    fill="none"
                    stroke="currentcolor"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                  >
                    <path
                      d="M30 7 L25 2 5 22 3 29 10 27 Z M21 6 L26 11 Z M5 22 L10 27 Z"
                    />
                  </svg>
                </div>
              </li>
            </ul>
          </div>
          <div
            class="btn btn-action"
            v-on:click="item.showAction = !item.showAction"
          >
            <v-icon>{{ item.action }}</v-icon>
          </div>
        </template>
      </v-data-table>
    </div>
  </v-container>
</template>
<script>
export default {
  data() {
    return {
      dates: ["2018-09-15", "2018-09-20"],
      menu: false,
      display: true,
      showPopup: false,
      Search: null,
      sortBy: "name",
      orderBy: "name",
      editedIndex: -1,
      editedItem: {
        /** edititem **/
        name: "",
        calories: 0,
        fat: 0,
        carbs: 0,
        protein: 0,
        action: "fas fa-ellipsis-h",
        showAction: false,
      },
      NewName: "",
      NewCalories: "",
      singleSelect: false,
      selected: [],
      justify: "center", //flex
      items: ["Calories", "weight"],
      headers: [
        {
          text: "Name(SUBSCRIBERS)",
          sortable: false,
          value: "name",
        },
        //header table
        { text: "Starting Date", value: "dates" },
        { text: "Calories", value: "calories" },
        { text: "Fat (g)", value: "fat" },
        { text: "Iron (%)", value: "iron" },
        { text: "Current weight (kg)", value: "CurrentWeight" },
        { text: "Action", value: "action" },
      ],
      desserts: [
        //data table
        {
          id: 1,
          name: "Delcine Rozsa",
          dates: "2019/12/12",
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
          iron: "1%",
          CurrentWeight: 100 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 2,
          name: "Elinor Groome",
          dates: "2020/01/01",
          calories: 237,
          fat: 9.0,
          carbs: 37,
          protein: 4.3,
          iron: "1%",
          CurrentWeight: 95 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 3,
          name: "Paton Pauncefort",
          dates: "2019/12/03",
          calories: 262,
          fat: 16.0,
          carbs: 23,
          protein: 6.0,
          iron: "7%",
          CurrentWeight: 76 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 4,
          name: "Ulysses Rhyme",
          dates: "2019/12/04",
          calories: 305,
          fat: 3.7,
          carbs: 67,
          protein: 4.3,
          iron: "8%",
          CurrentWeight: 66 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 5,
          name: "Jordana Nunson",
          dates: "2019/11/11",
          calories: 356,
          fat: 16.0,
          carbs: 49,
          protein: 3.9,
          iron: "16%",
          CurrentWeight: 83 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 6,
          name: "Doy Aldridge",
          dates: "2019/04/04",
          calories: 375,
          fat: 0.0,
          carbs: 94,
          protein: 0.0,
          iron: "0%",
          CurrentWeight: 102 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 7,
          name: "Paulette Dyble",
          dates: "2020/12/05",
          calories: 392,
          fat: 0.2,
          carbs: 98,
          protein: 0,
          iron: "2%",
          CurrentWeight: 210 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 8,
          name: "Paulie Dursley",
          dates: "2020/02/02",
          calories: 408,
          fat: 3.2,
          carbs: 87,
          protein: 6.5,
          iron: "45%",
          CurrentWeight: 55 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 9,
          name: "Claus Enevold",
          dates: "2020/01/03",
          calories: 452,
          fat: 25.0,
          carbs: 51,
          protein: 4.9,
          iron: "22%",
          CurrentWeight: 40 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 10,
          name: "Leela Brooke",
          dates: "2020/04/04",
          calories: 518,
          fat: 26.0,
          carbs: 65,
          protein: 7,
          iron: "6%",
          CurrentWeight: 98 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },

        {
          id: 11,
          name: "Elinor Groome",
          dates: "2020/03/05",
          calories: 452,
          fat: 25.0,
          carbs: 51,
          protein: 5.9,
          iron: "22%",
          CurrentWeight: 89 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 12,
          name: "Doy Aldridge",
          dates: "2020/02/01",
          calories: 518,
          fat: 26.0,
          carbs: 65,
          protein: 7,
          iron: "6%",
          CurrentWeight: 55 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
        {
          id: 13,
          name: "Ulysses Rhyme",
          dates: "2020/01/05",
          calories: 518,
          fat: 26.0,
          carbs: 65,
          protein: 7,
          iron: "6%",
          CurrentWeight: 40 + "kg",
          action: "fas fa-ellipsis-h",
          showAction: false,
        },
      ],
    };
  },
  watch: {
    menu(val) {
      val && setTimeout(() => (this.$refs.picker.activePicker = "YEAR"));
    },
  },
  methods: {
    savedData(date) {
      this.$refs.menu.savedData(date);
    },
    // btn bgcolor style in function
    getColor(CurrentWeight) {
      if (CurrentWeight == 40 + "kg") return "#e7e5f1ab";
      else if (CurrentWeight == 95 + "kg") return "rgba(244,241,228, 0.70)";
      else if (CurrentWeight == 55 + "kg") return "rgba(225,233,250, 0.70)";
      else if (CurrentWeight == 100 + "kg") return "rgb(247,237,228, 0.70)";
      else return "rgba(245,232,236,0.70)";
    },
    //color btn style in function
    Color(CurrentWeight) {
      if (CurrentWeight == 40 + "kg") return "#756dcc";
      else if (CurrentWeight == 95 + "kg") return "rgb(193,175,113)";
      else if (CurrentWeight == 55 + "kg") return "rgb(88,115,177)";
      else if (CurrentWeight == 100 + "kg") return "rgb(233,179,118)";
      else return "rgb(218,131,156)";
    },
    // function delet Row list
    RemoveRow: function(item) {
      this.desserts.splice(this.desserts.indexOf(item), 1);
    },
    // edited row  and add new Row
    editItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.showPopup = true;
    },
    Add() {
      if (
        this.editedItem.name == " " ||
        this.editedItem.dates == " " ||
        this.editedItem.calories == " " ||
        this.editedItem.fat == " " ||
        this.editedItem.iron == " " ||
        this.editedItem.CurrentWeight == " "
      ) {
        return;
      }
      this.desserts.push({
        name: this.editedItem.name,
        dates: this.editedItem.dates,
        fat: this.editedItem.fat,
        iron: this.editedItem.iron + "٪",
        CurrentWeight: this.editedItem.CurrentWeight + "kg",
        calories: this.editedItem.calories,
        action: "fas fa-ellipsis-h",
        showAction: false,
      });
      this.editedItem.name = " ";
      this.editedItem.fat = " ";
      this.editedItem.iron = " ";
      this.editedItem.CurrentWeight = " ";
      this.editedItem.calories = " ";
    },
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.desserts[this.editedIndex], this.editedItem);
      } else {
        this.desserts.push(this.editedItem);
      }
      this.close();
    },
    close() {
      this.showPopup = false;
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      }, 300);
    },
  },

  //search filter
  computed: {
    resultSearch() {
      if (this.Search) {
        return this.desserts.filter((item) => {
          return this.Search.toLowerCase()
            .split(" ")
            .every((v) => item.name.toLowerCase().includes(v));
        });
      } else {
        return this.desserts;
      }
    },
  },
};
</script>
