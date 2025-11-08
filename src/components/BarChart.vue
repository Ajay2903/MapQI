<script setup lang="ts">
import { Bar } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale,
} from 'chart.js'
// 💡 Type-only imports to satisfy 'verbatimModuleSyntax'
import type { ChartOptions, ChartData } from 'chart.js'
import { computed } from 'vue'

// Register the necessary components from Chart.js (Value Imports)
ChartJS.register(Title, Tooltip, Legend, BarElement, CategoryScale, LinearScale)

interface PollutantItem {
  name: string
  value: number
  color: string
}

const props = defineProps<{
  data: PollutantItem[]
}>()

// --- Chart Data Configuration ---
const chartData = computed<ChartData<'bar'>>(() => ({
  // XAxis dataKey="name"
  labels: props.data.map((item) => item.name),
  datasets: [
    {
      label: 'Concentration (µg/m³)',
      // Apply different colors per bar using payload.color logic from Recharts
      backgroundColor: props.data.map((item) => item.color),
      // Bar dataKey="value"
      data: props.data.map((item) => item.value),

      // Corresponds to :radius="[8, 8, 0, 0]"
      borderRadius: 8,
    },
  ],
}))

// --- Chart Options Configuration (Styling) ---
const chartOptions: ChartOptions<'bar'> = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false, // Hide the legend
    },
    tooltip: {
      // Fix 1: Use cornerRadius instead of borderRadius
      backgroundColor: 'rgba(0,0,0,0.8)',
      borderColor: 'transparent',
      cornerRadius: 8,
      bodyColor: '#fff',
      titleColor: '#fff',
    },
  },
  scales: {
    y: {
      beginAtZero: true,
      // YAxis stroke="rgba(255,255,255,0.7)"
      ticks: { color: 'rgba(255, 255, 255, 0.7)' },
      // CartesianGrid stroke="rgba(255,255,255,0.1)" / strokeDasharray="3 3"
      grid: {
        color: 'rgba(255, 255, 255, 0.1)',
        borderDash: [3, 3], // Fix 2: Use borderDash inside grid
      } as any,
    },
    x: {
      // XAxis stroke="rgba(255,255,255,0.7)"
      ticks: { color: 'rgba(255, 255, 255, 0.7)' },
      grid: {
        color: 'rgba(255, 255, 255, 0.1)',
      },
    },
  },
}
</script>

<template>
  <Bar :data="chartData" :options="chartOptions" :width="500" :height="300" />
</template>
