<template>
  <div>
    <!-- Navigation Bar -->
    <div class="bg-gradient-to-r from-indigo-600 to-purple-600 p-5 flex gap-5 justify-center items-center shadow-lg">
      <button @click="currentView = 'service'"
        :class="['px-6 py-3 rounded-lg border-none font-bold cursor-pointer flex items-center gap-2 text-base transition-all',
          currentView === 'service' ? 'bg-white text-indigo-600' : 'bg-white/20 text-white hover:bg-white/30']">
        <Icon name="flowbite:users-outline" size="25" />
        Service Advisor
      </button>
      <button @click="currentView = 'bay'"
      :class="['px-6 py-3 rounded-lg border-none font-bold cursor-pointer flex items-center gap-2 text-base transition-all',
          currentView === 'bay' ? 'bg-white text-indigo-600' : 'bg-white/20 text-white hover:bg-white/30']">
        <Icon name="pepicons-pencil:monitor" size="25" />
        Bay Worker
      </button>
      <button @click="currentView = 'supervisor'"
        :class="['px-6 py-3 rounded-lg border-none font-bold cursor-pointer flex items-center gap-2 text-base transition-all',
          currentView === 'supervisor' ? 'bg-white text-indigo-600' : 'bg-white/20 text-white hover:bg-white/30']">
        <Icon name="carbon:dashboard" size="25" />
        Supervisor
      </button>
    </div>

    <!-- Content -->
    <ServiceAdvisor v-if="currentView === 'service'" />
    <BayWorker v-if="currentView === 'bay'" />
    <SupervisorDashboard v-if="currentView === 'supervisor'" />
  </div>
</template>

<script setup>
import { ref } from 'vue'

const currentView = ref('service')
</script>
