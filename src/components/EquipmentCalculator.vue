<template>
  <div class="equipment-calculator">
    <div v-for="(section, index) in equipmentSections" :key="index">
      <div class="question">
        <h2>{{ section.name }}</h2>
        <div>
          <div v-for="item in section.items" :key="item.id">
            <button
            class="equipment-button"
  @click="selectEquipment(index, item.name)"
  :class="{ 'selected': selectedEquipment[index] === item.name }"
>
              <div class="equipment-option">
                <div class="equipment-image">
                  <img :src="item.imgHref" :alt="item.name" />
                </div>
                <div class="equipment-info">
                  <div class="equipment-name">
                    <strong>{{ item.name }}</strong>
                  </div>
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
      showSummary: false,
      dataJson: null // Nowa właściwość do przechowywania danych z data.json
    };
  },
  mounted() {
    axios.get(process.env.BASE_URL + 'data.json')
    .then(response => {
      // Zapisz dane z data.json w dataJson
      this.dataJson = response.data;
      this.equipmentSections = this.dataJson.filter(section => section.type === 'sectionDefinition')[0].items;
    })
    .catch(error => {
      console.error('Error loading data:', error);
    });
  },
  methods: {
    selectEquipment(sectionIndex, itemName) {
  this.selectedEquipment[sectionIndex] = itemName;
},

    calculateCost() {
      // Inicjalizuj zmienną na całkowity koszt
      let totalCost = 0;
      
      // Iteruj przez wybrane elementy w selectedEquipment
      for (const sectionType in this.selectedEquipment) {
        const itemId = this.selectedEquipment[sectionType];
        
        // Znajdź wybrany element w danych
        const section = this.dataJson.find(section => section.type === 'sectionDefinition');
        const item = section.items.find(item => item.id === itemId);
        
        // Jeśli element został znaleziony, dodaj jego koszt do całkowitego kosztu
        if (item) {
          totalCost += this.calculateItemCost(item);
        }
      }
      
      return totalCost;
    },
    calculateItemCost(item) {
  // Inicjalizuj koszt elementu
  let itemCost = 0;

  // Sprawdź, czy item.operationsIfEnabled jest tablicą, zanim będziesz próbować go przeglądać
  if (Array.isArray(item.operationsIfEnabled)) {
    // Przeglądaj operacje elementu i oblicz koszt
    for (const operation of item.operationsIfEnabled) {
      if (operation.type === 'add') {
        itemCost += operation.number;
      } else if (operation.type === 'multiply') {
        itemCost *= operation.number;
      }
    }
  }

  return itemCost;
}

  }
};
</script>


<style scoped>
.equipment-button.selected {
  background-color: #00FF00; /* Zielony kolor dla zaznaczonych opcji */
}
.equipment-button {
  width: 100%;
  padding: 0;
  margin: 5px;
  background-color: #ffffff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
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
  padding: 15px 20px;
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
  text-align: left;
}

.question {
  background-color: #f44336;
  padding: 10px;
  margin-bottom: 10px;
  color: white;
  padding: 50px;
}
@media (min-width: 768px) {
  .question {
    /* margin-bottom: 320px; */
  }
}

.question-divider {
  height: 10px;
}

.summary {
  /* Stylizacja podsumowania */
}
</style>
