<template>
  <div class="auto-race-notification" v-if="showNotification">
    <v-card class="notification-card" rounded="lg" border>
      <v-card-title class="notification-title">
        <v-icon icon="mdi-racing-helmet" class="mr-2"></v-icon>
        {{ translate('auto_race_available') }}
      </v-card-title>
      <v-card-text class="notification-content">
        <div class="race-info">
          <div class="race-name">{{ autoRaceData?.trackName || 'Unknown Track' }}</div>
          <div class="race-details">
            <v-chip size="small" color="primary">{{ lapsText }}</v-chip>
            <v-chip size="small" color="green" v-if="autoRaceData?.buyIn">{{ translate('currency_text') }}{{ autoRaceData.buyIn }}</v-chip>
            <v-chip size="small" color="orange" v-if="autoRaceData?.ranked">{{ translate('ranked') }}</v-chip>
          </div>
        </div>
        <div class="join-prompt">
          <v-icon icon="mdi-keyboard" class="mr-1"></v-icon>
          {{ translate('press_e_to_join') }}
        </div>
      </v-card-text>
    </v-card>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { translate } from '@/helpers/translate';
import { useGlobalStore } from '@/store/global';

const globalStore = useGlobalStore();

const showNotification = computed(() => !!globalStore.autoRaceNotification);
const autoRaceData = computed(() => globalStore.autoRaceNotification);

const lapsText = computed(() => {
  if (!autoRaceData.value) return '';
  if (autoRaceData.value.laps === 0) return translate('sprint');
  if (autoRaceData.value.laps === -1) return translate('elimination');
  return `${autoRaceData.value.laps} ${translate('laps')}`;
});
</script>

<style scoped lang="scss">
.auto-race-notification {
  position: fixed;
  top: 50%;
  right: 2rem;
  transform: translateY(-50%);
  z-index: 1500;
  animation: slideInRight 0.5s ease-out;
}

.notification-card {
  width: 320px;
  background: rgba(0, 0, 0, 0.9) !important;
  backdrop-filter: blur(10px);
  border: 2px solid rgb(var(--v-theme-primary));
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
}

.notification-title {
  font-size: 1.1rem;
  font-weight: bold;
  padding: 12px 16px 8px 16px;
  color: rgb(var(--v-theme-primary));
  display: flex;
  align-items: center;
}

.notification-content {
  padding: 8px 16px 16px 16px;
}

.race-info {
  margin-bottom: 12px;
}

.race-name {
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 8px;
  color: white;
}

.race-details {
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
}

.join-prompt {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 8px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  font-weight: bold;
  color: white;
  animation: pulse 2s infinite;
}

@keyframes slideInRight {
  from {
    transform: translateY(-50%) translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateY(-50%) translateX(0);
    opacity: 1;
  }
}

@keyframes pulse {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.7;
  }
}
</style>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { translate } from '@/helpers/translate';
import { useGlobalStore } from '@/store/global';

const globalStore = useGlobalStore();

const showNotification = computed(() => !!globalStore.autoRaceNotification);
const autoRaceData = computed(() => globalStore.autoRaceNotification);

const lapsText = computed(() => {
  if (!autoRaceData.value) return '';
  if (autoRaceData.value.laps === 0) return translate('sprint');
  if (autoRaceData.value.laps === -1) return translate('elimination');
  return `${autoRaceData.value.laps} ${translate('laps')}`;
});
</script>

<style scoped lang="scss">
.auto-race-notification {
  position: fixed;
  top: 50%;
  right: 2rem;
  transform: translateY(-50%);
  z-index: 1500;
  animation: slideInRight 0.5s ease-out;
}

.notification-card {
  width: 320px;
  background: rgba(0, 0, 0, 0.9) !important;
  backdrop-filter: blur(10px);
  border: 2px solid rgb(var(--v-theme-primary));
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
}

.notification-title {
  font-size: 1.1rem;
  font-weight: bold;
  padding: 12px 16px 8px 16px;
  color: rgb(var(--v-theme-primary));
  display: flex;
  align-items: center;
}

.notification-content {
  padding: 8px 16px 16px 16px;
}

.race-info {
  margin-bottom: 12px;
}

.race-name {
  font-size: 1.2rem;
  font-weight: bold;
  margin-bottom: 8px;
  color: white;
}

.race-details {
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
}

.join-prompt {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 8px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  font-weight: bold;
  color: white;
  animation: pulse 2s infinite;
}

@keyframes slideInRight {
  from {
    transform: translateY(-50%) translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateY(-50%) translateX(0);
    opacity: 1;
  }
}

@keyframes pulse {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.7;
  }
}
</style>
</template>