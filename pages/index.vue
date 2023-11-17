<template>
  <!-- Loader while fetching content -->
  <div v-if="showLoadingScreen" class="loading-screen">
    <div class="pre-loading-animation">
      <!-- Loading animation waves -->
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
      <div class="wave"></div>
    </div>
  </div>

  <div v-else-if="shouldRenderView" class="dashboard-container">
    <!-- Dashboard Header -->
    <header class="dashboard-header">
      <h1 class="dashboard-title">User Management Dashboard</h1>
    </header>

    <div class="wrapper">
      <!-- Dashboard Navigation -->
      <div class="dashboard-navigation">
        <!-- Buttons for each tab -->
        <button
          v-for="tab in tabs"
          :key="tab.name"
          class="dashboard-button"
          :class="{ active: activeTab === tab.name }"
          :tab-direction="tab.direction"
          @click="changeTab(tab.name)"
        >
          {{ tab.label }}
        </button>
      </div>
    </div>

    <!-- Display content based on active tab -->
    <div class="sliding-tabs">
      <div
        v-for="tab in tabs"
        v-show="activeTab === tab.name"
        :key="tab.name"
        class="dashboard-content"
      >
        <!-- Render component based on active tab -->
        <component :is="tab.component" v-if="shouldRenderView" />
      </div>
    </div>
  </div>
</template>

<script>
import { defineAsyncComponent, ref, onBeforeMount } from 'vue'

const UserDetails = defineAsyncComponent(() =>
  import('@/components/UserDetails')
)
const AccountCreation = defineAsyncComponent(() =>
  import('@/components/AccountCreation')
)

export default {
  setup() {
    // State variables
    const shouldRenderView = ref(false)
    const showLoadingScreen = ref(true)
    const activeTab = ref('user-details') // Set the first tab as active initially

    // Tabs data containing tab information
    const tabs = [
      {
        name: 'user-details',
        label: 'User Details',
        component: UserDetails,
        direction: 'left',
        slideDirection: 'right',
      },
      {
        name: 'account-creation',
        label: 'Account Creation',
        component: AccountCreation,
        direction: 'right',
        slideDirection: 'left',
      },
    ]

    // Execute actions before mounting
    onBeforeMount(() => {
      document.title = 'User Management Dashboard'
      setTimeout(() => {
        showLoadingScreen.value = false // Hide the loading screen after 3 seconds
      }, 3000)
      // Simulate async data fetching delay
      setTimeout(() => {
        shouldRenderView.value = true // Enable rendering of components after delay
      }, 3000)
    })

    // Function to switch tabs
    const changeTab = (tab) => {
      if (activeTab.value === tab) return
      const direction = tabs.find((t) => t.name === tab).direction
      const slideDirection =
        tabs.find((t) => t.name === tab).slideDirection || direction
      activeTab.value = tab
      const dashboardNavigation = document.querySelector(
        '.dashboard-navigation'
      )
      dashboardNavigation.classList.remove('left', 'right')
      dashboardNavigation.classList.add(direction)
      const slidingTabs = document.querySelector('.sliding-tabs')
      slidingTabs.classList.remove('slide-left', 'slide-right')
      slidingTabs.classList.add(`slide-${slideDirection}`)
      document.body.style.overflow = 'hidden'
      setTimeout(() => {
        document.body.style.overflow = ''
      }, 1000)
    }

    // Simulate async data fetching delay
    setTimeout(() => {
      shouldRenderView.value = true // Enable rendering of components after delay
    }, 2000)

    return {
      tabs,
      shouldRenderView,
      showLoadingScreen,
      activeTab,
      changeTab,
    }
  },
}
</script>

