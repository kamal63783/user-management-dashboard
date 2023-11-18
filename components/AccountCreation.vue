<template>
  <!-- Account Creation Container -->
  <div class="account-creation-container">
    <!-- Header Section -->
    <header class="account-creation-header">
      <h1 class="account-creation-title">Account Creation</h1>
    </header>

    <!-- Form Section -->
    <form
      v-if="!submitted"
      class="form-container"
      novalidate
      @submit.prevent="createAccount"
    >
      <!-- Iterating through Form Fields -->
      <div v-for="field in formFields" :key="field.id">
        <div class="input-container">
          <!-- Input Fields -->
          <input
            :id="field.id"
            v-model="formData[field.key]"
            :type="
              field.type === 'password'
                ? showPassword
                  ? 'text'
                  : 'password'
                : field.type
            "
            :required="field.required"
            :placeholder="field.placeholder"
            :minlength="field.minLength ? field.minLength : null"
            :pattern="field.pattern ? field.pattern : null"
            :class="[
              field.type === 'password' ? 'password-input' : '',
              {
                'valid-password': isPasswordValid && isPasswordLengthValid,
                'invalid-input': !isPasswordValid || !isPasswordLengthValid,
                'show-warning':
                  field.type === 'password' && showPasswordWarning,
              },
            ]"
            @input="checkPasswordValidity"
            @focus="handleFieldFocus(field.key)"
            @blur="validatePassword"
          />

          <!-- Password Warning Message -->
          <div
            v-if="field.type === 'password' && showPasswordWarning"
            class="warning-message-container"
          >
            <div class="warning-message">
              Password must contain 8 to 15 characters with uppercase,
              lowercase, number, and special character.
            </div>
          </div>

          <!-- Eye Icon for Password Field -->
          <button
            v-if="field.type === 'password'"
            class="toggle-password"
            @click="togglePasswordVisibility"
          >
            <i :class="showPassword ? 'fas fa-eye-slash' : 'fas fa-eye'"></i>
          </button>
        </div>
      </div>

      <!-- Submit Button -->
      <div>
        <button type="submit">Create Account</button>
      </div>
    </form>

    <!-- Loading Animation Section -->
    <div v-if="loading" class="loading-animation">
      <img src="~static/loading.gif" alt="Uploading Details..." />
      <h3>Uploading Details...</h3>
    </div>

    <!-- Success Message Section -->
    <div v-if="showSuccessMessage" class="success-message">
      <img src="~static/success.gif" alt="Successfully Submitted" />
      <h3>Successfully Submitted</h3>
      <!-- Option to Submit Another Form -->
      <div class="submit-another-form">
        <button @click="resetForm">Submit Another Form</button>
      </div>
    </div>

    <!-- Failed Message Section -->
    <div v-if="showFailedMessage" class="failed-message">
      <img src="~static/error.gif" alt="Submission Failed" />
      <h3>Submission Failed</h3>
      <!-- Option to Try Again -->
      <div class="try-again">
        <button @click="resetForm">Try Again</button>
      </div>
    </div>
  </div>
</template>

