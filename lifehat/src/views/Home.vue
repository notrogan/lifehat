<template>
  <ion-page>
    <ion-content :fullscreen="true">
      <div class="wrapper">
        <div class="toolbar">Dashboard</div>
        <div style="height: 9.8vh;"></div>
        <div class="card">
          <div class="header">
            AIR QUALITY
          </div>
          <div class="status">
            {{ status }}
          </div>
          <div class="date">
            {{ time }}
          </div>
          <div class="forecast">
            FORECAST
          </div>
          <div class="average">Daily Average</div>
          <div class="days">
            <IonGrid>
              <IonRow>
                <IonCol>
                  <div class="day">MON</div>
                  <div class="dot"></div>
                </IonCol>
                <IonCol>
                  <div class="day">TUE</div>
                  <div class="dot2"></div>
                </IonCol>
                <IonCol>
                  <div class="day">WED</div>
                  <div class="dot3"></div>
                </IonCol>
              </IonRow>
            </IonGrid>
          </div>
          <Gas></Gas>
        </div>
        <div style="height: 2.25vh;"></div>
        <div class="card">
          <div class="header">
            DUST QUANTITY
          </div>
          <div class="status">
            {{ dustStatus }}
          </div>
          <div class="date">
            {{ time }}
          </div>
          <div class="forecast">
            FORECAST
          </div>
          <div class="average">Daily Average</div>
          <div class="days">
            <IonGrid>
              <IonRow>
                <IonCol>
                  <div class="day">MON</div>
                  <div class="dot"></div>
                </IonCol>
                <IonCol>
                  <div class="day">TUE</div>
                  <div class="dot2"></div>
                </IonCol>
                <IonCol>
                  <div class="day">WED</div>
                  <div class="dot3"></div>
                </IonCol>
              </IonRow>
            </IonGrid>
          </div>
          <Dust></Dust>
        </div>
        <!-- <div style="height: 2.25vh;"></div>
        <div class="card">
          <div class="header">
            UV INDEX
          </div>
          <div class="status">
            {{ dustStatus }}
          </div>
          <div class="date">
            {{ time }}
          </div>

          <Index></Index>
        </div> -->
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { IonContent, IonPage, IonGrid, IonRow, IonCol } from '@ionic/vue';
import Dust from './components/Dust.vue';
import Index from './components/Index.vue';
import Gas from './components/Gas.vue';

import { DataService } from "@/services/DataService";

const dataService = DataService.getInstance();

let time = "";
let status = ref("");
let dustStatus = ref("");

let gasDotFirst = ref("#80dd00");
let gasDotSecond = ref("#80dd00");
let gasDotThird = ref("#80dd00");

let gasList: any[] = [];
let gasAverage = ref(0);

const date = new Date()

time = `${date.toLocaleString('en-US', { month: 'short' })} ${date.toLocaleString('en-US', { day: '2-digit' })} ${date.getHours()}:00, local time`;

onMounted(() => {
  setInterval(getStatus, 1000);
  setInterval(getForecast, 1000);
})

async function getStatus() : Promise<Record<string, any>[]> {
  const data = await dataService.getData("METRIC");

  if (data[0].ppm < 450) {
    status.value = "Good";
  }
  else if (data[0].ppm < 1200) {
    status.value = "Moderate";
  }
  else if (data[0].ppm < 2400) {
    status.value = "Poor";
  }
  else {
    status.value = "Terrible"
  }

  if (data[0].dust < 65) {
    dustStatus.value = "Good";
  }
  else if (data[0].dust < 100) {
    dustStatus.value = "Moderate";
  }
  else if (data[0].dust < 200) {
    dustStatus.value = "Poor";
  }
  else {
    dustStatus.value = "Terrible"
  }

  return data;
}

async function getForecast() : Promise<Record<string, any>[]> {
  const data = await dataService.getData("METRIC");

  gasList.push(data[0].ppm);
  
  if (gasList.length > 100) {
    gasList = [];
  }

  var average = 0;
  for (var i=0; i < gasList.length; i++) {
    average += gasList[i];
  }
  gasAverage.value = average / gasList.length;
  console.log(gasAverage.value);

  if (gasAverage.value < 4500) {
    gasDotFirst.value = "#80dd00";
    gasDotSecond.value = "#80dd00";
    gasDotThird.value = "#80dd00";
  }
  else if (gasAverage.value < 12000) {
    gasDotFirst.value = "#ffe401"; 
    gasDotSecond.value = "#80dd00";
    gasDotThird.value = "#80dd00";
  }
  else if (gasAverage.value < 24000) {
    gasDotFirst.value = "#fe8000";
    gasDotSecond.value = "#ffe401";
    gasDotThird.value = "#80dd00";
  }
  else {
    gasDotFirst.value = "#fe0c57";  
    gasDotSecond.value = "#fe8000";
    gasDotThird.value = "#ffe401";
  }

  return data;
}


</script>

<style scoped>

.days {
  text-align: center;
  position: absolute;
  width: 34%;
  margin-left: 13.4rem;
  margin-top: 8.4rem;
}

.dot {
  border-radius: 50%;
  background-color: v-bind(gasDotFirst);
  width: 2vw;
  height: 2vw;
  display: block;
  margin-top: .75rem;
  margin-left: auto;
  margin-right: auto;
}

.dot2 {
  border-radius: 50%;
  background-color: v-bind(gasDotSecond);
  width: 2vw;
  height: 2vw;
  display: block;
  margin-top: .75rem;
  margin-left: auto;
  margin-right: auto;
}

.dot3 {
  border-radius: 50%;
  background-color: v-bind(gasDotThird);
  width: 2vw;
  height: 2vw;
  display: block;
  margin-top: .75rem;
  margin-left: auto;
  margin-right: auto;
}

.day {
  color: #7791b4;
  font-size: .75rem;
  font-family: 'rivian';
}

.forecast {
  font-size: .85rem;
  letter-spacing: .2rem;
  font-family: 'rivian-bold';

  position: absolute;
  margin-top: 9rem;
  color: #c5d1e3;
  margin-left: 1.25rem;
}

.average {
  font-size: .75rem;
  font-family: 'rivian';

  position: absolute;
  margin-top: 10.55rem;
  color: #7791b4;
  margin-left: 1.25rem;
}

.toolbar {
  position: absolute;
  height: 2.75vh;
  width: 100%;
  text-align: center;
  margin-top: 5.5vh;
  font-size: 1.1rem;
  font-family: 'rivian';
}

.wrapper {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background-color: #001034;
}

.card {
  height: 23.25vh;
  width: 90vw;
  margin-left: 5vw;
  border-radius: 1rem;
  background-color: #01246a;
}

.header {
  font-size: .85rem;
  letter-spacing: .2rem;
  font-family: 'rivian-bold';

  position: absolute;
  margin-top: 2rem;
  color: #c5d1e3;
  margin-left: 1.25rem;
}

.status {
  font-size: 1.75rem;
  font-family: 'rivian';

  position: absolute;
  margin-top: 3.25rem;
  color: #ffffff;
  margin-left: 1.25rem;
}

.date {
  font-size: .75rem;
  font-family: 'rivian';

  position: absolute;
  margin-top: 5.85rem;
  color: #7791b4;
  margin-left: 1.25rem;
}


@font-face {
  font-family: 'rivian';
  src: url('../font/Kraftig.otf');
  font-weight: normal;
  font-style: normal;
}

@font-face {
  font-family: 'rivian-bold';
  src: url('../font/Halbfett.otf');
  font-weight: normal;
  font-style: normal;
}


</style>
