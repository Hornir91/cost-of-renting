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
      dataJson: null
    };
  },
  mounted() {
    axios.get(process.env.BASE_URL + 'data.json')
    .then(response => {
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
      let totalCost = 0;
      
      for (const sectionType in this.selectedEquipment) {
        const itemId = this.selectedEquipment[sectionType];
        
        const section = this.dataJson.find(section => section.type === 'sectionDefinition');
        const item = section.items.find(item => item.id === itemId);
        
        if (item) {
          totalCost += this.calculateItemCost(item);
        }
      }
      
      return totalCost;
    },
    calculateItemCost(item) {
  let itemCost = 0;

  if (Array.isArray(item.operationsIfEnabled)) {
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
  background-color: #00FF00;
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