<script>
import '@fortawesome/fontawesome-free/css/all.css'
export default {
  data() {
    return {
      // Form data and states
      formData: {
        username: '',
        password: '',
        email: '',
        phone: '',
      },
      submitted: false,
      loading: false,
      showSuccessMessage: false,
      showFailedMessage: false,
      isPasswordFieldFocused: false,
      showPassword: false,
      showPasswordWarning: false,

      // Form fields with details
      formFields: [
        {
          id: 'username',
          key: 'username',
          type: 'text',
          required: true,
          placeholder: 'User Name',
        },
        {
          id: 'password',
          key: 'password',
          type: 'password',
          required: true,
          placeholder: 'Password',
          minLength: 8,
          maxLength: 15,
          pattern:
            /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&#()[\]^_{}|~])[A-Za-z\d@$!%*?&#()[\]^_{}|~]{8,15}$/,
        },
        {
          id: 'email',
          key: 'email',
          type: 'email',
          required: true,
          placeholder: 'Email',
        },
        {
          id: 'phone',
          key: 'phone',
          type: 'tel',
          required: true,
          placeholder: 'Phone',
        },
      ],
    }
  },

  computed: {
    isPasswordValid() {
      const passwordPattern =
        /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&#()[\]^_{}|~])[A-Za-z\d@$!%*?&#()[\]^_{}|~]{8,15}$/
      return passwordPattern.test(this.formData.password)
    },
    isPasswordLengthValid() {
      const isLengthValid = this.formData.password.length >= 8
      return isLengthValid
    },
  },

  methods: {
    async createAccount() {
      // Check if all required fields are filled
      if (
        this.formData.username &&
        this.formData.password &&
        this.formData.email &&
        this.formData.phone
      ) {
        this.submitted = true
        this.loading = true
        try {
          if (this.formData.email === 'failed@example.com') {
            throw new Error('Error: This email is not allowed')
          }
          // Simulating request delay
          await this.simulateRequest()
          setTimeout(() => {
            this.loading = false
            this.showSuccessMessage = true
          }, 3000)
        } catch (error) {
          setTimeout(() => {
            this.loading = false
            this.showFailedMessage = true
          }, 3000)
        }
      }
    },
    simulateRequest() {
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve('Success')
        }, 1500)
      })
    },
    // Reset form fields and states
    resetForm() {
      this.submitted = false
      this.showSuccessMessage = false
      this.showFailedMessage = false
      this.isPasswordFieldFocused = false
      this.showPassword = false
      this.showPasswordWarning = false
      this.formData = {
        username: '',
        password: '',
        email: '',
        phone: '',
      }
    },
    // Utility Methods for Form Validation and Interactions
    checkPasswordValidity() {
      this.isPasswordFieldFocused = this.formData.password !== ''
    },

    isPasswordPatternValid() {
      const passwordPattern =
        /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&#()[\]^_{}|~])[A-Za-z\d@$!%*?&#()[\]^_{}|~]{8,15}$/
      return passwordPattern.test(this.formData.password)
    },
    togglePasswordVisibility() {
      this.showPassword = !this.showPassword
      event.preventDefault()
    },
    handleFieldFocus(fieldName) {
      this.isPasswordFieldFocused = fieldName === 'password'
    },
    validatePassword() {
      if (this.formData.password !== '' && !this.isPasswordPatternValid()) {
        this.showPasswordWarning = true
      } else {
        this.showPasswordWarning = false
      }
    },
  },
}
</script>

<style>
/* Styles for input container */
.input-container {
  position: relative;
  display: inline-block;
}

/* Styles for the form container */
.form-container {
  background-color: rgba(255, 255, 255, 0.8); /* Adjust the color and opacity */
  padding: 20px; /* Adjust padding */
  border-radius: 8px; /* Add border radius for rounded corners */
}

.account-creation-title {
  font-size: 2.5rem;
  font-weight: bold;
  color: white;
}

/* Styles for the account creation container */
.account-creation-container {
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
}

.account-creation-header {
  padding: 20px;
  text-align: center;
}

/* Form container styles */
.account-creation-container form {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-top: 20px;
}

.account-creation-container form div {
  display: flex;
  flex-direction: column;
  margin-bottom: 15px;
}

.account-creation-container form label {
  font-size: 1rem;
  margin-bottom: 5px;
  color: white;
}

/* Styles for input fields in the form */
.account-creation-container form input[type='text'],
.account-creation-container form input[type='password'],
.account-creation-container form input[type='email'],
.account-creation-container form input[type='tel'] {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 400px;
  font-size: 14px;
  box-sizing: border-box;
  margin-bottom: 15px;
}

