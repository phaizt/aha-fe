<template>
  <div class="bg-light min-vh-100 d-flex flex-row align-items-center">
    <CContainer>
      <CRow class="justify-content-center">
        <CCol :md="8">
          <CCardGroup>
            <CCard class="p-4">
              <CCardBody>
                <CForm @submit.prevent="handleSubmit">
                  <h1>Login</h1>
                  <CAlert color="danger" v-if="errorMessage.length">
                    <ul v-for="(err, idx) in errorMessage" :key="idx">
                      <li>{{ err }}</li>
                    </ul>
                  </CAlert>
                  <p class="text-medium-emphasis">Sign In to your account</p>
                  <CInputGroup class="mb-3">
                    <CInputGroupText>
                      <CIcon icon="cil-user" />
                    </CInputGroupText>
                    <CFormInput
                      placeholder="Username"
                      autocomplete="username"
                      v-model="email"
                    />
                  </CInputGroup>
                  <CInputGroup class="mb-4">
                    <CInputGroupText>
                      <CIcon icon="cil-lock-locked" />
                    </CInputGroupText>
                    <CFormInput
                      type="password"
                      placeholder="Password"
                      autocomplete="current-password"
                      v-model="password"
                    />
                  </CInputGroup>
                  <CRow>
                    <CCol :xs="6">
                      <CButton color="primary" class="px-4" type="submit">
                        Login
                      </CButton>
                    </CCol>
                    <CCol :xs="6" class="text-right">
                      <CButton
                        color="secondary"
                        class="px-4 text-sm"
                        @click="googleLogin"
                      >
                        Google Login
                      </CButton>
                    </CCol>
                  </CRow>
                </CForm>
              </CCardBody>
            </CCard>
            <CCard class="text-white bg-primary py-5" style="width: 44%">
              <CCardBody class="text-center">
                <div>
                  <h2>Sign up</h2>
                  <p>
                    Lorem ipsum dolor sit amet, consectetur adipisicing elit,
                    sed do eiusmod tempor incididunt ut labore et dolore magna
                    aliqua.
                  </p>
                  <CButton
                    color="light"
                    variant="outline"
                    class="mt-3"
                    @click="this.$router.push({ path: 'register' })"
                  >
                    Register Now!
                  </CButton>
                </div>
              </CCardBody>
            </CCard>
          </CCardGroup>
        </CCol>
      </CRow>
    </CContainer>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Login',
  data: () => {
    return {
      email: '',
      password: '',
      errorMessage: [],
    }
  },
  mounted() {
    const access_token = this.$route.query?.access_token
    if (access_token) {
      this.$cookies.config(60 * 60 * 24 * 30, '')
      this.$cookies.set('access_token', access_token)
    }

    if (this.$cookies.get('access_token')) {
      this.$router.push({ path: '/dashboard' })
    }
  },
  methods: {
    async handleSubmit() {
      this.errorMessage = []
      axios({
        method: 'post',
        url: `${process.env.VUE_APP_API_URL}/auth/login`,
        data: {
          email: this.email,
          password: this.password,
        },
      })
        .then((el) => {
          if (el?.status === 200) {
            this.$cookies.config(60 * 60 * 24 * 30, '')
            this.$cookies.set('access_token', el?.data?.access_token)
            this.$router.push({ path: '/dashboard' })
          }
        })
        .catch((err) => {
          const errMsg = err?.response?.data?.message
          this.errorMessage = Array.isArray(errMsg) ? errMsg : [errMsg]
        })
    },
    googleLogin() {
      window.location.href = `${process.env.VUE_APP_API_URL}/google/redirect`
    },
  },
}
</script>
