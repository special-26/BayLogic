<template>
  <div class="min-h-screen bg-gradient-to-br from-blue-50 to-indigo-100 p-8">
    <div class="max-w-6xl mx-auto">
      <!-- Success Screen -->
      <div v-if="submitted" class="max-w-2xl mx-auto">
        <div class="bg-white rounded-2xl shadow-xl p-8">
          <div class="text-center mb-8">
            <div class="inline-flex items-center justify-center w-20 h-20 bg-green-100 rounded-full mb-4">
              <IconCheckCircle2 class="w-12 h-12 text-green-600" />
            </div>
            <h1 class="text-3xl font-bold text-gray-800 mb-2">Job Created Successfully</h1>
            <p class="text-gray-600">Job card has been saved and assigned to service bays</p>
          </div>

          <div class="bg-gray-50 rounded-lg p-6 mb-6">
            <h2 class="text-lg font-semibold text-gray-800 mb-4">Job Details</h2>
            <div class="grid grid-cols-2 gap-4 text-sm">
              <div>
                <p class="text-gray-500">Car Number</p>
                <p class="font-semibold text-gray-800">{{ formData.carNumber }}</p>
              </div>
              <div>
                <p class="text-gray-500">Customer Name</p>
                <p class="font-semibold text-gray-800">{{ formData.customerName }}</p>
              </div>
              <div>
                <p class="text-gray-500">Phone Number</p>
                <p class="font-semibold text-gray-800">{{ formData.phoneNumber }}</p>
              </div>
              <div>
                <p class="text-gray-500">Priority</p>
                <span :class="['inline-block px-3 py-1 rounded-full text-xs font-semibold', 
                  formData.priority === 'urgent' ? 'bg-red-100 text-red-700' : 'bg-blue-100 text-blue-700']">
                  {{ formData.priority === 'urgent' ? 'URGENT' : 'NORMAL' }}
                </span>
              </div>
            </div>
          </div>

          <div class="mb-6">
            <h2 class="text-lg font-semibold text-gray-800 mb-4">Assigned Service Bays</h2>
            <div class="space-y-3">
              <div v-for="(bay, service) in assignedBays" :key="service" 
                class="flex items-center justify-between bg-indigo-50 rounded-lg p-4">
                <div class="flex items-center gap-3">
                  <IconWrench class="w-5 h-5 text-indigo-600" />
                  <span class="font-medium text-gray-800 capitalize">{{ service }}</span>
                </div>
                <span class="bg-indigo-600 text-white px-4 py-1 rounded-full text-sm font-semibold">
                  Bay {{ bay }}
                </span>
              </div>
            </div>
          </div>

          <div class="bg-green-50 border border-green-200 rounded-lg p-4 mb-6">
            <p class="text-green-800 text-sm">
              ✓ Bay jobs have been created and assigned to workers
            </p>
          </div>

          <div class="flex gap-3">
            <button @click="handleNewJob" 
              class="flex-1 bg-indigo-600 text-white py-3 rounded-lg font-semibold hover:bg-indigo-700 transition-colors">
              Create New Job Card
            </button>
            <button @click="submitted = false; activeTab = 'view'" 
              class="flex-1 bg-gray-200 text-gray-800 py-3 rounded-lg font-semibold hover:bg-gray-300 transition-colors">
              View All Jobs
            </button>
          </div>
        </div>
      </div>

      <!-- Main Content -->
      <div v-else>
        <!-- Tab Navigation -->
        <div class="bg-white rounded-t-2xl shadow-lg">
          <div class="flex border-b">
            <button @click="activeTab = 'create'" 
              :class="['flex-1 py-4 px-6 font-semibold transition-colors', 
                activeTab === 'create' ? 'text-indigo-600 border-b-2 border-indigo-600' : 'text-gray-500 hover:text-gray-700']">
              Create Job Card
            </button>
            <button @click="activeTab = 'view'" 
              :class="['flex-1 py-4 px-6 font-semibold transition-colors',
                activeTab === 'view' ? 'text-indigo-600 border-b-2 border-indigo-600' : 'text-gray-500 hover:text-gray-700']">
              View All Jobs ({{ jobCards.length }})
            </button>
          </div>
        </div>

        <!-- Create Tab -->
        <div v-if="activeTab === 'create'" class="bg-white rounded-b-2xl shadow-xl p-8">
          <div class="max-w-2xl mx-auto">
            <div class="mb-8">
              <h1 class="text-3xl font-bold text-gray-800 mb-2">Service Advisor</h1>
              <p class="text-gray-600">Create a new job card for vehicle service</p>
            </div>

            <form @submit.prevent="handleSubmit">
              <!-- Car Number -->
              <div class="mb-6">
                <label class="block text-sm font-semibold text-gray-700 mb-2">
                  <div class="flex items-center gap-2">
                    <IconCar class="w-4 h-4" />
                    Car Number
                  </div>
                </label>
                <input v-model="formData.carNumber" type="text" placeholder="e.g., KA-01-AB-1234" required
                  class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent outline-none transition" />
              </div>

              <!-- Customer Name -->
              <div class="mb-6">
                <label class="block text-sm font-semibold text-gray-700 mb-2">
                  <div class="flex items-center gap-2">
                    <IconUser class="w-4 h-4" />
                    Customer Name
                  </div>
                </label>
                <input v-model="formData.customerName" type="text" placeholder="Enter customer name" required
                  class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent outline-none transition" />
              </div>

              <!-- Phone Number -->
              <div class="mb-6">
                <label class="block text-sm font-semibold text-gray-700 mb-2">
                  <div class="flex items-center gap-2">
                    <IconPhone class="w-4 h-4" />
                    Phone Number
                  </div>
                </label>
                <input v-model="formData.phoneNumber" type="tel" placeholder="Enter phone number" required
                  class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-transparent outline-none transition" />
              </div>

              <!-- Services -->
              <div class="mb-6">
                <label class="block text-sm font-semibold text-gray-700 mb-3">
                  Services Required
                </label>
                <div class="grid grid-cols-2 gap-3">
                  <label v-for="service in ['denting', 'painting', 'tyre', 'washing']" :key="service"
                    :class="['flex items-center gap-3 p-4 border-2 rounded-lg cursor-pointer transition',
                      formData.services[service] ? 'border-indigo-500 bg-indigo-50' : 'border-gray-200 hover:border-gray-300']">
                    <input type="checkbox" v-model="formData.services[service]"
                      class="w-5 h-5 text-indigo-600 rounded focus:ring-2 focus:ring-indigo-500" />
                    <span class="font-medium text-gray-800 capitalize">{{ service }}</span>
                  </label>
                </div>
              </div>

              <!-- Priority -->
              <div class="mb-8">
                <label class="block text-sm font-semibold text-gray-700 mb-3">
                  Priority Level
                </label>
                <div class="flex gap-4">
                  <label :class="['flex-1 flex items-center justify-center gap-2 p-4 border-2 rounded-lg cursor-pointer transition',
                    formData.priority === 'normal' ? 'border-blue-500 bg-blue-50' : 'border-gray-200 hover:border-gray-300']">
                    <input type="radio" v-model="formData.priority" value="normal" class="w-5 h-5 text-blue-600" />
                    <span class="font-medium text-gray-800">Normal</span>
                  </label>
                  <label :class="['flex-1 flex items-center justify-center gap-2 p-4 border-2 rounded-lg cursor-pointer transition',
                    formData.priority === 'urgent' ? 'border-red-500 bg-red-50' : 'border-gray-200 hover:border-gray-300']">
                    <input type="radio" v-model="formData.priority" value="urgent" class="w-5 h-5 text-red-600" />
                    <span class="font-medium text-gray-800">Urgent</span>
                  </label>
                </div>
              </div>

              <button type="submit" :disabled="loading"
                class="w-full bg-indigo-600 text-white py-4 rounded-lg font-semibold text-lg hover:bg-indigo-700 transition-colors shadow-lg hover:shadow-xl disabled:opacity-50 disabled:cursor-not-allowed">
                {{ loading ? 'Creating Job & Assigning Bays...' : 'Create Job Card' }}
              </button>
            </form>
          </div>
        </div>

        <!-- View Tab -->
        <div v-else class="bg-white rounded-b-2xl shadow-xl p-8">
          <div class="flex items-center justify-between mb-6">
            <h2 class="text-2xl font-bold text-gray-800">All Job Cards</h2>
            <button @click="fetchJobCards"
              class="flex items-center gap-2 px-4 py-2 bg-gray-100 hover:bg-gray-200 rounded-lg transition-colors">
              <IconRefreshCw class="w-4 h-4" />
              Refresh
            </button>
          </div>

          <div v-if="jobCards.length === 0" class="text-center py-12 text-gray-500">
            <IconCar class="w-16 h-16 mx-auto mb-4 opacity-50" />
            <p>No job cards created yet</p>
          </div>

          <div v-else class="space-y-4">
            <div v-for="job in jobCards" :key="job.id" 
              class="border border-gray-200 rounded-lg p-6 hover:shadow-md transition-shadow">
              <div class="flex items-start justify-between mb-4">
                <div>
                  <h3 class="text-xl font-bold text-gray-800">{{ job.car_number }}</h3>
                  <p class="text-gray-600">{{ job.customer_name }}</p>
                  <p class="text-sm text-gray-500">{{ job.phone_number }}</p>
                </div>
                <div class="text-right">
                  <span :class="['inline-block px-3 py-1 rounded-full text-xs font-semibold mb-2',
                    job.priority === 'urgent' ? 'bg-red-100 text-red-700' : 'bg-blue-100 text-blue-700']">
                    {{ job.priority === 'urgent' ? 'URGENT' : 'NORMAL' }}
                  </span>
                  <div class="flex items-center gap-1 text-xs text-gray-500">
                    <IconClock class="w-3 h-3" />
                    {{ formatDate(job.created_at) }}
                  </div>
                </div>
              </div>

              <div class="mb-4">
                <p class="text-sm font-semibold text-gray-700 mb-2">Services & Assigned Bays:</p>
                <div class="grid grid-cols-2 gap-2">
                  <div v-for="(bay, service) in job.assigned_bays" :key="service"
                    class="flex items-center justify-between bg-gray-50 rounded px-3 py-2 text-sm">
                    <span class="capitalize text-gray-700">{{ service }}</span>
                    <span class="font-semibold text-indigo-600">Bay {{ bay }}</span>
                  </div>
                </div>
              </div>

              <div class="flex gap-2">
                <select v-model="job.status" @change="updateJobStatus(job.id, job.status)"
                  class="px-3 py-1 border border-gray-300 rounded text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                  <option value="pending">Pending</option>
                  <option value="in_progress">In Progress</option>
                  <option value="completed">Completed</option>
                </select>
                <span :class="['px-3 py-1 rounded text-sm font-medium',
                  job.status === 'completed' ? 'bg-green-100 text-green-700' :
                  job.status === 'in_progress' ? 'bg-yellow-100 text-yellow-700' :
                  'bg-gray-100 text-gray-700']">
                  {{ job.status.replace('_', ' ').toUpperCase() }}
                </span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'

