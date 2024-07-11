<template>
        
        <ion-header class=".radio-header"   :translucent="true">
            <ion-toolbar   >
              <ion-buttons  slot="start">
                <ion-button @click="confirm">Cancel</ion-button>
              </ion-buttons>
              <ion-title>Find station</ion-title>
              <ion-buttons slot="end">
                <ion-button @click="search_by()" :strong="true">Search</ion-button>
              </ion-buttons>
            </ion-toolbar>
          </ion-header>
          <ion-content class="ion-padding">
            <ion-item>
              <ion-input label-placement="stacked" label="Search for a radio station" v-model="name" placeholder="eg. Fun radio"></ion-input>
            </ion-item>
            <ion-item>
              <ion-button style="margin: 0px auto;" @click="get_top()">
                <span v-show="!top_listed">Get top 10 radios worldwide</span>
                <span v-show="top_listed">Hide top 10 radios worldwide</span>
              </ion-button>
            </ion-item>
            <ion-list>
              <ion-item v-for="(radio,index) in country_result " >
                <ion-toolbar id="tooling" :class="{'custom' : index % 2 == 0 }"  >
                <ion-label class="heading-label" >{{radio.name}}</ion-label>
                <ion-label>Country: {{ radio.country }}</ion-label>
                <ion-label class="last-label"> <a @click="playAudios(index,1)" target="_blank"><img src="../../public/play.png" /></a></ion-label>
                <audio :class="'audioR-' + index">
                  <source :src="radio.url" type="audio/mpeg">
                  Your browser does not support the audio element.
                </audio>
              </ion-toolbar>
              </ion-item>
       
            </ion-list>
            <ion-list v-show="top_listed">

              <ion-item :class="{'custom' : index % 2 == 0 }" v-for="(radio,index) in radios_json " >
                <ion-label>{{radio.name}} <a  @click="playAudios(index,2)" target="_blank"><img src="../../public/play.png" /></a></ion-label>
                <audio :class="'audios-' + index">
                  <source :src="radio.url" type="audio/mpeg">
                  Your browser does not support the audio element.
                </audio>
              </ion-item>
            </ion-list>

            <canvas ref="canvasRef" v-show="!top_listed" class="top-graph" >

            </canvas>

            <canvas ref="canvasCircleGraph" v-show="!top_listed" class="low-graph" >

            </canvas>


          </ion-content>
       
          
   
</template>

