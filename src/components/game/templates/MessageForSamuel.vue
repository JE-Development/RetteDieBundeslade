<style>

.half-layout{
  width: 50%;
}

.speak-layout{
  width: 50%;
}

.letter{
  transform: translateY(80px);
  width: 100px;
}
</style>

<template>
  <div class="max-width center-horizontal">
    <div class="center-horizontal" style="width: 100%; max-width: 400px;">
      <div class="half-layout">

        <div class="relative center pointer max-width">
          <img src="../../../assets/tasks/man_speaking.png" v-if="mouthOpen">
          <img src="../../../assets/tasks/man_silent.png" v-else>
          <div class="absolute">
            <transition name="brief">
              <img src="../../../assets/tasks/schriftrolle.png" class="letter" v-if="showBrief">
            </transition>
          </div>
        </div>
      </div>
      <div class="speak-layout">
        <p>{{speak}}</p>
      </div>
    </div>
  </div>

  <div class="center-horizontal">
    <UIButton title="Brief annehmen" @clicked="onBrief" v-if="!isInformed"/>
  </div>
</template>


<script>

import UIButton from "@/components/views/UIButton.vue";

export default {
    name: "MessageForSamuel",
    components: {UIButton},
    data() {
        return {
          mouthOpen: false,
          speak: "Ich habe einen Brief für Samuel. Übergebe ihm diesen Brief.",
          isInformed: false,
          showBrief: true,
          socket: null
        };
    },

    created() {

    },
    mounted() {
      this.socket = new WebSocket(import.meta.env.VITE_SERVER_URL);

      this.socket.addEventListener('open', (event) => {
        let dat = {
          type: "register",
          func: "replaceClient",
          player: this.getCookies("username")
        };
        this.send(dat)

      });

      this.socket.addEventListener('message', (event) => {
        const message = JSON.parse(event.data)
        console.log(message)
      });
    },


    methods: {

      async createSpeakEffect(q){
        this.speak = ""
        this.mouthOpen = true
        let chars = q.split('')
        for(let i = 0; i < chars.length; i++) {
          await this.timeout(30)
          this.speak = this.speak + chars[i]
        }
        this.mouthOpen = false
      },

      onBrief(){

        let arr = this.$route.fullPath.split("/")
        let g = arr[1]
        let t = arr[2]

        let dat = {
          type: "engine",
          func: "nextTask",
          g: g,
          t: t,
          player: this.getCookies("username")
        }

        this.send(dat)

        this.showBrief = false
        this.isInformed = true
        this.createSpeakEffect("Schließe die Task und schaue auf die Karte. Dort wirst du ein gelben Punkt sehen und dort gebe Samuel den Brief.")
      },

      timeout(ms) {
        return new Promise(resolve => setTimeout(resolve, ms));
      },

      onFinished() {
        this.$emit("finished")
      },

      send(dat){
        this.socket.send(JSON.stringify(dat))
      },

      getCookies(key){
        return this.$cookies.get(key);
      },
      setCookies(key, value){
        return this.$cookies.set(key, value, 2147483647);
      },
    }
}
</script>