// Icons (will use Icon prefix for auto-import)
const IconCheckCircle2 = resolveComponent('IconCheckCircle2')
const IconCar = resolveComponent('IconCar')
const IconUser = resolveComponent('IconUser')
const IconPhone = resolveComponent('IconPhone')
const IconWrench = resolveComponent('IconWrench')
const IconClock = resolveComponent('IconClock')
const IconRefreshCw = resolveComponent('IconRefreshCw')

const config = useRuntimeConfig()
const supabase = useSupabaseClient()

const formData = ref({
  carNumber: '',
  customerName: '',
  phoneNumber: '',
  services: {
    denting: false,
    painting: false,
    tyre: false,
    washing: false
  },
  priority: 'normal'
})

const submitted = ref(false)
const assignedBays = ref({})
const jobCards = ref([])
const loading = ref(false)
const activeTab = ref('create')

const fetchJobCards = async () => {
  try {
    const { data, error } = await supabase
      .from('job_cards')
      .select('*')
      .order('created_at', { ascending: false })

    if (error) throw error
    jobCards.value = data || []
  } catch (error) {
    console.error('Error fetching job cards:', error)
  }
}

const assignBays = (services) => {
  const bays = {}
  const bayOptions = {
    denting: ['D-1', 'D-2', 'D-3', 'D-4'],
    painting: ['P-5', 'P-6', 'P-7', 'P-8'],
    tyre: ['T-1', 'T-2', 'T-3'],
    washing: ['W-1', 'W-2', 'W-3', 'W-4']
  }

  Object.keys(services).forEach(service => {
    if (services[service]) {
      const options = bayOptions[service]
      bays[service] = options[Math.floor(Math.random() * options.length)]
    }
  })

  return bays
}

