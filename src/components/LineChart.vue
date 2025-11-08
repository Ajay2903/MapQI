<script setup lang="ts">
import { Line } from 'vue-chartjs'
import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  LineElement,
  LinearScale,
  CategoryScale,
  PointElement,
} from 'chart.js'
import type { ChartOptions, ChartData } from 'chart.js'
import { computed } from 'vue'

// Register the necessary components from Chart.js
ChartJS.register(Title, Tooltip, Legend, LineElement, LinearScale, CategoryScale, PointElement)

interface DataItem {
  time: string
  temp: number
}

const props = defineProps<{
  data: DataItem[]
}>()

// --- Chart Data Configuration ---
const chartData = computed<ChartData<'line'>>(() => ({
  // X-Axis Labels (dataKey="time")
  labels: props.data.map((item) => item.time),
  datasets: [
    {
      label: 'Temperature (°C)',
      // Corresponds to stroke="#60a5fa" (Tailwind blue-400)
      backgroundColor: '#60a5fa',
      borderColor: '#60a5fa',
      // dataKey="temp"
      data: props.data.map((item) => item.temp),
      tension: 0.4, // type="monotone"
      pointBackgroundColor: '#3b82f6', // dot fill
      pointBorderColor: 'white',
      pointRadius: 5, // dot r
      borderWidth: 3, // strokeWidth
    },
  ],
}))

// --- Chart Options Configuration (Styling) ---
const chartOptions: ChartOptions<'line'> = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false, // Often hidden for simple line charts
    },
    tooltip: {
      // Mimic your tooltipStyle
      backgroundColor: 'rgba(0,0,0,0.8)',
      borderColor: 'transparent',
      cornerRadius: 8,
      bodyColor: '#fff',
      titleColor: '#fff',
    },
  },
  scales: {
    y: {
      // YAxis stroke="rgba(255,255,255,0.7)"
      ticks: { color: 'rgba(255, 255, 255, 0.7)' },
      // CartesianGrid stroke="rgba(255,255,255,0.1)" / strokeDasharray="3 3"
      grid: {
        color: 'rgba(255, 255, 255, 0.1)',
        borderDash: [3, 3],
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
  <Line :data="chartData" :options="chartOptions" :width="500" :height="300" />
</template>
