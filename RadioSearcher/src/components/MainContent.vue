<template>
  <MainHeader :fetchData="fetchData" :country="country_search" />
  <InfoPage :current_selected="current_playing"/>
    <ion-content id="main-content">
        <ion-grid :fixed="true">
          <img @click="openModal"  class="menu-icon" src='../../public/menu.png'>
          <div v-show="showPause"  class="play-menu">
            <ion-range @ion-change="manageVol" class="volume-range" aria-label="Volume" :pin="true" :pin-formatter="pinFormatter" ></ion-range>
            <img @click="pauseAudio()" src='../../public/pause.png'>
            <img class="muteTag" @click="muteAudio()" src="../../public/infoImg/mute.png" />
            <p>Playing: <span style="font-weight: bold;font-size:80%">{{current_playing.station_name}}</span></p>
            <img class="radio-img" src="../../public/radioIcon.png" alt="">
          </div>
   
            <ion-row class="row"> 
              <ion-col style="border-right:none;" class="middle-radio" size="12"><ion-header class="heading-tool">
              
                <ion-toolbar >
                  <ion-title >
                    Available radios
                    
                  </ion-title>
                  <p style="font-size: 70%;position:absolute;left:25%;">Made by Ondra/Andrew<br> contact: ondra.mic2@gmail.com</p>
                  <div class="time-header">
                    <img src="../../public/infoImg/sun.png">
                    <ion-label>
                      {{ date_time }}
                    </ion-label>
                  </div>
                </ion-toolbar>
             
              </ion-header></br>
              <ion-list>
                  <ion-item class="radio-station" v-for="(radio,index) in data_json" :key="radio.stationuuid">
                    <ion-toolbar :class="{'custom_back' : index % 2 === 0}" >

  
                      <ion-label class="heading-label">{{radio.name}}</ion-label>
                      <ion-label>Country: {{ radio.country }}</ion-label>
                      <ion-label class="last-label"> <a v-show="!showPause" @click="playAudio(index)" target="_blank"><img src="../../public/play.png" /></a><ion-menu-button @click="display_info(index)" ><img src="../../public/info.png" /></ion-menu-button>
                        <img @click="removeItem(index)" class="remove-img" src="../../public/infoImg/remove.png"   />
                      </ion-label>
                      <audio :class="'audio-' + index">
                        <source :src="radio.url" type="audio/mpeg">
                        Your browser does not support the audio element.
                      </audio>
                      
                    </ion-toolbar>
                     
                  </ion-item>
            
              
                </ion-list>
                </ion-col>
            </ion-row>
          </ion-grid>
        </ion-content>

        <audio class="btn-audio" src="../../public/click.wav"></audio>
</template>

<script setup lang="ts">
import { IonContent,IonButton,IonGrid,IonRow,IonCol,IonList,IonItem,IonLabel,modalController,IonMenuButton,IonRange,loadingController,IonHeader,IonToolbar,IonTitle } from '@ionic/vue';
import MainMenu from './MainMenu.vue'
import MainHeader from './MainHeader.vue';
import InfoPage from './InfoPage.vue';

import { ref,onMounted } from 'vue';


let showPause: Boolean = ref(false);
let current_playing: Object = ref({});
//const url = 'https://developer.jamendo.com/v3.0/radios/stream';
const base_country_url = `https://nl1.api.radio-browser.info/json/stations/bycountry`;
let data_json = ref([]);
let country_search = ref('czechia');
let date_time = ref(null);

onMounted(()=>{
  date_time.value = new Date().toDateString();

});

const muteAudio = () => {
      const audio_element = document.querySelector(`.audio-${current_playing.value.index}`);
      audio_element.muted = !audio_element.muted;
}

const display_info = (index : Number) => {
  current_playing.value.index = index;
  current_playing.value.station = data_json.value[index];
}

const showLoading = async () => {
        const loading = await loadingController.create({
          message: 'Loading',
        });

        loading.present();
};

const removeItem = (index) => {
    data_json.value.splice(index,1);
}

const manageVol = (e) => {
  const audio = document.querySelector(`.audio-${current_playing.value.index}`);
  if (audio != null) audio.volume = e.detail.value / 100;
  console.log(audio.volume);
}

