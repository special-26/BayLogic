<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-50 to-blue-50">
    <!-- Header -->
    <div class="bg-gradient-to-r from-indigo-600 to-blue-600 text-white p-6 shadow-lg">
      <div class="max-w-7xl mx-auto">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-4">
            <div class="bg-white/20 p-4 rounded-2xl backdrop-blur-sm">
              <IconBarChart3 class="w-10 h-10" />
            </div>
            <div>
              <h1 class="text-4xl font-bold mb-1">Supervisor Dashboard</h1>
              <p class="text-blue-100">Real-time workshop monitoring</p>
            </div>
          </div>
          <div class="text-right">
            <p class="text-sm text-blue-100 mb-1">Last Updated</p>
            <p class="text-lg font-semibold">{{ lastUpdate }}</p>
          </div>
        </div>
      </div>
    </div>

    <div class="max-w-7xl mx-auto p-6">
      <!-- Stats Cards -->
      <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
        <div class="bg-white rounded-xl p-6 shadow-md border-l-4 border-indigo-500">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-sm text-gray-600 mb-1">Total Bays</p>
              <p class="text-3xl font-bold text-gray-900">{{ stats.total }}</p>
            </div>
            <div class="bg-indigo-100 p-4 rounded-full">
              <IconWrench class="w-8 h-8 text-indigo-600" />
            </div>
          </div>
        </div>

        <div class="bg-white rounded-xl p-6 shadow-md border-l-4 border-green-500">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-sm text-gray-600 mb-1">Active</p>
              <p class="text-3xl font-bold text-green-600">{{ stats.active }}</p>
            </div>
            <div class="bg-green-100 p-4 rounded-full">
              <IconActivity class="w-8 h-8 text-green-600" />
            </div>
          </div>
        </div>

        <div class="bg-white rounded-xl p-6 shadow-md border-l-4 border-gray-500">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-sm text-gray-600 mb-1">Idle</p>
              <p class="text-3xl font-bold text-gray-600">{{ stats.idle }}</p>
            </div>
            <div class="bg-gray-100 p-4 rounded-full">
              <IconPauseCircle class="w-8 h-8 text-gray-600" />
            </div>
          </div>
        </div>

        <div class="bg-white rounded-xl p-6 shadow-md border-l-4 border-red-500">
          <div class="flex items-center justify-between">
            <div>
              <p class="text-sm text-gray-600 mb-1">Delayed</p>
              <p class="text-3xl font-bold text-red-600">{{ stats.delayed }}</p>
            </div>
            <div class="bg-red-100 p-4 rounded-full">
              <IconAlertTriangle class="w-8 h-8 text-red-600" />
            </div>
          </div>
        </div>
      </div>

      <!-- Filters -->
      <div class="bg-white rounded-xl p-6 shadow-md mb-6">
        <div class="flex items-center justify-between">
          <h2 class="text-xl font-bold text-gray-800">Bay Status</h2>
          <div class="flex gap-3">
            <button @click="filter = 'all'"
              :class="['px-6 py-2 rounded-lg font-semibold transition-all',
                filter === 'all' ? 'bg-indigo-600 text-white shadow-lg' : 'bg-gray-100 text-gray-700 hover:bg-gray-200']">
              All Bays ({{ bayDefinitions.length }})
            </button>
            <button @click="filter = 'active'"
              :class="['px-6 py-2 rounded-lg font-semibold transition-all',
                filter === 'active' ? 'bg-green-600 text-white shadow-lg' : 'bg-gray-100 text-gray-700 hover:bg-gray-200']">
              Active ({{ stats.active }})
            </button>
            <button @click="filter = 'delayed'"
              :class="['px-6 py-2 rounded-lg font-semibold transition-all',
                filter === 'delayed' ? 'bg-red-600 text-white shadow-lg' : 'bg-gray-100 text-gray-700 hover:bg-gray-200']">
              Delayed ({{ stats.delayed }})
            </button>
            <button @click="filter = 'idle'"
              :class="['px-6 py-2 rounded-lg font-semibold transition-all',
                filter === 'idle' ? 'bg-gray-600 text-white shadow-lg' : 'bg-gray-100 text-gray-700 hover:bg-gray-200']">
              Idle ({{ stats.idle }})
            </button>
          </div>
        </div>
      </div>

      <!-- Bay Grid -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-6">
        <BayCard v-for="bay in filteredBays" :key="bay.code" :bay="bay" :bayStatus="getBayStatus(bay.code)" />
      </div>

      <div v-if="filteredBays.length === 0" class="bg-white rounded-xl p-12 text-center shadow-md">
        <IconPauseCircle class="w-16 h-16 mx-auto mb-4 text-gray-400" />
        <p class="text-xl text-gray-500 font-medium">No bays match this filter</p>
        <p class="text-gray-400 mt-2">Try selecting a different filter option</p>
      </div>
    </div>

    <!-- Footer Info -->
    <div class="max-w-7xl mx-auto px-6 pb-6">
      <div class="bg-gradient-to-r from-blue-500 to-indigo-500 rounded-xl p-6 text-white">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-3">
            <IconTrendingUp class="w-8 h-8" />
            <div>
              <p class="text-sm opacity-90">Workshop Efficiency</p>
              <p class="text-2xl font-bold">
                {{ stats.total > 0 ? Math.round((stats.active / stats.total) * 100) : 0 }}%
              </p>
            </div>
          </div>
          <div class="flex items-center gap-3">
            <IconUsers class="w-8 h-8" />
            <div>
              <p class="text-sm opacity-90">Bays in Operation</p>
              <p class="text-2xl font-bold">{{ stats.active }} / {{ stats.total }}</p>
            </div>
          </div>
          <button @click="fetchBayData"
            class="bg-white text-indigo-600 px-6 py-3 rounded-lg font-semibold hover:bg-blue-50 transition-colors flex items-center gap-2">
            <IconActivity class="w-5 h-5" />
            Refresh Data
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

