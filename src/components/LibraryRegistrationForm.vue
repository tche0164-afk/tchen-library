<template>
  <div class="container mt-5">
    <div class="row">
      <div class="col-md-8 offset-md-2">
        <!-- Form Title -->
        <h1 class="text-center">User Information Form</h1>

        <!-- User Information Form -->
        <form @submit.prevent="submitForm">
          <!-- Username and Password -->
          <div class="row mb-3">
            <!-- Username -->
            <div class="col-md-6">
              <label for="username" class="form-label"> Username </label>

              <input
                type="text"
                class="form-control"
                id="username"
                v-model="formData.username"
                @blur="() => validateName(true)"
                @input="() => validateName(false)"
              />

              <div v-if="errors.username" class="text-danger">
                {{ errors.username }}
              </div>
            </div>

            <!-- Password -->
            <div class="col-md-6">
              <label for="password" class="form-label"> Password </label>

              <input
                type="password"
                class="form-control"
                id="password"
                v-model="formData.password"
                @blur="() => validatePassword(true)"
                @input="() => validatePassword(false)"
              />

              <div v-if="errors.password" class="text-danger">
                {{ errors.password }}
              </div>
            </div>
          </div>

          <!-- Australian Resident and Gender -->
          <div class="row mb-3">
            <!-- Australian Resident -->
            <div class="col-md-6">
              <div class="form-check">
                <input
                  type="checkbox"
                  class="form-check-input"
                  id="isAustralian"
                  v-model="formData.isAustralian"
                  @change="() => validateResident(true)"
                />

                <label class="form-check-label" for="isAustralian"> Australian Resident? </label>
              </div>

              <div v-if="errors.isAustralian" class="text-danger">
                {{ errors.isAustralian }}
              </div>
            </div>

            <!-- Gender -->
            <div class="col-md-6">
              <label for="gender" class="form-label"> Gender </label>

              <select
                class="form-select"
                id="gender"
                v-model="formData.gender"
                @change="() => validateGender(true)"
              >
                <option value="">Please select</option>
                <option value="male">Male</option>
                <option value="female">Female</option>
                <option value="other">Other</option>
              </select>

              <div v-if="errors.gender" class="text-danger">
                {{ errors.gender }}
              </div>
            </div>
          </div>

          <!-- Reason -->
          <div class="mb-3">
            <label for="reason" class="form-label"> Reason for joining </label>

            <textarea
              class="form-control"
              id="reason"
              rows="3"
              v-model="formData.reason"
              @blur="() => validateReason(true)"
              @input="() => validateReason(false)"
            ></textarea>

            <div v-if="errors.reason" class="text-danger">
              {{ errors.reason }}
            </div>
          </div>

          <!-- Buttons -->
          <div class="text-center">
            <button type="submit" class="btn btn-primary me-2">Submit</button>

            <button type="button" class="btn btn-secondary" @click="clearForm">Clear</button>
          </div>
        </form>

        <!-- Submitted User Information DataTable -->
        <div class="mt-5" v-if="submittedCards.length">
          <DataTable :value="submittedCards" tableStyle="min-width: 50rem">
            <Column field="username" header="Username" />

            <Column field="password" header="Password" />

            <Column field="isAustralian" header="Australian Resident" />

            <Column field="gender" header="Gender" />

            <Column field="reason" header="Reason" />
          </DataTable>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import DataTable from 'primevue/datatable'
import Column from 'primevue/column'

// Store the current form data
const formData = ref({
  username: '',
  password: '',
  isAustralian: false,
  reason: '',
  gender: '',
})

// Store all submitted user information
const submittedCards = ref([])

// Store validation errors
const errors = ref({
  username: null,
  password: null,
  isAustralian: null,
  gender: null,
  reason: null,
})

// Validate username
const validateName = (blur) => {
  if (formData.value.username.length < 3) {
    if (blur) {
      errors.value.username = 'Name must be at least 3 characters'
    }
  } else {
    errors.value.username = null
  }
}

// Validate password
const validatePassword = (blur) => {
  const password = formData.value.password
  const minLength = 8

  const hasUppercase = /[A-Z]/.test(password)
  const hasLowercase = /[a-z]/.test(password)
  const hasNumber = /\d/.test(password)
  const hasSpecialChar = /[!@#$%^&*(),.?":{}|<>]/.test(password)

  if (password.length < minLength) {
    if (blur) {
      errors.value.password = 'Password must be at least 8 characters'
    }
  } else if (!hasUppercase) {
    if (blur) {
      errors.value.password = 'Password must contain an uppercase letter'
    }
  } else if (!hasLowercase) {
    if (blur) {
      errors.value.password = 'Password must contain a lowercase letter'
    }
  } else if (!hasNumber) {
    if (blur) {
      errors.value.password = 'Password must contain a number'
    }
  } else if (!hasSpecialChar) {
    if (blur) {
      errors.value.password = 'Password must contain a special character'
    }
  } else {
    errors.value.password = null
  }
}

// Validate Australian residency
const validateResident = (blur) => {
  if (!formData.value.isAustralian) {
    if (blur) {
      errors.value.isAustralian = 'You must confirm Australian residency'
    }
  } else {
    errors.value.isAustralian = null
  }
}

// Validate gender
const validateGender = (blur) => {
  if (!formData.value.gender) {
    if (blur) {
      errors.value.gender = 'Please select a gender'
    }
  } else {
    errors.value.gender = null
  }
}

// Validate reason for joining
const validateReason = (blur) => {
  if (formData.value.reason.length < 10) {
    if (blur) {
      errors.value.reason = 'Reason must be at least 10 characters'
    }
  } else {
    errors.value.reason = null
  }
}

// Submit the form
const submitForm = () => {
  validateName(true)
  validatePassword(true)
  validateResident(true)
  validateGender(true)
  validateReason(true)

  if (
    !errors.value.username &&
    !errors.value.password &&
    !errors.value.isAustralian &&
    !errors.value.gender &&
    !errors.value.reason
  ) {
    submittedCards.value.push({
      ...formData.value,
    })
  }
}

// Clear the form
const clearForm = () => {
  formData.value = {
    username: '',
    password: '',
    isAustralian: false,
    reason: '',
    gender: '',
  }

  errors.value = {
    username: null,
    password: null,
    isAustralian: null,
    gender: null,
    reason: null,
  }
}
</script>

<style scoped>
.card {
  border: 1px solid #ccc;
  border-radius: 10px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.card-header {
  background-color: #275fda;
  color: white;
  padding: 10px;
  border-radius: 10px 10px 0 0;
}

.list-group-item {
  padding: 10px;
}
</style>
