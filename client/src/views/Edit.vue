<template>
  <div id="post">
    <v-app :style="{ background: $vuetify.theme.themes.light.background }">
      <div class="my-5" />
      <v-container>
        <v-row justify="center" align-content="center">
          <v-col>
            <h1 class="blue--text">入力画面</h1>
            <div class="my-5" />
            <v-row justify="center" align-content="center">
              <v-col>
                <div class="white--text text-h4">プロフィール</div>
                <div class="my-3" />
                <v-text-field
                  v-model="userName"
                  background-color="#676767"
                  label="ユーザーネーム"
                  style="width: 400px"
                  placeholder="ユーザーネーム"
                  solo
                  :class="`rounded-xl custom-placeholder-color custom-label-color`"
                >
                </v-text-field>
                <div class="my-1" />
                <v-text-field
                  v-model="gameName"
                  background-color="#676767"
                  label="主にプレイしているゲーム"
                  style="width: 400px"
                  placeholder="主にプレイしているゲーム"
                  solo
                  :class="`rounded-xl custom-placeholder-color custom-label-color`"
                >
                </v-text-field>
                <div class="my-1" />
                <v-text-field
                  v-model="gameName"
                  background-color="#676767"
                  label="プロフィール"
                  style="width: 800px"
                  placeholder="プロフィール"
                  solo
                  :class="`rounded-xl custom-placeholder-color custom-label-color`"
                >
                </v-text-field>
                <div class="my-1" />
              </v-col>
            </v-row>
            <div class="white--text text-h4">一日のスケジュール</div>
            <div class="my-3" />
            <v-row style="width: 880px">
              <v-col>
                <v-text-field
                  v-model="actionContent"
                  background-color="#676767"
                  label="メニュー"
                  style="width: 400px"
                  placeholder="メニュー"
                  solo
                  :class="`rounded-xl custom-placeholder-color custom-label-color`"
                >
                </v-text-field>
                <div v-if="this.actionDataList.length !== 0">
                  <div v-for="(action, key) in actionDataList" :key="key">
                    <div
                      v-if="action.start_time !== '' && action.end_time !== ''"
                      class="white--text text-h5"
                    >
                      ・{{ action.start_time }}～{{ action.end_time }}:{{
                        action.menu
                      }}
                    </div>
                  </div>
                </div>
              </v-col>
              <div class="my-3" />
              <v-col>
                <vue-timepicker
                  format="HH:mm"
                  v-model="startTimeObject"
                  class="timepicker"
                ></vue-timepicker>
              </v-col>
              <v-col>
                <vue-timepicker
                  format="HH:mm"
                  v-model="endTimeObject"
                ></vue-timepicker>
              </v-col>
              <v-col>
                <v-btn fab small v-on:click="addActionData()">
                  <v-icon size="50">mdi-plus-circle</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </v-col>
          <v-col>
            <PieChart :chart-data="chartItems" :options="chartOptions" />
          </v-col>
        </v-row>
      </v-container>
      <div class="my-5" />
      <v-container>
        <v-row justify="center" align-content="center">
          <v-col>
            <div class="white--text text-h4">食事で気を付けること</div>
            <div class="my-3" />
            <v-row style="width: 800px">
              <!-- <v-col>
                <v-card
                  :class="`rounded-xl`"
                  class="mx-auto"
                  color="#3e3e3e"
                  height="50"
                  width="50"
                >
                </v-card>
              </v-col> -->
              <v-col>
                <v-text-field
                  v-model="mealCarefulContent"
                  background-color="#3e3e3e"
                  label="例：朝ごはんはきちんと食べる"
                  style="width: 800px"
                  placeholder="例：朝ごはんはきちんと食べる"
                  solo
                  :class="`rounded-xl custom-placeholder-color custom-label-color`"
                >
                  <template v-slot:append>
                    <v-btn fab small
                      ><v-icon size="50">mdi-plus-circle</v-icon></v-btn
                    >
                  </template>
                </v-text-field>
              </v-col>
            </v-row>
            <div class="my-5" />
            <div class="white--text text-h4">ゲームで気を付けること</div>
            <div class="my-3" />
            <v-row style="width: 800px">
              <!-- <v-col>
                <v-sheet
                  :class="`rounded-xl`"
                  class="mx-auto"
                  color="#3e3e3e"
                  width="50"
                  height="50"
                ></v-sheet>
              </v-col> -->
              <v-col>
                <v-text-field
                  v-model="gameCarefulContent"
                  background-color="#3e3e3e"
                  color="red"
                  label="例：朝ごはんはきちんと食べる"
                  style="width: 800px"
                  placeholder="例：朝ごはんはきちんと食べる"
                  solo
                  :class="`rounded-xl custom-placeholder-color custom-label-color`"
                >
                  <template v-slot:append>
                    <v-btn fab small
                      ><v-icon size="50">mdi-plus-circle</v-icon></v-btn
                    >
                  </template>
                </v-text-field>
              </v-col>
            </v-row>
          </v-col>
        </v-row>
      </v-container>
      <v-container>
        <v-row justify="end">
          <v-btn v-on:click="updateData()" color="blue">編集</v-btn>
        </v-row>
      </v-container>
      <div class="my-5" />
    </v-app>
  </div>
</template>

