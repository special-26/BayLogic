<template>
  <div :class="['rounded-2xl p-6 hover:shadow-lg transition-shadow relative overflow-hidden border-2',
    config.bg, config.border]">
    <!-- Status Badge -->
    <div class="absolute top-0 right-0">
      <div :class="['text-white px-4 py-2 rounded-bl-2xl flex items-center gap-2 font-semibold', config.badge]">
        <component :is="StatusIcon" class="w-4 h-4" />
        {{ config.text }}
      </div>
    </div>

    <!-- Bay Header -->
    <div class="mb-4">
      <div class="flex items-center gap-3 mb-2">
        <span class="text-4xl">{{ bay.icon }}</span>
        <div>
          <h3 class="text-xl font-bold text-gray-800">{{ bay.name }}</h3>
          <p class="text-sm text-gray-600">{{ bay.code }}</p>
        </div>
      </div>
    </div>

    <!-- Job Details -->
    <div v-if="bayStatus.status !== 'idle'" class="space-y-3">
      <div class="bg-white rounded-lg p-4">
        <p class="text-xs text-gray-500 mb-1">Car Number</p>
        <p class="text-2xl font-bold text-gray-900">{{ bayStatus.carNumber }}</p>
      </div>

      <div class="bg-white rounded-lg p-3">
        <p class="text-xs text-gray-500 mb-1">Customer</p>
        <p class="text-sm font-semibold text-gray-800">{{ bayStatus.customerName }}</p>
      </div>

      <div class="bg-white rounded-lg p-3">
        <p class="text-xs text-gray-500 mb-1">Task</p>
        <p class="text-sm text-gray-700">{{ bayStatus.task }}</p>
      </div>

      <div class="flex items-center justify-between bg-white rounded-lg p-3">
        <div>
          <p class="text-xs text-gray-500 mb-1">Time Running</p>
          <p class="text-lg font-bold text-gray-900 flex items-center gap-2">
            <IconClock class="w-4 h-4 text-gray-600" />
            {{ timeRunning }}
          </p>
        </div>
        <span v-if="bayStatus.priority === 'urgent'" 
          class="bg-red-100 text-red-700 px-3 py-1 rounded-full text-xs font-bold">
          🔥 URGENT
        </span>
      </div>
    </div>

    <!-- Idle State -->
    <div v-else class="text-center py-8">
      <IconCar class="w-16 h-16 mx-auto mb-3 text-gray-400" />
      <p class="text-gray-500 font-medium">No active job</p>
      <p class="text-xs text-gray-400 mt-1">Bay is available</p>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

// Icons
const IconCheckCircle = resolveComponent('IconCheckCircle')
const IconAlertTriangle = resolveComponent('IconAlertTriangle')
const IconPauseCircle = resolveComponent('IconPauseCircle')
const IconClock = resolveComponent('IconClock')
const IconCar = resolveComponent('IconCar')

const props = defineProps({
  bay: {
    type: Object,
    required: true
  },
  bayStatus: {
    type: Object,
    required: true
  }
})

const statusConfig = {
  active: {
    bg: 'bg-green-50',
    border: 'border-green-400',
    badge: 'bg-green-500',
    icon: 'IconCheckCircle',
    text: 'Active'
  },
  delayed: {
    bg: 'bg-red-50',
    border: 'border-red-400',
    badge: 'bg-red-500',
    icon: 'IconAlertTriangle',
    text: 'Delayed'
  },
  idle: {
    bg: 'bg-gray-50',
    border: 'border-gray-300',
    badge: 'bg-gray-400',
    icon: 'IconPauseCircle',
    text: 'Idle'
  }
}

const config = computed(() => statusConfig[props.bayStatus.status])

const StatusIcon = computed(() => {
  const iconName = config.value.icon
  return resolveComponent(iconName)
})

const timeRunning = computed(() => {
  if (!props.bayStatus.startTime) return null
  
  const start = new Date(props.bayStatus.startTime)
  const now = new Date()
  const diff = now - start
  
  const hours = Math.floor(diff / (1000 * 60 * 60))
  const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60))
  
  return `${hours}h ${minutes}m`
})
</script>