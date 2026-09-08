<template>
  <div class="app-container">
    <header class="app-header">
      <h1>🍽️ Comidas del Mes</h1>
      <p class="subtitle">Planifica tus comidas de forma fácil</p>
    </header>

    <main class="app-main">
      <Calendar 
        :year="currentYear" 
        :month="currentMonth"
        :meals="meals"
        @prev-month="prevMonth"
        @next-month="nextMonth"
        @select-day="selectDay"
      />

      <MealForm 
        v-if="selectedDay"
        :day="selectedDay"
        :meals="getMealsForDay(selectedDay)"
        @add-meal="addMeal"
        @delete-meal="deleteMeal"
        @close="selectedDay = null"
      />
    </main>

    <footer class="app-footer">
      <p>© 2024 Comidas del Mes • Hecho con Vue.js</p>
    </footer>
  </div>
</template>

<script>
import { ref, computed } from 'vue'
import Calendar from './components/Calendar.vue'
import MealForm from './components/MealForm.vue'

export default {
  name: 'App',
  components: {
    Calendar,
    MealForm
  },
  setup() {
    const today = new Date()
    const currentYear = ref(today.getFullYear())
    const currentMonth = ref(today.getMonth())
    const selectedDay = ref(null)
    const meals = ref(loadMeals())

    function loadMeals() {
      const saved = localStorage.getItem('meals')
      return saved ? JSON.parse(saved) : {}
    }

    function saveMeals() {
      localStorage.setItem('meals', JSON.stringify(meals.value))
    }

    function getMealsForDay(day) {
      const key = `${currentYear.value}-${currentMonth.value}-${day}`
      return meals.value[key] || []
    }

    function addMeal(day, meal) {
      const key = `${currentYear.value}-${currentMonth.value}-${day}`
      if (!meals.value[key]) {
        meals.value[key] = []
      }
      meals.value[key].push({
        id: Date.now(),
        text: meal.text,
        category: meal.category
      })
      saveMeals()
    }

    function deleteMeal(day, mealId) {
      const key = `${currentYear.value}-${currentMonth.value}-${day}`
      meals.value[key] = meals.value[key].filter(m => m.id !== mealId)
      saveMeals()
    }

    function prevMonth() {
      currentMonth.value--
      if (currentMonth.value < 0) {
        currentMonth.value = 11
        currentYear.value--
      }
      selectedDay.value = null
    }

    function nextMonth() {
      currentMonth.value++
      if (currentMonth.value > 11) {
        currentMonth.value = 0
        currentYear.value++
      }
      selectedDay.value = null
    }

    return {
      currentYear,
      currentMonth,
      selectedDay,
      meals,
      getMealsForDay,
      addMeal,
      deleteMeal,
      prevMonth,
      nextMonth
    }
  }
}
</script>

<style scoped>
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}

.app-header {
  text-align: center;
  color: white;
  padding: 40px 20px 20px;
  background: rgba(0, 0, 0, 0.1);
}

.app-header h1 {
  font-size: 2.5rem;
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
}

.subtitle {
  font-size: 1.1rem;
  opacity: 0.9;
}

.app-main {
  flex: 1;
  padding: 20px;
  max-width: 1200px;
  margin: 0 auto;
  width: 100%;
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 20px;
}

.app-footer {
  text-align: center;
  color: white;
  padding: 20px;
  background: rgba(0, 0, 0, 0.1);
  font-size: 0.9rem;
}

/* Responsive */
@media (max-width: 1024px) {
  .app-main {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .app-header h1 {
    font-size: 2rem;
  }

  .subtitle {
    font-size: 1rem;
  }

  .app-main {
    padding: 10px;
    gap: 10px;
  }
}
</style>