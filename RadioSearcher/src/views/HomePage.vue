<template>
  <ion-page>

    <ion-content :fullscreen="true">
      <MainContent />
     
    </ion-content>

    
  </ion-page>
</template>

<script setup lang="ts">
import { IonButton ,IonContent, IonHeader, IonPage, IonTitle, IonToolbar,IonModal,IonList,IonLabel,IonItem } from '@ionic/vue';
import MainContent from '../components/MainContent.vue';






</script>

<style scoped>

#container {
  text-align: center;
  
  position: absolute;
  left: 0;
  right: 0;
  top: 50%;
  transform: translateY(-50%);
}

#container strong {
  font-size: 20px;
  line-height: 26px;
}

#container p {
  font-size: 16px;
  line-height: 22px;
  
  color: #8c8c8c;
  
  margin: 0;
}

#container a {
  text-decoration: none;
}
</style>
