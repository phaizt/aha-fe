<template>
  <div class="bg-light min-vh-100 d-flex flex-row align-items-center">
    <CContainer>
      <CRow class="justify-content-center">
        <CCol :md="9" :lg="7" :xl="6">
          <CCard class="mx-4">
            <CCardBody class="p-4">
              <CForm @submit.prevent="handleSubmit">
                <h1>Register</h1>
                <CAlert color="danger" v-if="errorMessage.length">
                  <ul v-for="(err, idx) in errorMessage" :key="idx">
                    <li>{{ err }}</li>
                  </ul>
                </CAlert>
                <p class="text-medium-emphasis">Create your account</p>
                <CInputGroup class="mb-3">
                  <CInputGroupText>
                    <CIcon icon="cil-user" />
                  </CInputGroupText>
                  <CFormInput
                    placeholder="Name"
                    autocomplete="name"
                    v-model="name"
                  />
                </CInputGroup>
                <CInputGroup class="mb-3">
                  <CInputGroupText>@</CInputGroupText>
                  <CFormInput
                    placeholder="Email"
                    autocomplete="email"
                    v-model="email"
                  />
                </CInputGroup>
                <CInputGroup class="mb-3">
                  <CInputGroupText>
                    <CIcon icon="cil-lock-locked" />
                  </CInputGroupText>
                  <CFormInput
                    type="password"
                    placeholder="Password"
                    autocomplete="new-password"
                    v-model="password"
                  />
                </CInputGroup>
                <CInputGroup class="mb-4">
                  <CInputGroupText>
                    <CIcon icon="cil-lock-locked" />
                  </CInputGroupText>
                  <CFormInput
                    type="password"
                    placeholder="Repeat password"
                    autocomplete="new-password"
                    v-model="password_confirm"
                  />
                </CInputGroup>
                <div class="d-grid">
                  <CButton
                    color="success"
                    type="submit"
                    :disabled="disableButton"
                    >Create Account</CButton
                  >
                </div>
              </CForm>
            </CCardBody>
          </CCard>
        </CCol>
      </CRow>
    </CContainer>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'Register',
  data() {
    return {
      name: '',
      password: '',
      email: '',
      password_confirm: '',
      errorMessage: [],
      disableButton: false,
    }
  },
  methods: {
    async handleSubmit() {
      this.errorMessage = []
      this.disableButton = true
      axios({
        method: 'post',
        url: `${process.env.VUE_APP_API_URL}/users`,
        data: {
          name: this.name,
          password: this.password,
          email: this.email,
          password_confirm: this.password_confirm,
        },
      })
        .then((el) => {
          if (el?.status === 201) {
            alert('success, please login')
            this.$router.push({ path: '/pages/login' })
            this.disableButton = false
          }
        })
        .catch((err) => {
          this.disableButton = false
          this.errorMessage = err?.response?.data?.message
        })
    },
  },
}
</script>
