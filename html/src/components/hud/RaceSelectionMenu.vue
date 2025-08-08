<template>
  <div class="race-selection-overlay" v-if="showMenu" @click="closeMenu">
    <div class="race-selection-container" @click.stop>
      <div class="menu-header">
        <h2 class="menu-title">{{ translate('select_race_to_join') }}</h2>
        <div class="close-hint">{{ translate('press_esc_to_close') }}</div>
      </div>
      
      <div class="races-grid" v-if="availableRaces.length > 0">
        <div 
          v-for="race in availableRaces" 
          :key="race.raceId"
          class="race-card"
          :class="{ 'selected': selectedRaceIndex === race.index }"
          @click="selectRace(race.index)"
          @dblclick="joinSelectedRace"
        >
          <div class="race-card-content">
            <div class="race-header">
              <h3 class="race-name">{{ race.trackName }}</h3>
              <div class="race-status">
                <v-chip size="small" color="success" v-if="race.automated">{{ translate('automated') }}</v-chip>
                <v-chip size="small" color="orange" v-if="race.ranked">{{ translate('ranked') }}</v-chip>
              </div>
            </div>
            
            <div class="race-details">
              <div class="detail-row">
                <v-icon icon="mdi-go-kart-track" size="small"></v-icon>
                <span>{{ getLapsText(race) }}</span>
              </div>
              <div class="detail-row" v-if="race.buyIn > 0">
                <v-icon icon="mdi-cash" size="small"></v-icon>
                <span>{{ translate('currency_text') }}{{ race.buyIn }}</span>
              </div>
              <div class="detail-row">
                <v-icon icon="mdi-account-group" size="small"></v-icon>
                <span>{{ race.racers || 0 }} {{ translate('racers') }}</span>
              </div>
              <div class="detail-row" v-if="race.maxClass">
                <v-icon icon="mdi-car-info" size="small"></v-icon>
                <span>{{ translate('max_class') }}: {{ race.maxClass }}</span>
              </div>
              <div class="detail-row">
                <v-icon icon="mdi-timer" size="small"></v-icon>
                <span>{{ getTimeRemaining(race) }}</span>
              </div>
            </div>
            
            <div class="race-host">
              <v-icon icon="mdi-account-star" size="small"></v-icon>
              <span>{{ race.hostName || translate('automated') }}</span>
            </div>
          </div>
          
          <div class="selection-indicator" v-if="selectedRaceIndex === race.index">
            <v-icon icon="mdi-check-circle" color="success"></v-icon>
          </div>
        </div>
      </div>
      
      <div class="no-races" v-else>
        <v-icon icon="mdi-racing-helmet" size="large" class="mb-4"></v-icon>
        <h3>{{ translate('no_races_available') }}</h3>
        <p>{{ translate('check_back_later') }}</p>
      </div>
      
      <div class="menu-footer" v-if="availableRaces.length > 0">
        <div class="controls-hint">
          <span>{{ translate('use_mouse_to_select') }}</span>
          <span>{{ translate('double_click_to_join') }}</span>
        </div>
        <div class="action-buttons">
          <v-btn 
            variant="outlined" 
            @click="closeMenu"
            rounded="xl"
          >
            {{ translate('cancel') }}
          </v-btn>
          <v-btn 
            variant="flat" 
            color="success" 
            @click="joinSelectedRace"
            :disabled="selectedRaceIndex === null"
            rounded="xl"
          >
            {{ translate('join_race') }}
          </v-btn>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref, onMounted, onUnmounted } from 'vue';
import { translate } from '@/helpers/translate';
import { useGlobalStore } from '@/store/global';
import api from '@/api/axios';
import { closeApp } from '@/helpers/closeApp';

const globalStore = useGlobalStore();
const selectedRaceIndex = ref<number | null>(null);

const showMenu = computed(() => globalStore.showRaceSelectionMenu);
const availableRaces = computed(() => {
  return globalStore.availableAutoRaces.map((race, index) => ({
    ...race,
    index
  }));
});

const selectRace = (index: number) => {
  selectedRaceIndex.value = index;
};

const joinSelectedRace = async () => {
  if (selectedRaceIndex.value !== null) {
    const selectedRace = availableRaces.value[selectedRaceIndex.value];
    if (selectedRace) {
      const res = await api.post("UiJoinRace", JSON.stringify(selectedRace.raceId));
      if (res.data) {
        closeMenu();
        closeApp();
      }
    }
  }
};