const playAudio = (index: Number) => {
  const audio_element = document.querySelector(`.audio-${index}`);
  console.log(audio_element);
  showPause.value = !showPause.value;
  current_playing.value.index = index;
  current_playing.value.station_name = data_json.value[index].name;
  audio_element.play();
};

const pauseAudio = () => {
  if(current_playing.value != -1) {
    const audio_element = document.querySelector(`.audio-${current_playing.value.index}`);
    audio_element.pause();
    showPause.value = !showPause.value;
  }
}

const fetchData = async (country: String) => {
     const url = `${base_country_url}/${country}`;
      try {
        const response = await fetch(url);
        if (!response.ok) {
          throw new Error('Network response was not ok ' + response.statusText);
        }
        const data = await response.json();
        data_json.value = data;   
        console.log(data);
      } catch (error) {
        console.error('There has been a problem with your fetch operation:', error);
      }

      loadingController.dismiss(null,'stop');
    };


  const pinFormatter = (value : Number) => {
      return `${value}%`;
  }


showLoading();  

fetchData(country_search.value);

const message = ref('');

const openModal = async () => {
    const audio_html = document.querySelector('.btn-audio');
    audio_html.play();
    const modal = await modalController.create({
      component: MainMenu,
    });
    modal.present();
    const { data, role } = await modal.onWillDismiss();
    if (role === 'confirm') {
      country_search.value = data;
      fetchData(country_search.value);
    }
  };


</script>

<style scoped>

ion-range {
  --bar-background: #000000;
  --pin-background: #030303;
  --knob-size: 26px;
  --bar-height: 8px;
}

.time-header {
  width:100%;
}
.time-header  img {
  width: 15%;
  margin-left: -30%;
}
.time-header > ion-label {
  margin-left: 15%;
}

ion-label > .remove-img {
  position: absolute;
  width: 7%;
  left: 90%;
  top: 65%;
  z-index: 1000;
}

.play-menu {
  z-index: 400;
  position:fixed;
  width:55%;
  height:15%;
  top:70%;
  left:40%;
  background:white;
  border:1px solid black;
  opacity:0.9;
  box-shadow: 0px 0px 10px black;

}
.play-menu .radio-img {
  position: absolute;
  top: 65%;
}
.radio-img:active {
  transform: scale(1.1);
}
.muteTag:active {
  transform: scale(0.8);
}
.volume-range {
  position: absolute;
  width: 60%;
  top: 8%;
  left: 30%;
}
.heading-label {
  margin-top: 5%;
}

.play-menu img {
  width:20%;
  opacity:1;
}
.menu-icon {
  top:75%;
  z-index: 400;
  position:fixed;
  width:30%;
  
}
.menu-icon:active {
  width:20%;
  transform:translate(25%,25%);
  
}

.custom_back {
  --background:rgb(252, 202, 3);;
}
.menu-list {
  background:rgb(0, 0, 0.3);
  width:100%;
}
.menu-list > ion-button {
  width:60%;
  font-size:60%;
  margin-left:50%;
  transform:translateX(-25%);
  font-weight:bold;
  letter-spacing:5%;
}

ion-label {
  margin-left:5%;
 
}

a > img {
  top: 65%;
  position: absolute;
  left: 75%;
  width: 7%;
  z-index: 600;
}
.heading-tool {
  border-bottom: 3px solid black;
}

.radio-station {
  border: none;
}
.heading-label {
  font-weight: bold;

}

.radio-grid {

  border: 2px solid white;
}
ion-item {

  font-size: 76%;
  

}

.last-label > ion-menu-button > img {
  width: 7%;
  margin-left: 25%;
}

ion-content{
    --background:white;
 }

ion-toolbar {
  width: 100%;
  height:110%;


}

 ion-list {
  width: 95%;


 }

 ion-title {
  font-weight: bold;
 }
 ion-col {
    border: solid 3px rgb(0, 0, 0.3);;
    border-top: none;
    color: #fff;
    text-align: center;
  }
.menu-col {
    background:rgb(0, 0, 0.3);
    color:rgb(252, 202, 3);
    font-weight:bold;
    font-size: 100%;
    margin:0px auto;

    
}
.row {
    height:100vh;
    width:105vw;
    border:none;
}
.middle-radio {
    color:rgb(252, 202, 3);
    font-weight:bold;
    font-size: 100%;
    border:none;
}

</style>