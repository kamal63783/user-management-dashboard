<template>
  <!-- User details container -->
  <div class="user-details-container">
    <!-- Header -->
    <header class="user-details-header">
      <h1 class="user-details-title">User Details</h1>
    </header>

    <!-- Filters and Search bar -->
    <div class="user-details-filters">
      <!-- Search input -->
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Search..."
        class="search-bar"
        @input="filterUsers"
        @focus="onFocusSearch"
        @blur="onBlurSearch"
      />
    </div>

    <!-- Display filtered users -->
    <div class="user-details-table">
      <table class="user-table">
        <!-- Table headers -->
        <thead>
          <tr>
            <th>Username</th>
            <th>Email</th>
            <th>Phone</th>
            <th>ID</th>
            <th>Creation Date</th>
          </tr>
        </thead>
        <tbody>
          <!-- Render user data -->
          <tr
            v-for="user in filteredUsers"
            :key="user.id"
            @click="openReportModal(user)"
          >
            <td>{{ user.username }}</td>
            <td>{{ user.email }}</td>
            <td>{{ user.phone }}</td>
            <td>{{ user.id }}</td>
            <td>{{ user.creationDate }}</td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- Report Modal -->
    <div
      v-if="showModal"
      class="report-modal"
      :class="{ 'modal-open': showModal }"
      @click="closeModalOutside"
      @keydown.esc="closeModal"
    >
      <div class="modal-content" @click.stop>
        <!-- Close button -->
        <button class="close-btn" @click="closeModal">&times;</button>
        <!-- Modal information -->
        <div class="modal-info">
          <h2 class="modal-title">User Details</h2>
          <!-- Display selected user details -->
          <div v-if="selectedUser">
            <p>Username: {{ selectedUser.username }}</p>
            <p>Email: {{ selectedUser.email }}</p>
            <p>Phone: {{ selectedUser.phone }}</p>
            <p>ID: {{ selectedUser.id }}</p>
            <p>Creation Date: {{ selectedUser.creationDate }}</p>
          </div>
        </div>
        <!-- Generate report button -->
        <button class="generate-button" @click="generateReport(selectedUser)">
          Generate Report
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
// eslint-disable-next-line import/no-named-as-default
import jsPDF from 'jspdf'
import 'jspdf-autotable'

// Reactive variables
const users = ref([])
const searchQuery = ref('')
const filteredUsers = ref([])
const showModal = ref(false)
const selectedUser = ref(null)

// Fetch user data from an external source
async function fetchUsers() {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users')
    users.value = await response.json()
    // Generate random creation dates for users
    users.value.forEach((user) => {
      const startDate = new Date('1990-01-01')
      const endDate = new Date()
      user.creationDate = randomDate(startDate, endDate).toLocaleDateString()
    })

    filteredUsers.value = users.value
  } catch (error) {
    // Handle error if fetch fails
  }
}

// Filter users based on search query
function filterUsers() {
  const query = searchQuery.value.toLowerCase()
  filteredUsers.value = users.value.filter(
    (user) =>
      (user.username && user.username.toLowerCase().includes(query)) ||
      (user.email && user.email.toLowerCase().includes(query)) ||
      (user.phone && user.phone.toLowerCase().includes(query)) ||
      (user.id && user.id.toString().toLowerCase().includes(query)) ||
      (user.creationDate && user.creationDate.toLowerCase().includes(query))
  )
}

// Function to open modal with user details
function openReportModal(user) {
  if (searchQuery.value.trim() !== '') {
    selectedUser.value = user
    showModal.value = true
  }
}

// Function to close modal when clicking outside of it
function closeModalOutside(event) {
  if (!event.target.closest('.modal-content')) {
    closeModal()
  }
}

// Function to handle clicks within the modal
function closeModal() {
  selectedUser.value = null
  const modal = document.querySelector('.report-modal')
  if (modal) {
    modal.classList.add('fadeOutModal') // Apply fade-out animation
    setTimeout(() => {
      showModal.value = false
      document.body.style.overflow = ''
      modal.classList.remove('fadeOutModal') // Remove fade-out class after animation
    }, 1000) // Adjust the timeout to match the animation duration
  }
}

// Generate a PDF report for the selected user
function generateReport(user) {
  // eslint-disable-next-line new-cap
  const doc = new jsPDF()
  let yOffset = 15
  doc.setFontSize(18)
  doc.text('User Report', 105, yOffset, { align: 'center' })
  yOffset += 10
  const tableData = [
    [user.username, user.email, user.phone, user.id, user.creationDate],
  ]
  const headers = ['Username', 'Email', 'Phone', 'ID', 'Creation Date']
  doc.autoTable({
    head: [headers],
    body: tableData,
    startY: 20,
  })
  doc.save('user_report.pdf') // Save PDF with user report
}