<script setup lang="ts">
  import {
    IonContent,
    IonHeader,
    IonTitle,
    IonToolbar,
    IonButtons,
    IonButton,
    IonItem,
    IonInput,
    IonLabel,
    modalController,
    IonList,
    IonModal,
  } from '@ionic/vue';
  import { defineComponent, ref,onMounted } from 'vue';
  const name = ref('');
  const radios_json = ref([]);
  const props = defineProps({
    country: {
      type: String,
      required: true
    },
  })

  let base_top_url = `https://nl1.api.radio-browser.info/json/stations/topvote/10`;
  const base_country_url = `https://nl1.api.radio-browser.info/json/stations/bycountry/${props.country}`;
  let top_listed = ref(false);
  const country_result = ref([]);
  const canvasRef = ref(null);
  const canvasCircleGraph = ref(null);
  let mounted = false;
  const cancel = () => modalController.dismiss(null, 'cancel');
  const confirm = () => modalController.dismiss(name.value ? name.value : 'nothing', 'confirm');

  onMounted(() => {
    const canvas : HTMLCanvasElement | null = canvasRef.value;
    if (canvas && canvas?.getContext) {
        const bars = [];
        const available_colors = ['red','green','blue','black','#155e61','purple','orange','brown','grey','#32a852'];
        const ctx = canvas.getContext('2d');
        preload_top().then(val=>{
          let xOffset = 30;
          let rect_width = 20;
          const y = 100;
          val.forEach((radio,index)=>{
              ctx.fillStyle = available_colors[index];
              ctx.fillRect(xOffset, y, rect_width, -1 * (radio.votes / 10000));
              bars.push({coordX:xOffset,coordY:y,label:`${radio.name} with votes ${radio.votes}`,width:rect_width,height:-1 * (radio.votes / 10000),color:available_colors[index]});
              xOffset+=rect_width+5;
          })
          draw_dotted_graph(val,available_colors);
          drawInfo(canvas,ctx,bars);
        })
   
        mounted = true;
    }
  });


  const playAudios = (index: Number,id: Number) => {
      let audio_element = null;
      switch(id) {
        case 1:
          audio_element = document.querySelector(`.audioR-${index}`);
          break;
        case 2:
          audio_element = document.querySelector(`.audios-${index}`);
          break;

      }
  
      console.log(audio_element);
      if(audio_element != null) {
        audio_element.play();
      }

};

  const drawInfo = (canvas : HTMLCanvasElement,ctx,bars : Array<{coordX: number,coordY:number,label:String,width:number,height:number,color: any }>) => {
    canvas.addEventListener("pointerdown",(e)=>{
        const rect = e.target.getBoundingClientRect();
        const x : number = e.x - (rect.left + window.scrollX);
        const y : number = e.y - (rect.top + window.scrollY);
        console.log(x,y);
        bars.forEach((radio,index) => {
          console.log(radio);
          if(x >= radio.coordX && x <= (radio.width + radio.coordX)
            && y >= radio.coordY && y <= (radio.coordY + radio.coordY)
          ) {
            let text_width = 230;
            ctx.clearRect(0,radio.coordY,canvas.width,canvas.height);
            ctx.fillStyle = radio.color;
            if(radio.coordX  >= canvas.width/2) {
              radio.coordX-=text_width/2;
            }
            ctx.fillText(radio.label,radio.coordX-20,radio.coordY + 15,text_width)
          }
      });


    });
  }

  const draw_dotted_graph = (radios : any,available_colors) => {
    const canvas : HTMLCanvasElement | null = canvasCircleGraph.value;
    const ctx = canvas.getContext('2d');
    ctx.strokeStyle = "black";
    const YLineHeight = 100;
    const XLineLength = 250;
    const xOffset = 20;
    const y = 0;
    let xPath = 50;
    ctx.beginPath();
    ctx.moveTo(xOffset,y+10);
    ctx.lineTo(xOffset,y+YLineHeight+20);
    ctx.moveTo(xOffset - 20,y+YLineHeight);
    ctx.lineTo( xOffset + XLineLength, y +YLineHeight);
    ctx.stroke();
    ctx.closePath();
    radios.forEach((radio,index)=>{
      ctx.fillStyle = available_colors[index];
      ctx.beginPath();
      ctx.arc(xPath,radio.votes/10000,radio.votes/40000,0,Math.PI * 2,true);
      ctx.fill();
      ctx.closePath();
      xPath+=20;


    });

  }


  const get_top = async () => {
      try {
        const response = await fetch(base_top_url);
        if(!response.ok) throw new Error("Network problem\n");
        const json = await response.json();
        radios_json.value = json;
        top_listed.value = !top_listed.value;

      }catch(error) {

      }
  }

  const preload_top = async () => {
    try {
        const response = await fetch(base_top_url);
        if(!response.ok) throw new Error("Network problem\n");
        const json = await response.json();
        return json;
      }catch(error) {
        console.log("Could not preload data");
      }
  }


  const search_by = async () => {

    try {
        const response = await fetch(base_country_url);
        if(!response.ok) throw new Error("Network problem\n");
        const json = await response.json();
        json.forEach(radio => {
          if(radio.name.includes(name.value)) {
            country_result.value.push(radio);
          }
        });
        console.log(country_result.value);
      }catch(error) {

      }
  }
</script>

<style scoped  >



.low-graph {
  width: 100%;
  height: 40%;
  background:rgb(255, 255, 255);
}
.top-graph {
  width: 100%;
  height: 40%;
  background:rgb(255, 255, 255);

}
ion-label {
  margin-left:5%;
 
}
a > img {
  top: 30%;
  position: absolute;
  left: 75%;
  width: 7%;
  z-index: 600;
}
ion-header {
    text-align: center;
    --ion-background-color:rgb(0, 0, 0.3);
    --ion-text-color:rgb(252, 202, 3);
}

ion-content{
    --background:rgb(255, 255, 255);
}
.heading-label {
  font-weight: bold;

}


.custom {
  --background: rgb(252, 202, 3);
 }
</style>