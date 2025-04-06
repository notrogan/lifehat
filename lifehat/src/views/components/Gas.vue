<template>
  <div class="wrap">
    <div class="main">
      <div class="outer">
        <div class="inner">
          <div id="value">
            {{ gas }}
            <div id="unit">
              PPM
            </div>
          </div>
        </div>
      </div>

      <svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="90vw" height="90vw">
        <defs>
          <linearGradient id="GradientColor" gradientTransform="rotate(15)">
              <stop offset="25%" stop-color="#fa0f56" />
              <stop offset="85%" stop-color="#ffe401" />
              <stop offset="100%" stop-color="#80dd00" />
          </linearGradient>
        </defs>
        <circle cx="50vw" cy="37.5vw" r="32.5vw" stroke-linecap="round" />
      </svg>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import { DataService } from "@/services/DataService";
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar } from '@ionic/vue';

const dataService = DataService.getInstance();

function stall(func: () => void, ms: number | undefined) {
  return new Promise(() => {
      setTimeout(func, ms)
  })
}


let gas = ref(0);
let gasProgress = ref(0);

onMounted(() => {
  setInterval(getGas, 1000);
})

async function getGas() : Promise<Record<string, any>[]> {
  const data = await dataService.getData("METRIC");


  gas.value = Math.round(data[0].ppm);
  gasProgress.value = Math.max(200, Math.min(730 - Math.round(data[0].ppm * .2), 735));

  return data;
}

</script>

<style scoped>

.wrap {
  transform: translateX(7vw);
  height: 50vh;
  display: flex;
  justify-content: center;
}

.main {
  margin-top: 2vh;
  width: 75vw;
  height: 75vw;
}

.outer {
  transform: translateX(17vw) translateY(-10vh) scale(.5);
  padding: 5vw;
  width: inherit;
  height: inherit;
}

.inner {
  width: 55vw;
  height: 55vw;
  display: flex;
  border-radius: 50%;
  align-items: center;
  justify-content: center;
}

#value {
  margin-top: 2.5rem;
  margin-left: 2.75rem;
  color: #ffffff;
  font-size: 3rem;
  text-align: center;
  font-family: "rivian-bold";
}

#unit {
  color: #ffffff95;
  font-size: 1.75rem;
  text-align: center;
  font-family: "rivian";
}

circle {
  transform: translateX(5vw) translateY(14.5vh) scale(.4);
  fill: none;
  stroke-width: 10vw;
  stroke-opacity: .85;
  stroke-dasharray: 765;
  stroke: url(#GradientColor);
  transition: ease-in-out 1s;
  stroke-dashoffset: v-bind(gasProgress);
}

svg {
  margin-top: 2vh;
  transform: rotate(149.5deg) translateX(-43.25vw) translateY(38.75vh);
}

@font-face {
  font-family: 'rivian';
  src: url('../../font/Kraftig.otf');
  font-weight: normal;
  font-style: normal;
}

@font-face {
  font-family: 'rivian-bold';
  src: url('../../font/Halbfett.otf');
  font-weight: normal;
  font-style: normal;
}

</style>
