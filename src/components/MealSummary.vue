<template>
  <div v-if="isOpen" class="modal-overlay" @click="close">
    <div class="modal-content" @click.stop>
      <div class="modal-header">
        <h2>📊 Resumen de Comidas</h2>
        <button class="close-btn" @click="close">✕</button>
      </div>

      <div class="modal-body">
        <!-- Pestañas -->
        <div class="tabs">
          <button 
            :class="['tab', { active: activeTab === 'monthly' }]"
            @click="activeTab = 'monthly'"
          >
            📅 Este Mes
          </button>
          <button 
            :class="['tab', { active: activeTab === 'weekly' }]"
            @click="activeTab = 'weekly'"
          >
            📆 Esta Semana
          </button>
          <button 
            :class="['tab', { active: activeTab === 'categories' }]"
            @click="activeTab = 'categories'"
          >
            🏷️ Por Categoría
          </button>
        </div>

        <!-- Resumen Mensual -->
        <div v-if="activeTab === 'monthly'" class="tab-content">
          <div class="stats-grid">
            <div class="stat-card">
              <div class="stat-value">{{ totalMealsMonth }}</div>
              <div class="stat-label">Total de comidas</div>
            </div>
            <div class="stat-card">
              <div class="stat-value">{{ daysWithMealsMonth }}</div>
              <div class="stat-label">Días planificados</div>
            </div>
            <div class="stat-card">
              <div class="stat-value">{{ averageMealsPerDay }}</div>
              <div class="stat-label">Promedio por día</div>
            </div>
          </div>

          <div class="meals-list">
            <h3>Comidas este mes:</h3>
            <div v-if="mealsListMonth.length > 0" class="meal-items">
              <div v-for="(meal, idx) in mealsListMonth" :key="idx" class="meal-item">
                <span class="meal-text">{{ meal.text }}</span>
                <span class="meal-category">{{ meal.category }}</span>
              </div>
            </div>
            <div v-else class="empty-message">
              No hay comidas planificadas este mes
            </div>
          </div>
        </div>

        <!-- Resumen Semanal -->
        <div v-if="activeTab === 'weekly'" class="tab-content">
          <div class="stats-grid">
            <div class="stat-card">
              <div class="stat-value">{{ totalMealsWeek }}</div>
              <div class="stat-label">Total de comidas</div>
            </div>
            <div class="stat-card">
              <div class="stat-value">{{ daysWithMealsWeek }}</div>
              <div class="stat-label">Días planificados</div>
            </div>
          </div>

          <div class="week-breakdown">
            <h3>Por día de la semana:</h3>
            <div v-for="day in weekDays" :key="day.date" class="day-row">
              <div class="day-name">{{ day.name }} ({{ day.date }})</div>
              <div class="day-meals">
                {{ day.count }} 🍽️
              </div>
            </div>
          </div>

          <div class="meals-list">
            <h3>Comidas esta semana:</h3>
            <div v-if="mealsListWeek.length > 0" class="meal-items">
              <div v-for="(meal, idx) in mealsListWeek" :key="idx" class="meal-item">
                <span class="meal-text">{{ meal.text }}</span>
                <span class="meal-category">{{ meal.category }}</span>
              </div>
            </div>
            <div v-else class="empty-message">
              No hay comidas planificadas esta semana
            </div>
          </div>
        </div>

        <!-- Resumen por Categoría -->
        <div v-if="activeTab === 'categories'" class="tab-content">
          <div class="categories-list">
            <h3>Comidas por categoría:</h3>
            <div v-if="categorySummary.length > 0" class="category-items">
              <div v-for="cat in categorySummary" :key="cat.name" class="category-item">
                <div class="category-header">
                  <span class="category-name">{{ cat.name }}</span>
                  <span class="category-count">{{ cat.count }} comidas</span>
                </div>
                <div class="category-bar">
                  <div class="category-fill" :style="{ width: cat.percentage + '%' }"></div>
                </div>
              </div>
            </div>
            <div v-else class="empty-message">
              No hay comidas categorizadas
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'MealSummary',
  props: {
    meals: Object,
    year: Number,
    month: Number
  },
  setup(props) {
    const isOpen = ref(false)
    const activeTab = ref('monthly')

    const monthlyMeals = computed(() => {
      const result = []
      for (const key in props.meals) {
        const [y, m] = key.split('-')
        if (parseInt(y) === props.year && parseInt(m) === props.month) {
          result.push(...props.meals[key])
        }
      }
      return result
    })

    const weeklyMeals = computed(() => {
      const today = new Date()
      const result = []
      
      // Conseguir el inicio de la semana (lunes)
      const firstDay = new Date(today)
      firstDay.setDate(today.getDate() - today.getDay() + 1)
      
      for (const key in props.meals) {
        const [y, m, d] = key.split('-')
        const date = new Date(parseInt(y), parseInt(m), parseInt(d))
        
        if (date >= firstDay && date <= today) {
          result.push(...props.meals[key])
        }
      }
      return result
    })

    const totalMealsMonth = computed(() => monthlyMeals.value.length)
    
    const daysWithMealsMonth = computed(() => {
      const days = new Set()
      for (const key in props.meals) {
        const [y, m] = key.split('-')
        if (parseInt(y) === props.year && parseInt(m) === props.month && props.meals[key].length > 0) {
          days.add(key)
        }
      }
      return days.size
    })

    const averageMealsPerDay = computed(() => {
      if (daysWithMealsMonth.value === 0) return '0'
      return (totalMealsMonth.value / daysWithMealsMonth.value).toFixed(1)
    })

    const mealsListMonth = computed(() => monthlyMeals.value)

    const totalMealsWeek = computed(() => weeklyMeals.value.length)

    const daysWithMealsWeek = computed(() => {
      const days = new Set()
      const today = new Date()
      const firstDay = new Date(today)
      firstDay.setDate(today.getDate() - today.getDay() + 1)
      
      for (const key in props.meals) {
        const [y, m, d] = key.split('-')
        const date = new Date(parseInt(y), parseInt(m), parseInt(d))
        if (date >= firstDay && date <= today && props.meals[key].length > 0) {
          days.add(key)
        }
      }
      return days.size
    })

    const mealsListWeek = computed(() => weeklyMeals.value)

    const weekDays = computed(() => {
      const today = new Date()
      const firstDay = new Date(today)
      firstDay.setDate(today.getDate() - today.getDay() + 1)
      
      const days = []
      const dayNames = ['Lun', 'Mar', 'Mié', 'Jue', 'Vie', 'Sab', 'Dom']
      
      for (let i = 0; i < 7; i++) {
        const d = new Date(firstDay)
        d.setDate(firstDay.getDate() + i)
        
        const key = `${d.getFullYear()}-${d.getMonth()}-${d.getDate()}`
        const count = props.meals[key] ? props.meals[key].length : 0
        
        days.push({
          name: dayNames[i],
          date: d.getDate(),
          count: count
        })
      }
      return days
    })

    const categorySummary = computed(() => {
      const categories = {}
      const allMeals = []
      
      for (const key in props.meals) {
        allMeals.push(...props.meals[key])
      }
      
      allMeals.forEach(meal => {
        const cat = meal.category || 'Sin categoría'
        categories[cat] = (categories[cat] || 0) + 1
      })
      
      const total = allMeals.length || 1
      return Object.entries(categories)
        .map(([name, count]) => ({
          name,
          count,
          percentage: (count / total * 100).toFixed(1)
        }))
        .sort((a, b) => b.count - a.count)
    })

    function open() {
      isOpen.value = true
    }

    function close() {
      isOpen.value = false
    }

    return {
      isOpen,
      activeTab,
      totalMealsMonth,
      daysWithMealsMonth,
      averageMealsPerDay,
      mealsListMonth,
      totalMealsWeek,
      daysWithMealsWeek,
      mealsListWeek,
      weekDays,
      categorySummary,
      open,
      close
    }
  }
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  border-radius: 12px;
  width: 90%;
  max-width: 600px;
  max-height: 80vh;
  overflow-y: auto;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
  border-bottom: 2px solid #f0f0f0;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  border-radius: 12px 12px 0 0;
}