const handleSubmit = async () => {
  loading.value = true

  try {
    const bays = assignBays(formData.value.services)

    const jobCardData = {
      car_number: formData.value.carNumber,
      customer_name: formData.value.customerName,
      phone_number: formData.value.phoneNumber,
      services: formData.value.services,
      priority: formData.value.priority,
      assigned_bays: bays,
      status: 'pending'
    }

    const { data: jobCard, error: jobCardError } = await supabase
      .from('job_cards')
      .insert([jobCardData])
      .select()

    if (jobCardError) throw jobCardError

    const bayJobs = []
    const serviceDescriptions = {
      denting: 'Dent removal and body work',
      painting: 'Paint service and finishing',
      tyre: 'Tyre service and alignment',
      washing: 'Car washing and detailing'
    }

    const bayNames = {
      denting: 'DENTING BAY',
      painting: 'PAINT BAY',
      tyre: 'TYRE BAY',
      washing: 'WASHING BAY'
    }

    for (const [service, bayCode] of Object.entries(bays)) {
      const { data: existingJobs } = await supabase
        .from('bay_jobs')
        .select('queue_position')
        .eq('bay_code', bayCode)
        .order('queue_position', { ascending: false })
        .limit(1)

      const nextPosition = existingJobs && existingJobs.length > 0 
        ? existingJobs[0].queue_position + 1 
        : 1

      bayJobs.push({
        bay_code: bayCode,
        bay_name: bayNames[service],
        car_number: formData.value.carNumber,
        customer_name: formData.value.customerName,
        phone_number: formData.value.phoneNumber,
        task_description: serviceDescriptions[service],
        priority: formData.value.priority,
        status: nextPosition === 1 ? 'in_progress' : 'waiting',
        queue_position: nextPosition
      })
    }

    if (bayJobs.length > 0) {
      const { error: bayJobsError } = await supabase
        .from('bay_jobs')
        .insert(bayJobs)

      if (bayJobsError) throw bayJobsError
    }

    assignedBays.value = bays
    submitted.value = true
    loading.value = false
  } catch (error) {
    console.error('Error creating job card:', error)
    alert('Error creating job card. Please check console for details.')
    loading.value = false
  }
}

const handleNewJob = () => {
  formData.value = {
    carNumber: '',
    customerName: '',
    phoneNumber: '',
    services: {
      denting: false,
      painting: false,
      tyre: false,
      washing: false
    },
    priority: 'normal'
  }
  submitted.value = false
  assignedBays.value = {}
  activeTab.value = 'create'
}

const updateJobStatus = async (id, newStatus) => {
  try {
    const { error } = await supabase
      .from('job_cards')
      .update({ status: newStatus })
      .eq('id', id)

    if (error) throw error
  } catch (error) {
    console.error('Error updating status:', error)
  }
}

const formatDate = (dateString) => {
  const date = new Date(dateString)
  return date.toLocaleString('en-IN', {
    day: '2-digit',
    month: 'short',
    year: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

onMounted(() => {
  fetchJobCards()

  // Realtime subscription
  const channel = supabase
    .channel('job_cards_changes')
    .on('postgres_changes', 
      { event: '*', schema: 'public', table: 'job_cards' },
      (payload) => {
        console.log('Change received!', payload)
        fetchJobCards()
      }
    )
    .subscribe()

  onUnmounted(() => {
    supabase.removeChannel(channel)
  })
})
</script>
