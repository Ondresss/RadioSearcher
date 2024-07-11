<template>
    <ion-header class="radio-header">
      <ion-toolbar>
        <ion-buttons slot="start">
          <ion-button color="medium" @click="cancel">Cancel</ion-button>
        </ion-buttons>
        <ion-title>Available countries</ion-title>
      </ion-toolbar>
    </ion-header>
    <ion-content class="ion-padding">
        <ion-list>
            <ion-item @click="pick_country(country.name.common)" :class="{'custom' : index % 2 == 0}"  v-for="(country,index) in contries_json" :key="country.cca2">
                <img class="flag-img" :src="country.flags.png" />
                <div class="info-place">
                    <ion-label >{{country.continents[0]}}/<span style="font-weight:bold;">{{country.name.common}}</span></ion-label>
                </div>
            </ion-item>
        </ion-list>


    </ion-content>
  </template>
  
  <script lang="ts" setup>
    import {
      IonContent,
      IonHeader,
      IonTitle,
      IonToolbar,
      IonButtons,
      IonButton,
      IonItem,
      IonInput,
      IonList,
      modalController,loadingController
    } from '@ionic/vue';
    import { defineComponent, ref } from 'vue';
    
    let contries_json = ref([])
    let base_country_url = `https://restcountries.com/v3.1/independent?status=true`;
    
    const showLoading = async () => {
        const loading = await loadingController.create({
          message: 'Loading',
        });

        loading.present();
    };

    showLoading();
    const pick_country = (name:String) => {
      modalController.dismiss(name, 'confirm');
    }

    const get_countries = async () => {
        try {
        const response = await fetch(base_country_url);
        if (!response.ok) {
          throw new Error('Network response was not ok ' + response.statusText);
        }
        const data = await response.json();
        contries_json.value = data;
        console.log(data);
        loadingController.dismiss(null,'stop');
      } catch (error) {
        console.error('There has been a problem with your fetch operation:', error);
      }
    }
    get_countries();
    const cancel = () => modalController.dismiss(null, 'cancel');
  </script>

<style scoped>
.radio-header {
    text-align: center;
    --ion-background-color:rgb(0, 0, 0.3);
    --ion-text-color:rgb(252, 202, 3);
  }
.flag-img {
    width:20%;
}
ion-label {
    margin-left:15%;
}
.info-place {
    width:70%;
}
.custom {
    --background:rgb(252, 202, 3);;
}

</style>