const closeMenu = () => {
  globalStore.showRaceSelectionMenu = false;
  selectedRaceIndex.value = null;
};

const getLapsText = (race: any) => {
  if (race.laps === 0) return translate('sprint');
  if (race.laps === -1) return translate('elimination');
  return `${race.laps} ${translate('laps')}`;
};

const getTimeRemaining = (race: any) => {
  // This would need to be implemented based on your race expiration system
  return translate('starting_soon');
};

const handleKeyPress = (event: KeyboardEvent) => {
  if (event.key === 'Escape') {
    closeMenu();
  }
};

onMounted(() => {
  document.addEventListener('keydown', handleKeyPress);
});

onUnmounted(() => {
  document.removeEventListener('keydown', handleKeyPress);
});
</script>

<style scoped lang="scss">
.race-selection-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(5px);
  z-index: 2000;
  display: flex;
  align-items: center;
  justify-content: center;
  animation: fadeIn 0.3s ease-out;
}

.race-selection-container {
  width: 90vw;
  max-width: 1200px;
  height: 80vh;
  background: linear-gradient(135deg, #1a1a1a 0%, #2d2d2d 100%);
  border-radius: 16px;
  border: 2px solid rgba(255, 255, 255, 0.1);
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.5);
  display: flex;
  flex-direction: column;
  overflow: hidden;
}

.menu-header {
  padding: 24px 32px;
  background: linear-gradient(90deg, rgba(255, 255, 255, 0.05) 0%, rgba(255, 255, 255, 0.02) 100%);
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.menu-title {
  font-size: 2rem;
  font-weight: bold;
  color: white;
  margin: 0;
  text-transform: uppercase;
  letter-spacing: 2px;
}

.close-hint {
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.9rem;
}

.races-grid {
  flex: 1;
  padding: 24px;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 20px;
  overflow-y: auto;
  scrollbar-width: thin;
  scrollbar-color: rgba(255, 255, 255, 0.3) transparent;
}

.race-card {
  background: linear-gradient(145deg, #2a2a2a 0%, #1e1e1e 100%);
  border: 2px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.race-card:hover {
  border-color: rgba(255, 255, 255, 0.3);
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.3);
}

.race-card.selected {
  border-color: rgb(var(--v-theme-success));
  background: linear-gradient(145deg, rgba(76, 175, 80, 0.1) 0%, rgba(76, 175, 80, 0.05) 100%);
  transform: translateY(-4px);
  box-shadow: 0 12px 30px rgba(76, 175, 80, 0.2);
}

.race-card-content {
  position: relative;
  z-index: 1;
}

.race-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 16px;
}

.race-name {
  font-size: 1.3rem;
  font-weight: bold;
  color: white;
  margin: 0;
  line-height: 1.2;
}

.race-status {
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
}

.race-details {
  display: flex;
  flex-direction: column;
  gap: 8px;
  margin-bottom: 16px;
}

.detail-row {
  display: flex;
  align-items: center;
  gap: 8px;
  color: rgba(255, 255, 255, 0.8);
  font-size: 0.9rem;
}

.race-host {
  display: flex;
  align-items: center;
  gap: 8px;
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.85rem;
  padding-top: 12px;
  border-top: 1px solid rgba(255, 255, 255, 0.1);
}

.selection-indicator {
  position: absolute;
  top: 12px;
  right: 12px;
  z-index: 2;
}

.no-races {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: rgba(255, 255, 255, 0.6);
  text-align: center;
}

.menu-footer {
  padding: 20px 32px;
  background: rgba(0, 0, 0, 0.3);
  border-top: 1px solid rgba(255, 255, 255, 0.1);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.controls-hint {
  display: flex;
  flex-direction: column;
  gap: 4px;
  color: rgba(255, 255, 255, 0.6);
  font-size: 0.85rem;
}

.action-buttons {
  display: flex;
  gap: 12px;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}

/* Custom scrollbar */
.races-grid::-webkit-scrollbar {
  width: 8px;
}

.races-grid::-webkit-scrollbar-track {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 4px;
}

.races-grid::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.3);
  border-radius: 4px;
}

.races-grid::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 255, 255, 0.5);
}
</style>