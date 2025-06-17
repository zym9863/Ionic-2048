<template>
  <ion-page>
    <ion-header :translucent="true">
      <ion-toolbar>
        <ion-title>2048 游戏</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content :fullscreen="true">
      <ion-header collapse="condense">
        <ion-toolbar>
          <ion-title size="large">2048 游戏</ion-title>
        </ion-toolbar>
      </ion-header>

      <div class="game-page">
        <Game2048 />
      </div>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { IonContent, IonHeader, IonPage, IonTitle, IonToolbar } from '@ionic/vue';
import Game2048 from '@/components/Game2048.vue';
</script>

<style scoped>
.game-page {
  padding: 20px 0;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

@media (max-width: 768px) {
  .game-page {
    padding: 10px 0;
    align-items: flex-start;
  }
}
</style>
