<template>
  <div class="calendar-container card">
    <div class="calendar-header">
      <button @click="$emit('prev-month')" class="btn-nav">← Anterior</button>
      <h2 class="month-year">{{ monthName }} {{ year }}</h2>
      <button @click="$emit('next-month')" class="btn-nav">Siguiente →</button>
    </div>

    <div class="calendar">
      <!-- Encabezados de días de la semana -->
      <div class="weekday" v-for="day in weekdays" :key="day">
        {{ day }}
      </div>

      <!-- Espacios vacíos al inicio del mes -->
      <div v-for="_ in firstDayOfMonth" :key="'empty-' + _" class="empty"></div>

      <!-- Días del mes -->
      <div 
        v-for="day in daysInMonth" 
        :key="day"
        :class="['day', { 'today': isToday(day), 'has-meals': hasMeals(day) }]"
        @click="$emit('select-day', day)"
      >
        <div class="day-number">{{ day }}</div>
        <div class="meal-count" v-if="hasMeals(day)">
          {{ getMealCount(day) }} 🍽️
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { computed } from 'vue'

export default {
  name: 'Calendar',
  props: {
    year: Number,
    month: Number,
    meals: Object
  },
  emits: ['prev-month', 'next-month', 'select-day'],
  setup(props) {
    const weekdays = ['Lun', 'Mar', 'Mié', 'Jue', 'Vie', 'Sab', 'Dom']
    const monthNames = [
      'Enero', 'Febrero', 'Marzo', 'Abril', 'Mayo', 'Junio',
      'Julio', 'Agosto', 'Septiembre', 'Octubre', 'Noviembre', 'Diciembre'
    ]

    const monthName = computed(() => monthNames[props.month])

    const daysInMonth = computed(() => {
      return new Date(props.year, props.month + 1, 0).getDate()
    })

    const firstDayOfMonth = computed(() => {
      const first = new Date(props.year, props.month, 1)
      // getDay() devuelve 0 para domingo, necesitamos 0 para lunes
      // Entonces: domingo=6, lunes=0, martes=1, etc.
      let day = first.getDay() - 1
      if (day < 0) day = 6
      return day
    })

    function isToday(day) {
      const today = new Date()
      return day === today.getDate() &&
        props.month === today.getMonth() &&
        props.year === today.getFullYear()
    }

    function getMealKey(day) {
      return `${props.year}-${props.month}-${day}`
    }

    function hasMeals(day) {
      const key = getMealKey(day)
      return props.meals[key] && props.meals[key].length > 0
    }

    function getMealCount(day) {
      const key = getMealKey(day)
      return props.meals[key] ? props.meals[key].length : 0
    }

    return {
      weekdays,
      monthName,
      daysInMonth,
      firstDayOfMonth,
      isToday,
      hasMeals,
      getMealCount
    }
  }
}
</script>

<style scoped>
.calendar-container {
  background: white;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  gap: 10px;
}

.month-year {
  font-size: 1.5rem;
  color: #333;
  text-align: center;
  flex: 1;
  min-width: 150px;
}

.btn-nav {
  background: #667eea;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 6px;
  cursor: pointer;
  transition: background 0.3s ease;
  font-weight: 500;
}

.btn-nav:hover {
  background: #764ba2;
}

.calendar {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 8px;
}

.weekday {
  text-align: center;
  font-weight: 600;
  color: #667eea;
  padding: 10px;
  font-size: 0.9rem;
}

.day {
  aspect-ratio: 1;
  background: #f5f5f5;
  border: 2px solid transparent;
  border-radius: 8px;
  padding: 8px;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  min-height: 80px;
}

.day:hover {
  background: #e8e8ff;
  border-color: #667eea;
  transform: translateY(-2px);
}

.day.today {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  font-weight: bold;
  border-color: #667eea;
}

.day.has-meals {
  border-color: #4caf50;
  background: #f1f8f4;
}

.day.today.has-meals {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  border-color: #4caf50;
}

.day-number {
  font-size: 1.2rem;
  font-weight: 600;
  margin-bottom: 4px;
}

.meal-count {
  font-size: 0.8rem;
  color: #4caf50;
  font-weight: 500;
  text-align: center;
}

.day.today .meal-count {
  color: #fff;
}

.empty {
  min-height: 80px;
}

/* Responsive */
@media (max-width: 768px) {
  .calendar-container {
    padding: 15px;
  }

  .calendar-header {
    flex-wrap: wrap;
  }

  .btn-nav {
    padding: 6px 12px;
    font-size: 0.85rem;
  }

  .month-year {
    font-size: 1.2rem;
    width: 100%;
  }

  .day {
    min-height: 60px;
    padding: 4px;
  }

  .day-number {
    font-size: 1rem;
  }

  .meal-count {
    font-size: 0.7rem;
  }
}
</style>