<template>
  <div class="meal-form-container card">
    <div class="form-header">
      <h2>📝 Comidas del día {{ day }}</h2>
      <button @click="$emit('close')" class="btn-close">✕</button>
    </div>

    <form @submit.prevent="submitForm">
      <div class="form-group">
        <label for="meal-text">Comida:</label>
        <input 
          id="meal-text"
          v-model="mealText" 
          type="text" 
          placeholder="Ej: Pollo con arroz"
          required
        />
      </div>

      <div class="form-group">
        <label for="meal-category">Categoría:</label>
        <select id="meal-category" v-model="mealCategory">
          <option value="Desayuno">🌅 Desayuno</option>
          <option value="Almuerzo">☀️ Almuerzo</option>
          <option value="Merienda">🍪 Merienda</option>
          <option value="Cena">🌙 Cena</option>
        </select>
      </div>

      <button type="submit" class="btn-primary">
        ➕ Agregar Comida
      </button>
    </form>

    <div v-if="meals.length > 0" class="meals-list">
      <h3>Comidas para hoy:</h3>
      <div v-for="meal in meals" :key="meal.id" class="meal-item">
        <div class="meal-content">
          <span class="meal-category">{{ meal.category }}</span>
          <span class="meal-text">{{ meal.text }}</span>
        </div>
        <button 
          @click="$emit('delete-meal', day, meal.id)"
          class="btn-delete"
        >
          🗑️
        </button>
      </div>
    </div>

    <div v-else class="empty-state">
      <p>No hay comidas agendadas para este día</p>
      <p>¡Agrega una arriba! 👆</p>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue'

export default {
  name: 'MealForm',
  props: {
    day: Number,
    meals: Array
  },
  emits: ['add-meal', 'delete-meal', 'close'],
  setup(props, { emit }) {
    const mealText = ref('')
    const mealCategory = ref('Almuerzo')

    function submitForm() {
      if (mealText.value.trim()) {
        emit('add-meal', props.day, {
          text: mealText.value,
          category: mealCategory.value
        })
        mealText.value = ''
        mealCategory.value = 'Almuerzo'
      }
    }

    return {
      mealText,
      mealCategory,
      submitForm
    }
  }
}
</script>

<style scoped>
.meal-form-container {
  background: white;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
  height: fit-content;
  position: sticky;
  top: 20px;
}

.form-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.form-header h2 {
  color: #333;
  font-size: 1.3rem;
}

.btn-close {
  background: #f44336;
  color: white;
  border: none;
  padding: 6px 12px;
  border-radius: 50%;
  cursor: pointer;
  font-size: 1.2rem;
  width: 36px;
  height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: background 0.3s ease;
}

.btn-close:hover {
  background: #da190b;
}

form {
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  margin-bottom: 6px;
  font-weight: 500;
  color: #333;
}

.form-group input,
.form-group select {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 6px;
  font-size: 14px;
  transition: border-color 0.3s ease;
}

.form-group input:focus,
.form-group select:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.meals-list {
  margin-top: 20px;
  padding-top: 20px;
  border-top: 2px solid #f0f0f0;
}

.meals-list h3 {
  color: #333;
  margin-bottom: 12px;
  font-size: 1.1rem;
}

.meal-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  background: #f9f9f9;
  border-radius: 6px;
  margin-bottom: 8px;
  border-left: 4px solid #667eea;
}

.meal-content {
  display: flex;
  align-items: center;
  gap: 10px;
  flex: 1;
}

.meal-category {
  background: #667eea;
  color: white;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 0.75rem;
  font-weight: 600;
  white-space: nowrap;
}

.meal-text {
  color: #333;
  word-break: break-word;
}

.btn-delete {
  background: none;
  border: none;
  font-size: 1.2rem;
  cursor: pointer;
  padding: 4px 8px;
  transition: transform 0.2s ease;
}

.btn-delete:hover {
  transform: scale(1.2);
}

.empty-state {
  text-align: center;
  padding: 20px;
  color: #999;
}

.empty-state p {
  margin: 8px 0;
  font-size: 0.95rem;
}

/* Responsive */
@media (max-width: 1024px) {
  .meal-form-container {
    position: static;
  }
}

@media (max-width: 768px) {
  .meal-form-container {
    padding: 15px;
  }

  .form-header h2 {
    font-size: 1.1rem;
  }
}
</style>