// Icons
const IconBarChart3 = resolveComponent('IconBarChart3')
const IconWrench = resolveComponent('IconWrench')
const IconActivity = resolveComponent('IconActivity')
const IconPauseCircle = resolveComponent('IconPauseCircle')
const IconAlertTriangle = resolveComponent('IconAlertTriangle')
const IconTrendingUp = resolveComponent('IconTrendingUp')
const IconUsers = resolveComponent('IconUsers')

const supabase = useSupabaseClient()

const bayJobs = ref([])
const filter = ref('all')
const stats = ref({
  total: 0,
  active: 0,
  idle: 0,
  delayed: 0
})
const lastUpdate = ref('')

const bayDefinitions = [
  { code: 'P-5', name: 'Paint Bay 1', icon: '🎨', color: 'purple' },
  { code: 'P-6', name: 'Paint Bay 2', icon: '🎨', color: 'purple' },
  { code: 'P-7', name: 'Paint Bay 3', icon: '🎨', color: 'purple' },
  { code: 'P-8', name: 'Paint Bay 4', icon: '🎨', color: 'purple' },
  { code: 'D-1', name: 'Denting Bay 1', icon: '🔨', color: 'orange' },
  { code: 'D-2', name: 'Denting Bay 2', icon: '🔨', color: 'orange' },
  { code: 'D-3', name: 'Denting Bay 3', icon: '🔨', color: 'orange' },
  { code: 'D-4', name: 'Denting Bay 4', icon: '🔨', color: 'orange' },
  { code: 'T-1', name: 'Tyre Bay 1', icon: '⚙️', color: 'blue' },
  { code: 'T-2', name: 'Tyre Bay 2', icon: '⚙️', color: 'blue' },
  { code: 'T-3', name: 'Tyre Bay 3', icon: '⚙️', color: 'blue' },
  { code: 'W-1', name: 'Washing Bay 1', icon: '💧', color: 'cyan' },
  { code: 'W-2', name: 'Washing Bay 2', icon: '💧', color: 'cyan' },
  { code: 'W-3', name: 'Washing Bay 3', icon: '💧', color: 'cyan' },
  { code: 'W-4', name: 'Washing Bay 4', icon: '💧', color: 'cyan' },
]

const fetchBayData = async () => {
  try {
    const { data, error } = await supabase
      .from('bay_jobs')
      .select('*')
      .eq('queue_position', 1)
      .eq('status', 'in_progress')

    if (error) throw error

    bayJobs.value = data || []
    calculateStats(data || [])
    updateLastUpdateTime()
  } catch (error) {
    console.error('Error fetching bay data:', error)
  }
}

const calculateStats = (jobs) => {
  const activeBays = jobs.length
  const totalBays = bayDefinitions.length
  const idleBays = totalBays - activeBays
  
  const delayedBays = jobs.filter(job => {
    const startTime = new Date(job.created_at)
    const elapsed = (new Date() - startTime) / (1000 * 60 * 60)
    return elapsed > 2
  }).length

  stats.value = {
    total: totalBays,
    active: activeBays,
    idle: idleBays,
    delayed: delayedBays
  }
}

const getBayStatus = (bayCode) => {
  const job = bayJobs.value.find(j => j.bay_code === bayCode)
  
  if (!job) {
    return {
      status: 'idle',
      carNumber: null,
      customerName: null,
      task: null,
      startTime: null,
      priority: null
    }
  }

  const startTime = new Date(job.created_at)
  const elapsed = (new Date() - startTime) / (1000 * 60 * 60)
  const isDelayed = elapsed > 2

  return {
    status: isDelayed ? 'delayed' : 'active',
    carNumber: job.car_number,
    customerName: job.customer_name,
    task: job.task_description,
    startTime: job.created_at,
    priority: job.priority
  }
}

const filteredBays = computed(() => {
  return bayDefinitions.filter(bay => {
    const status = getBayStatus(bay.code)
    
    if (filter.value === 'all') return true
    if (filter.value === 'delayed') return status.status === 'delayed'
    if (filter.value === 'idle') return status.status === 'idle'
    if (filter.value === 'active') return status.status === 'active'
    
    return true
  })
})

const updateLastUpdateTime = () => {
  const now = new Date()
  lastUpdate.value = now.toLocaleTimeString('en-US', { 
    hour: '2-digit', 
    minute: '2-digit',
    second: '2-digit'
  })
}

onMounted(() => {
  fetchBayData()

  const channel = supabase
    .channel('supervisor_bay_changes')
    .on('postgres_changes', 
      { event: '*', schema: 'public', table: 'bay_jobs' },
      (payload) => {
        console.log('Bay update received!', payload)
        fetchBayData()
      }
    )
    .subscribe()

  onUnmounted(() => {
    supabase.removeChannel(channel)
  })
})
</script>