// Handle search bar focus
function onFocusSearch() {
  document.body.classList.add('search-bar-focused')
}

// Handle search bar blur
function onBlurSearch() {
  document.body.classList.remove('search-bar-focused')
}

// Fetch users on component mount
onMounted(() => {
  fetchUsers()
})

// Function to generate a random date
function randomDate(start, end) {
  return new Date(
    start.getTime() + Math.random() * (end.getTime() - start.getTime())
  )
}
</script>

<style>
/* Style for user details container */
.user-details-container {
  background-image: url('~static/bg-image.jpg');
  background-size: cover;
  background-position: center;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  padding-top: 20px;
  color: #333;
  overflow: hidden;
  position: relative;
}

/* Header styling */
.user-details-header {
  padding: 20px;
  text-align: center;
}

/* Title styling */
.user-details-title {
  font-size: 2.5rem;
  font-weight: bold;
  color: white;
}

/* Styles for the search bar */
.search-bar {
  width: 200px;
  -webkit-transition: width 0.4s ease-in-out;
  transition: width 0.4s ease-in-out;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-bottom: 20px;
  margin-right: 500px; /* Might not be necessary */
  font-size: 14px;
}

/* Focus styles for search bar */
.search-bar:focus {
  width: 100%;
}

/* Styles for user details table */
.user-details-table {
  width: 80%;
  margin: 20px auto;
  border-radius: 8px;
  overflow-x: auto;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

/* Table styles */
.user-table {
  border-collapse: collapse;
  width: 100%;
  background-color: rgba(255, 255, 255, 0.8);
}

/* Table cell styles */
.user-table th {
  padding: 12px 8px;
  text-align: left;
  border-bottom: 1px solid #ddd;
  color: #fff;
  font-weight: bold;
}

.user-table td {
  padding: 12px 8px;
  text-align: left;
  border-bottom: 1px solid #ddd;
  font-weight: 400;
  color: #000;
}

/* Header cell styles */
.user-table th {
  background-color: #428bca;
}

/* Hover effect for table rows */
.user-table tbody tr:hover {
  background-color: #fff;
}

/* Modal title styling */
.modal-title {
  text-align: center;
}

/* Modal content styling */
.modal-content {
  position: relative;
  background-color: #fff;
  padding: 30px;
  border-radius: 8px;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  border: 2px solid #007bff; /* Change the color to your preference */
  border-radius: 8px;
  z-index: 1;
}

.modal-info {
  margin-bottom: 20px;
}

/* Close button styles */
.close-btn {
  position: absolute;
  top: 5px;
  right: 15px;
  background: none;
  border: none;
  font-size: 30px;
  cursor: pointer;
  color: red;
  width: 40px;
  height: 40px;
  border-radius: 10px;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.close-btn:hover {
  background-color: #ccc;
}

/* Styles for report modal */
.report-modal {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
  animation: fadeInModal 1s ease forwards;
}

@keyframes fadeInModal {
  from {
    opacity: 0;
    transform: scale(0.8);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

.fadeOutModal {
  animation: fadeOutModal 1s ease forwards;
}

@keyframes fadeOutModal {
  from {
    opacity: 1;
    transform: scale(1);
  }
  to {
    opacity: 0;
    transform: scale(0);
  }
}

/* Generate report button styles */
.generate-button {
  padding: 5px 8px;
  border-radius: 5px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
  font-size: 1.1rem;
  transition: background-color 0.3s ease;
}

/* Hover styles for generate report button */
.generate-button:hover {
  background-color: #0056b3;
}

/* Media Query for Small Screens (e.g., Mobiles) */
@media screen and (max-width: 1238px) {
  /* Styles for the search bar on smaller screens */
  .user-details-title {
    font-size: 1.75rem;
  }
  .search-bar {
    width: 80%;
    margin: 0 auto;
    display: block;
    -webkit-transition: none;
    transition: none;
  }

  /* Table cell styles for smaller screens */
  .user-table th,
  .user-table td {
    padding: 6px;
    font-size: 14px;
  }

  .modal-content {
    padding: 10px;
    font-size: small;
  }

  .close-btn {
    font-size: small;
  }

  /* Generate report button styles for smaller screens */
  .generate-button {
    padding: 8px;
    font-size: small;
  }
}
</style>
