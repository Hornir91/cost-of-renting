<template>
  <div class="equipment-calculator">
    <div v-for="(section, index) in equipmentSections" :key="section.type">
      <div class="question">
        <h2>{{ section.name }}</h2>
        <div>
          <div v-for="item in section.items" :key="item.id">
            <button
              class="equipment-button"
              @click="selectEquipment(section.type, item.id)"
              :class="{ 'selected': selectedEquipment[section.type] === item.id }"
            >
              <div class="equipment-option">
                <div class="equipment-image">
                  <img :src="item.imgHref" :alt="item.name" />
                </div>
                <div class="equipment-info">
                  <div class="equipment-name">{{ item.name }}</div>
                  <div class="equipment-description">{{ item.description }}</div>
                </div>
              </div>
            </button>
          </div>
        </div>
      </div>
      <div v-if="index < equipmentSections.length - 1" class="question-divider"></div>
    </div>
    <div>
      <button @click="showSummary = true">Oblicz koszt</button>
      <div v-if="showSummary" class="summary">
        <h3>Podsumowanie</h3>
        <p>Całkowity koszt wynajmu: {{ calculateCost() }} zł</p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'EquipmentCalculator',
  data() {
    return {
      equipmentSections: [],
      selectedEquipment: {},
      showSummary: false
    };
  },
  mounted() {
    axios.get(process.env.BASE_URL + 'data.json')
    .then(response => {
      this.equipmentSections = response.data.filter(section => section.type === 'sectionDefinition')[0].items;
    })
    .catch(error => {
      console.error('Error loading data:', error);
    });
  },
  methods: {
    selectEquipment(sectionType, itemId) {
      this.$set(this.selectedEquipment, sectionType, itemId);
    },
    calculateCost() {
      // Implementuj logikę obliczania kosztu
    },
    findSelectedEquipment() {
      // Implementuj logikę znajdowania wybranego sprzętu
    }
  }
};
</script>

<style scoped>
.equipment-button {
  padding: 0;
  margin: 5px;
  cursor: pointer;
  background-color: #ffffff;
  border-radius: 15px;
  border: none;
  width: 100%;
  transition: background-color 0.5s;
}

.equipment-button:hover {
  background-color: #a4eba6;
}

.equipment-button.selected .equipment-option {
 /* to do */

}

.equipment-option {
  display: flex;
  align-items: center;

  padding: 5px 10px;
  transition: background-color 0.3s;
}

.equipment-image {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  overflow: hidden;
  margin-right: 10px;
}

.equipment-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.equipment-name {
  flex: 1;
}

.question {
  background-color: #f44336;
  padding: 10px;
  margin-bottom: 10px;
  color: white;

}

.question-divider {
  height: 10px;
}

.summary {
  /* Stylizacja podsumowania */
}
</style>