/* Focus styles for input fields */
.account-creation-container form input[type='text']:focus,
.account-creation-container form input[type='password']:focus,
.account-creation-container form input[type='email']:focus,
.account-creation-container form input[type='tel']:focus {
  outline: none;
  border-color: #007bff;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}

/* Placeholder styles for input fields */
.account-creation-container form input[type='text']::placeholder,
.account-creation-container form input[type='password']::placeholder,
.account-creation-container form input[type='email']::placeholder,
.account-creation-container form input[type='tel']::placeholder {
  color: #aaa;
}

/* Styles for password field */
.account-creation-container form input[type='password'].valid-password {
  border-color: #28a745 !important;
  box-shadow: 0 0 5px rgba(40, 167, 69, 0.5) !important;
}

.toggle-password {
  position: absolute;
  top: 20px;
  right: 10px;
  transform: translateY(-50%);
  background: none;
  border: none;
  cursor: pointer;
}

/* Styles for the submit button in the form */
.account-creation-container form button[type='submit'] {
  padding: 10px 20px;
  border-radius: 5px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

/* Hover styles for the submit button */
.account-creation-container form button[type='submit']:hover {
  background-color: #0056b3;
  transform: scale(1.05);
}

/* Styles for invalid input fields */
.account-creation-container form input:invalid {
  border-color: #dc3545;
  box-shadow: 0 0 5px rgba(220, 53, 69, 0.5);
}

.account-creation-container form input:valid {
  border-color: #28a745;
  box-shadow: 0 0 5px rgba(40, 167, 69, 0.5);
}

/* Styles for warning message */
.warning-message-container {
  max-height: 100px; /* Set a maximum height */
  overflow: hidden; /* Hide overflow content */
  margin-bottom: 5px; /* Add some spacing */
  max-width: 300px; /* Ensure the warning message spans the entire width */
}

.warning-message {
  background-color: red; /* Set background color */
  padding: 5px; /* Add padding */
  color: #fff;
  border-radius: 4px;
  font-size: 10px;
}

/* Styles for the loading animation */
.loading-animation {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
  width: 250px;
  height: auto;
}

.loading-animation h3 {
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 10px;
}

/* Styles for the success message */
.success-message {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
  width: 400px;
  height: auto;
}

.success-message h3 {
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 10px;
}

.success-message .submit-another-form button {
  padding: 8px 16px;
  border-radius: 5px;
  background-color: #2980b9;
  color: #fff;
  border: none;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.success-message .submit-another-form button:hover {
  background-color: #007bff;
  transform: scale(1.05);
}

/* Styles for the failed message */
.failed-message {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: white;
  width: 300px;
  height: auto;
}

.failed-message h3 {
  font-size: 1.5rem;
  font-weight: bold;
  margin-bottom: 10px;
}

.failed-message .try-again button {
  padding: 8px 16px;
  border-radius: 5px;
  background-color: #dc3545;
  color: #fff;
  border: none;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease, transform 0.2s ease;
}

.failed-message .try-again button:hover {
  background-color: #c82333;
  transform: scale(1.05);
}

/* Media Query for Small Screens (e.g., Mobiles) */
@media screen and (max-width: 768px) {
  .account-creation-title {
    font-size: 1.75rem;
  }
  .account-creation-container form {
    width: 90%;
    max-width: 400px;
  }

  .account-creation-container form input[type='text'],
  .account-creation-container form input[type='password'],
  .account-creation-container form input[type='email'],
  .account-creation-container form input[type='tel'] {
    width: 100%;
    max-width: 100%;
    padding: 10px;
    font-size: 14px;
    border: 1px solid #ccc;
    border-radius: 4px;
    margin-bottom: 15px;
  }

  .account-creation-container form input[type='text']::placeholder,
  .account-creation-container form input[type='password']::placeholder,
  .account-creation-container form input[type='email']::placeholder,
  .account-creation-container form input[type='tel']::placeholder {
    color: #999;
    font-size: 12px;
  }

  .account-creation-container form input[type='text']:focus,
  .account-creation-container form input[type='password']:focus,
  .account-creation-container form input[type='email']:focus,
  .account-creation-container form input[type='tel']:focus {
    outline: none;
    border-color: #007bff;
    box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
  }

  .account-creation-container form button[type='submit'] {
    padding: 12px 24px;
    border-radius: 5px;
    background-color: #007bff;
    color: #fff;
    border: none;
    cursor: pointer;
    font-size: 1rem;
    transition: background-color 0.3s ease, transform 0.2s ease;
  }

  .account-creation-container form button[type='submit']:hover {
    background-color: #0056b3;
    transform: scale(1.05);
  }
}
</style>