<style>
/* Styles for loading screen */
.loading-screen {
  height: 100vh;
  width: 100%;
  position: fixed;
  top: 0;
  left: 0;
  background-color: #000;
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Styles for the loading animation container */
.pre-loading-animation {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: #000;
  flex-direction: row;
}

/* Styles for the individual wave animation */
.wave {
  width: 5px;
  height: 100px;
  background: linear-gradient(45deg, cyan, #fff);
  margin: 10px;
  animation: wave 1s linear infinite;
  border-radius: 20px;
}

/* Animations for the wave */
.wave:nth-child(2) {
  animation-delay: 0.1s;
}
.wave:nth-child(3) {
  animation-delay: 0.2s;
}
.wave:nth-child(4) {
  animation-delay: 0.3s;
}
.wave:nth-child(5) {
  animation-delay: 0.4s;
}
.wave:nth-child(6) {
  animation-delay: 0.5s;
}
.wave:nth-child(7) {
  animation-delay: 0.6s;
}
.wave:nth-child(8) {
  animation-delay: 0.7s;
}
.wave:nth-child(9) {
  animation-delay: 0.8s;
}
.wave:nth-child(10) {
  animation-delay: 0.9s;
}

/* Keyframe animation for the wave */
@keyframes wave {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1);
  }
  100% {
    transform: scale(0);
  }
}

/* Dashboard title styles */
.dashboard-title {
  font-size: 3rem;
  font-weight: bold;
  transition: font-size 0.3s ease;
}

/* Styles for the dashboard container */
.dashboard-container {
  background-image: url('~static/bg-image.jpg');
  background-size: cover;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding-top: 20px;
}

.dashboard-header {
  color: white;
  padding: 20px;
  text-align: center;
}

/* Styles for the dashboard navigation */
.wrapper {
  border-radius: 37px;
  background-color: #e9e8e8;
  padding: 8px;
  width: 100%;
  max-width: 500px;
  margin-left: auto;
  margin-right: auto;
}

.dashboard-navigation {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  margin: 0 auto; /* Center the navigation */
  position: relative;
}

.dashboard-navigation:after {
  content: '';
  position: absolute;
  width: 50%;
  top: 0;
  transition: left cubic-bezier(0.88, -0.35, 0.565, 1.35) 0.4s;
  border-radius: 27.5px;
  box-shadow: 0 2px 15px 0 rgba(0, 0, 0, 0.1);
  background-color: #3d90ef;
  height: 100%;
  z-index: 0;
  left: 0;
}

.dashboard-navigation.left:after {
  left: 0;
}

.dashboard-navigation.right:after {
  left: 50%;
}

.dashboard-navigation .dashboard-button {
  display: inline-block;
  width: 50%;
  padding: 8px 0;
  z-index: 1;
  position: relative;
  cursor: pointer;
  transition: color 200ms;
  font-size: 16px;
  font-weight: bold;
  line-height: normal;
  user-select: none;
  color: #3d90ef;
}

.dashboard-navigation .dashboard-button.active {
  color: #ffffff;
}

.dashboard-content {
  width: 80%;
  margin: 20px auto;
}

/* Styles for sliding tabs */
.sliding-tabs {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
  width: 80%; /* Adjust the width of the sliding tabs */
  margin: 0 auto; /* Center the sliding tabs */
}

/* Styles for the selected tab */
.sliding-tab {
  cursor: pointer;
  padding: 10px 15px;
  border-radius: 5px;
  transition: background-color 0.3s ease;
  flex: 1; /* Make tabs equally distribute space */
  text-align: center; /* Center text within tabs */
}

.sliding-tab.selected {
  background-color: #3d90ef;
  color: white;
}

/* Define animations for sliding tabs */
.slide-left {
  animation: slideLeft 1s ease forwards;
}

.slide-right {
  animation: slideRight 1s ease forwards;
}

@keyframes slideLeft {
  from {
    transform: translateX(100%);
  }
  to {
    transform: translateX(0);
  }
}

@keyframes slideRight {
  from {
    transform: translateX(-100%);
  }
  to {
    transform: translateX(0);
  }
}

/* Responsive styles for tablets */
@media screen and (max-width: 768px) {
  .dashboard-title {
    font-size: 2rem;
  }

  .wrapper {
    width: 75%;
  }

  .dashboard-content {
    width: 90%;
  }
}
</style>
