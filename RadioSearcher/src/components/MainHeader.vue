<template>
    <ion-header :class="'radio-header'" :translucent="true">
        <ion-toolbar >
          <ion-button @click="openModal" color="light"   :class="'radio-search'">Search</ion-button>
          <ion-title  :class="'radio-title'">Radio Searcher</ion-title>
          <img :class="'radio-title-img'" src="../../public/radioIcon.png" alt="icon">
          <img @click="fetchData(country)" :class="'radio-title-img refresh'" src="../../public/refresh.png" alt="icon">
        </ion-toolbar>
        <ion-toolbar v-if="show_tool" class = "drop-view">
        <ion-label>{{message}}/{{country}}</ion-label>
        </ion-toolbar>
      </ion-header>


     
</template>

<script setup lang="ts">
import {IonHeader,IonTitle,IonToolbar,IonButton,IonButtons,IonItem,IonContent,IonModal,IonInput,modalController,IonLabel} from '@ionic/vue';
import { IonIcon } from '@ionic/vue';
import { searchOutline } from 'ionicons/icons';
import SearchModal from './SearchModal.vue';
import { ref } from 'vue';

const props = defineProps({
  country: {
    type: String,
    required: false,
    default: ''
  },
  fetchData: {
    type:Function,
    required: true
  }
});

console.log('Props:', props);

const message = ref();
let show_tool = ref(false);
const rem_timer = () => {
    setTimeout(() => {
        show_tool.value = false;
    }, 3000);
   
}


const openModal = async () => {
    const modal = await modalController.create({
      componentProps: {
        country:props.country,
      },
      component: SearchModal,
    });
  
    modal.present();

    const { data, role } = await modal.onWillDismiss();

    if (role === 'confirm') {
        show_tool.value = true;
        message.value = `You just searched for: ${data}`;
        rem_timer();

    }
  };
</script>

<style scoped >

.drop-view {
    --background:rgb(0, 0, 0.3);
    border-top: 2px solid rgb(252, 202, 3);
}

.radio-header {
    text-align: center;
    --ion-background-color:rgb(0, 0, 0.3);
    --ion-text-color:rgb(252, 202, 3);
  }
  .radio-title {
    font-size: 150%;
    font-weight: bold;
    word-spacing: 0.1em;
    line-height: 80%;
  }
  .radio-title-img.refresh {
    position: absolute;
    width: 10%;
    height: 55%;
    left: 90%;
    top: 25%;
    transition: transform 0.2s ease-out;
  }
  .radio-title-img.refresh:active {
    transform: rotate(-360deg);
    transition: transform 1s;
  }
  .radio-title-img {
    position: absolute;
    width: 7%;
    height: 50%;
    left: 80%;
    top: 25%;
  }
  .radio-search {
    width:15%;
    height:10%;
    position:absolute;
    z-index:200;
    left:1%;
    top:10%;
    font-size:65%;
    text-align:center;
    font-weight:bold;

  }
 
</style>