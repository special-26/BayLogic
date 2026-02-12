<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-900 via-slate-800 to-slate-900 p-8">
    <div class="max-w-7xl mx-auto">
      <!-- Bay Header -->
      <div class="bg-gradient-to-r from-indigo-600 to-purple-600 rounded-3xl p-8 mb-8 shadow-2xl">
        <div class="flex items-center justify-between">
          <div class="flex items-center gap-6">
            <div class="bg-white/20 p-6 rounded-2xl backdrop-blur-sm">
              <IconWrench class="w-16 h-16 text-white" />
            </div>
            <div>
              <div class="flex items-center jsutify-center gap-2">
                <h1 class="text-6xl font-bold text-white mb-2">{{ bayName }}</h1>
                <!-- Bay Toggle -->
                <div class="w-20 relative">
                  <button class="border-2 shadow rounded-xl w-10 h-10 ml-6 flex items-center justify-center hover:bg-white/50 cursor-pointer" @click="changeBay">
                    <Icon name="famicons:shuffle-outline" class="w-7 h-7 text-white" />
                  </button>
                  <div v-show="baysToggle" class="rounded-2xl bg-white shadow-xl absolute top-12 w-40">
                    <ul class="p-2 text-sm text-body font-medium w-full">
                      <li v-for="bay in baysTypes">
                        <a 
                          href="#" 
                          @click="changeBayType(bay)"
                          class="inline-flex items-center w-full p-2 hover:bg-gray-200 hover:text-heading rounded-xl border-b text-gray-800">{{ bay.bName }}</a>
                      </li>
                    </ul>
                  </div>
                </div>
                <!-- Bay Toggle Ends -->
              </div>
              <p class="text-3xl text-white/80 font-semibold">{{ bayCode }}</p>
            </div>
          </div>
          <div class="text-right">
            <div class="text-2xl text-white/70 mb-2">Current Time</div>
            <div class="text-4xl font-bold text-white">
              {{ currentTime }}
            </div>
          </div>
        </div>
      </div>

      <!-- Current Job Section -->
      <div v-if="currentJob" class="bg-white rounded-3xl p-10 mb-8 shadow-2xl border-4 border-green-400">
        <div class="flex items-center justify-between mb-8">
          <div class="flex items-center gap-4">
            <div :class="['px-8 py-3 rounded-full text-2xl font-bold flex items-center gap-3',
              currentJob.status === 'in_progress' ? 'bg-green-500 text-white animate-pulse' : 'bg-yellow-500 text-white']">
              <IconCheckCircle v-if="currentJob.status === 'in_progress'" class="w-8 h-8" />
              <IconClock v-else class="w-8 h-8" />
              {{ currentJob.status === 'in_progress' ? 'IN PROGRESS' : 'WAITING' }}
            </div>
          </div>
          <div class="text-right">
            <p class="text-2xl text-gray-500 mb-1">Priority</p>
            <span :class="['text-3xl font-bold', currentJob.priority === 'urgent' ? 'text-red-600' : 'text-blue-600']">
              {{ currentJob.priority === 'urgent' ? '🔥 URGENT' : '✓ NORMAL' }}
            </span>
          </div>
        </div>

        <div class="grid grid-cols-2 gap-8 mb-8">
          <div class="bg-slate-50 rounded-2xl p-8">
            <p class="text-2xl text-gray-500 mb-3">Car Number</p>
            <p class="text-7xl font-bold text-slate-900">{{ currentJob.car_number }}</p>
          </div>
          <div class="bg-slate-50 rounded-2xl p-8">
            <p class="text-2xl text-gray-500 mb-3">Customer</p>
            <p class="text-5xl font-bold text-slate-900 mb-2">{{ currentJob.customer_name }}</p>
            <p class="text-3xl text-gray-600">{{ currentJob.phone_number }}</p>
          </div>
        </div>

        <div class="bg-indigo-50 rounded-2xl p-8 mb-8">
          <p class="text-2xl text-gray-700 mb-3 font-semibold">Task Description</p>
          <p class="text-4xl text-slate-900 font-medium">{{ currentJob.task_description }}</p>
        </div>

        <button @click="markTaskCompleted" :disabled="loading"
          class="w-full bg-gradient-to-r from-green-500 to-green-600 text-white py-8 rounded-2xl font-bold text-4xl hover:from-green-600 hover:to-green-700 transition-all shadow-xl hover:shadow-2xl disabled:opacity-50 disabled:cursor-not-allowed flex items-center justify-center gap-4">
          <IconCheckCircle class="w-12 h-12" />
          {{ loading ? 'PROCESSING...' : 'MARK TASK COMPLETED' }}
        </button>
      </div>

      <!-- No Active Jobs -->
      <div v-else class="bg-white rounded-3xl p-16 mb-8 shadow-2xl text-center">
        <div class="bg-slate-100 w-32 h-32 rounded-full flex items-center justify-center mx-auto mb-6">
          <IconCar class="w-16 h-16 text-slate-400" />
        </div>
        <h2 class="text-5xl font-bold text-slate-400 mb-4">No Active Jobs</h2>
        <p class="text-3xl text-slate-400">Waiting for next assignment...</p>
      </div>

      <!-- Queue Section -->
      <div class="bg-slate-800 rounded-3xl p-10 shadow-2xl">
        <div class="flex items-center gap-4 mb-8">
          <IconAlertCircle class="w-12 h-12 text-yellow-400" />
          <h2 class="text-5xl font-bold text-white">NEXT IN QUEUE</h2>
        </div>

        <div v-if="queue.length > 0" class="space-y-6">
          <div v-for="(job, index) in queue" :key="job.id"
            class="bg-slate-700 rounded-2xl p-8 flex items-center justify-between border-2 border-slate-600 hover:border-slate-500 transition-colors">
            <div class="flex items-center gap-8">
              <div class="bg-slate-600 text-white w-20 h-20 rounded-2xl flex items-center justify-center text-4xl font-bold">
                {{ index + 1 }}
              </div>
              <div>
                <p class="text-5xl font-bold text-white mb-2">{{ job.car_number }}</p>
                <p class="text-3xl text-slate-300">{{ job.customer_name }}</p>
              </div>
            </div>
            <div class="text-right">
              <p class="text-2xl text-slate-400 mb-2">Task</p>
              <p class="text-3xl font-semibold text-white">{{ job.task_description }}</p>
              <span :class="['inline-block mt-3 px-6 py-2 rounded-full text-xl font-bold',
                job.priority === 'urgent' ? 'bg-red-500 text-white' : 'bg-blue-500 text-white']">
                {{ job.priority.toUpperCase() }}
              </span>
            </div>
            <IconArrowRight class="w-12 h-12 text-slate-500" />
          </div>
        </div>

        <div v-else class="text-center py-12">
          <p class="text-4xl text-slate-400">Queue is empty</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// Icons