<script>
import PieChart from "../components/PieChart.vue";
import VueTimePicker from "vue2-timepicker";
import "vue2-timepicker/dist/VueTimepicker.css";
import axios from "axios";
import { BASE_PATH } from "../conts.js";

export default {
  name: "Edit",
  components: {
    PieChart,
    "vue-timepicker": VueTimePicker,
  },
  mounted: async function () {
    const { data } = await this.getData();
    this.setData(data);
    data.schedule.forEach((s) => {
      this.convertScheduleToChartItems(s);
    });
    this.fillData(this.chartItems.datasets[0]["data"]);
  },
  data() {
    return {
      isChartExist: false,
      userName: null,
      gameName: null,
      profile: null,
      mealCarefulContent: null,
      gameCarefulContent: null,
      actionContent: null,
      actionDataList: [],
      latestId: 0,
      beforeTime: 25,
      chartItems: {
        datasets: [
          {
            label: "一日の生活",
            backgroundColor: "#847636",
            data: [1440],
          },
        ],
      },
      startTimeObject: {
        HH: "00",
        mm: "00",
      },
      endTimeObject: {
        HH: "00",
        mm: "00",
      },
      chartOptions: {
        //responsive: true,
        maintainAspectRatio: false,
      },
    };
  },
  methods: {
    addActionData() {
      this.actionDataList.push({
        start_time: `${this.startTimeObject.HH}`,
        end_time: `${this.endTimeObject.HH}`,
        menu: this.actionContent,
      });

      let list = this.actionDataList;
      let addTime =
        (parseInt(list[list.length - 1].end_time) -
          parseInt(list[list.length - 1].start_time)) *
        60;
      let maxTime =
        this.chartItems.datasets[0]["data"][
          this.chartItems.datasets[0]["data"].length - 1
        ];

      /*
      this.chartItems.datasets[0]["data"].unshift(addTime);
      this.chartItems.datasets[0]["data"][
        this.chartItems.datasets[0]["data"].length - 1
      ] = maxTime - addTime;
      */
      if (this.chartItems.datasets[0]["data"].length != 1) {
        this.chartItems.datasets[0]["data"][
          this.chartItems.datasets[0]["data"].length - 1
        ] = addTime;
        this.chartItems.datasets[0]["data"].push(maxTime - addTime);
      } else {
        this.chartItems.datasets[0]["data"].unshift(addTime);
        this.chartItems.datasets[0]["data"][
          this.chartItems.datasets[0]["data"].length - 1
        ] = maxTime - addTime;
      }
      this.fillData(this.chartItems.datasets[0]["data"]);

      this.actionContent = "";
    },
    fillData(props = "") {
      if (!props) {
        this.chartItems = {
          datasets: [
            {
              label: "一日の生活",
              backgroundColor: "#847636",
              data: [1440],
            },
          ],
        };
      } else {
        this.chartItems = {
          datasets: [
            {
              label: "一日の生活",
              backgroundColor: "#847636",
              data: props,
            },
          ],
        };
      }
    },
    async getData() {
      try {
        const res = await axios.get(
          `${BASE_PATH}api/articles/${this.$route.params.id}`
        );
        return res.data;
      } catch (err) {
        console.log(err);
      }
    },
    setData(data) {
      this.userName = data.userName;
      this.gameName = data.gameName;
      this.mealCarefulContent = data.meal_description;
      this.gameCarefulContent = data.notice;
      // this.actionDataList = data.schedule;
      this.profile = data.profile;
    },
    convertScheduleToChartItems(data) {
      this.actionDataList.push({
        start_time: `${data.start_time}`,
        end_time: `${data.end_time}`,
        menu: data.menu,
      });

      let list = this.actionDataList;
      let addTime =
        (parseInt(list[list.length - 1].end_time) -
          parseInt(list[list.length - 1].start_time)) *
        60;
      let maxTime =
        this.chartItems.datasets[0]["data"][
          this.chartItems.datasets[0]["data"].length - 1
        ];

      this.chartItems.datasets[0]["data"].unshift(addTime);
      this.chartItems.datasets[0]["data"][
        this.chartItems.datasets[0]["data"].length - 1
      ] = maxTime - addTime;
    },
    async updateData() {
      try {
        const config = {
          headers: {
            "Content-Type": "application/json",
          },
        };

        const body = {
          userName: this.userName,
          gameName: this.gameName,
          profile: this.profile,
          schedule: this.actionDataList,
          meal_description: this.mealCarefulContent,
          notice: this.gameCarefulContent,
        };

        const { data } = await axios.put(
          `https://3d.intern.jigd.info/api/articles/${this.$route.params.id}`,
          body,
          config
        );

        if (data.success) {
          // re-route "/"
          this.$router.push("/");
        } else {
          alert(data.msg);
        }
      } catch (err) {
        console.error(err);
      }
    },
  },
};
</script>

<style>
/*
.custom-placeholder-color input::placeholder {
  color: white !important;
  opacity: 1;
}
*/

.custom-placeholder-color input {
  color: white !important;
  opacity: 1;
}
.custom-label-color .v-label {
  color: white !important;
  opacity: 1;
}
.custom-label-color .v-model {
  color: white !important;
  opacity: 1;
}
.timepicker {
  color: white !important;
  opacity: 1;
}
</style>