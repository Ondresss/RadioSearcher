<template>
    <ion-menu content-id="main-content">
      <ion-header class="radio-header">
        <ion-toolbar>
          <ion-title>Station</ion-title>
        </ion-toolbar>
      </ion-header>
      <ion-content class="ion-padding" v-if="current_selected.station">
        <ion-card-header class="info-header">
          {{current_selected.station.name}}
        </ion-card-header>
        <ion-card>
          <ion-card-content>
            <ion-list>
              <ion-label style="font-weight: bold;" position="floating">Bitrate</ion-label>
              <ion-item>
                <ion-thumbnail slot="start">
                  <img alt="Silhouette of mountains" src="../../public/infoImg/bitrate.png" />
                </ion-thumbnail>
                <ion-label >{{current_selected.station.bitrate}}ps</ion-label>
              </ion-item>
              
              <ion-label style="font-weight: bold;" position="floating">Language</ion-label>
              <ion-item>
                <ion-thumbnail  slot="start">
                  <img alt="Silhouette of mountains" src="../../public/infoImg/lang.png" />
                </ion-thumbnail>
                <ion-label>{{current_selected.station.language}}</ion-label>
              </ion-item>

              <ion-label style="font-weight: bold;" position="floating">Homepage</ion-label>
              <ion-item>
                <ion-thumbnail  slot="start">
                  <img alt="Silhouette of mountains" src="../../public/infoImg/home.png" />
                </ion-thumbnail>
                <ion-label><a style="font-size: 70%;" :href="current_selected.station.homepage">{{current_selected.station.homepage}}</a></ion-label>
              </ion-item>

              <ion-label style="font-weight: bold;" position="floating">Codec</ion-label>
              <ion-item>
                <ion-thumbnail  slot="start">
                  <img alt="Silhouette of mountains" src="../../public/infoImg/codec.png" />
                </ion-thumbnail>
                <ion-label>{{current_selected.station.codec}}</ion-label>
              </ion-item>
      
          
            </ion-list>
          </ion-card-content>

        </ion-card>
      </ion-content>
    </ion-menu>
   
  </template>
  
  <script setup lang="ts">
    import { IonButtons, IonContent, IonHeader, IonMenu, IonMenuButton, IonPage, IonTitle, IonToolbar,
      IonCard, IonCardContent, IonCardHeader, IonCardSubtitle, IonCardTitle,IonItem,IonThumbnail,IonList,IonLabel
     } from '@ionic/vue';
    import { defineComponent,ref } from 'vue';
    const props = defineProps({
      current_selected: {
        type: Object,
        required: true
      }
    });
    console.log(props.current_selected);
  </script>

<style scoped>
.radio-header {
  text-align: center;
  --ion-background-color:rgb(0, 0, 0.3);
  --ion-text-color:rgb(252, 202, 3);
}
ion-title {
  font-weight: bold;
  font-size: 200%;
}
.info-header {
  text-align: center;
  font-weight: bold;
  word-spacing: 50%;
  font-size: 100%;
  word-wrap:break-word;
}
</style>