const IconWrench = resolveComponent('IconWrench')
const IconCheckCircle = resolveComponent('IconCheckCircle')
const IconClock = resolveComponent('IconClock')
const IconCar = resolveComponent('IconCar')
const IconAlertCircle = resolveComponent('IconAlertCircle')
const IconArrowRight = resolveComponent('IconArrowRight')

const supabase = useSupabaseClient()

const bayName = ref('PAINT BAY')
const bayCode = ref('P-7')
const currentJob = ref(null)
const queue = ref([])
const loading = ref(false)
const currentTime = ref('')

const baysToggle = ref(false)
const changeBay = () => {
  baysToggle.value = !baysToggle.value;
}
const baysTypes = [
  {bName: 'PAINT BAY', bCode: 'P-7'},
  {bName: 'DENTING BAY', bCode: 'D-3'},
  {bName: 'TYRE BAY', bCode: 'T-2'},
  {bName: 'WASHING BAY', bCode: 'W-1'}
]
const changeBayType = (bay) => {
  bayName.value = bay.bName;
  bayCode.value = bay.bCode;
  baysToggle.value = !baysToggle.value;
  fetchBayJobs()
}

let timeInterval = null

const updateTime = () => {
  const now = new Date()
  currentTime.value = now.toLocaleTimeString('en-US', { 
    hour: '2-digit', 
    minute: '2-digit' 
  })
}

const fetchBayJobs = async () => {
  try {
    const { data, error } = await supabase
      .from('bay_jobs')
      .select('*')
      .eq('bay_code', bayCode.value)
      .order('queue_position', { ascending: true })

    if (error) throw error

    if (data && data.length > 0) {
      currentJob.value = data[0]
      queue.value = data.slice(1, 3)
    } else {
      currentJob.value = null
      queue.value = []
    }
  } catch (error) {
    console.error('Error fetching bay jobs:', error)
  }
}

const markTaskCompleted = async () => {
  if (!currentJob.value) return

  loading.value = true
  try {
    const { error: deleteError } = await supabase
      .from('bay_jobs')
      .delete()
      .eq('id', currentJob.value.id)

    if (deleteError) throw deleteError

    // Update queue positions using RPC or raw SQL
    const remainingJobs = await supabase
      .from('bay_jobs')
      .select('*')
      .eq('bay_code', bayCode.value)
      .gt('queue_position', currentJob.value.queue_position)

    if (remainingJobs.data) {
      for (const job of remainingJobs.data) {
        await supabase
          .from('bay_jobs')
          .update({ queue_position: job.queue_position - 1 })
          .eq('id', job.id)
      }
    }

    await fetchBayJobs()
    loading.value = false
  } catch (error) {
    console.error('Error completing task:', error)
    loading.value = false
  }
}

onMounted(() => {
  updateTime()
  timeInterval = setInterval(updateTime, 1000)
  
  fetchBayJobs()

  const channel = supabase
    .channel('bay_jobs_changes')
    .on('postgres_changes', 
      { event: '*', schema: 'public', table: 'bay_jobs' },
      (payload) => {
        console.log('Change received!', payload)
        fetchBayJobs()
      }
    )
    .subscribe()

  onUnmounted(() => {
    if (timeInterval) {
      clearInterval(timeInterval)
    }
    supabase.removeChannel(channel)
  })
})
</script>

<style scoped>
@keyframes pulse {
  0%, 100% {
    opacity: 1;
  }
  50% {
    opacity: 0.8;
  }
}

.animate-pulse {
  animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}
</style>