.modal-header h2 {
  margin: 0;
  font-size: 1.5rem;
}

.close-btn {
  background: rgba(255, 255, 255, 0.2);
  border: none;
  color: white;
  font-size: 1.5rem;
  cursor: pointer;
  padding: 0;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  transition: background 0.3s ease;
}

.close-btn:hover {
  background: rgba(255, 255, 255, 0.3);
}

.modal-body {
  padding: 20px;
}

.tabs {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
  border-bottom: 2px solid #f0f0f0;
}

.tab {
  background: transparent;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
  font-size: 0.95rem;
  font-weight: 500;
  color: #999;
  transition: all 0.3s ease;
  border-bottom: 3px solid transparent;
  margin-bottom: -2px;
}

.tab:hover {
  color: #667eea;
}

.tab.active {
  color: #667eea;
  border-bottom-color: #667eea;
}

.tab-content {
  animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 15px;
  margin-bottom: 30px;
}

.stat-card {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
}

.stat-value {
  font-size: 2rem;
  font-weight: bold;
  margin-bottom: 8px;
}

.stat-label {
  font-size: 0.85rem;
  opacity: 0.9;
}

.meals-list {
  margin-top: 20px;
}

.meals-list h3,
.week-breakdown h3,
.categories-list h3 {
  margin-top: 0;
  color: #333;
  font-size: 1.1rem;
}

.meal-items,
.category-items {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.meal-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background: #f9f9f9;
  border-radius: 6px;
  border-left: 4px solid #667eea;
}

.meal-text {
  flex: 1;
  font-weight: 500;
  color: #333;
}

.meal-category {
  background: #667eea;
  color: white;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 500;
}

.empty-message {
  text-align: center;
  color: #999;
  padding: 20px;
  background: #f9f9f9;
  border-radius: 6px;
}

.week-breakdown {
  margin: 20px 0;
}

.day-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  background: #f9f9f9;
  border-radius: 6px;
  margin-bottom: 8px;
}

.day-name {
  font-weight: 500;
  color: #333;
}

.day-meals {
  background: #667eea;
  color: white;
  padding: 4px 12px;
  border-radius: 20px;
  font-weight: 500;
}

.category-item {
  margin-bottom: 20px;
}

.category-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.category-name {
  font-weight: 600;
  color: #333;
}

.category-count {
  color: #999;
  font-size: 0.9rem;
}

.category-bar {
  height: 8px;
  background: #f0f0f0;
  border-radius: 4px;
  overflow: hidden;
}

.category-fill {
  height: 100%;
  background: linear-gradient(90deg, #667eea 0%, #764ba2 100%);
  transition: width 0.3s ease;
}

/* Responsive */
@media (max-width: 768px) {
  .modal-content {
    width: 95%;
    max-height: 90vh;
  }

  .modal-header {
    padding: 15px;
  }

  .modal-header h2 {
    font-size: 1.2rem;
  }

  .modal-body {
    padding: 15px;
  }

  .stats-grid {
    grid-template-columns: 1fr;
  }

  .tabs {
    flex-wrap: wrap;
  }

  .tab {
    padding: 8px 12px;
    font-size: 0.85rem;
  }
}
</style>