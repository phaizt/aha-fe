<template>
  <div class="bg-light min-vh-100 d-flex flex-row align-items-center">
    <CContainer>
      <CRow class="justify-content-center">
        <CCol :md="9" :lg="7" :xl="6">
          <CCard class="mx-4">
            <CCardBody class="p-4">
              <CForm @submit.prevent="handleSubmit">
                <h1>Update Password</h1>
                <CAlert color="danger" v-if="errorMessage.length">
                  <ul v-for="(err, idx) in errorMessage" :key="idx">
                    <li>{{ err }}</li>
                  </ul>
                </CAlert>
                <CInputGroup class="mb-3">
                  <CInputGroupText>
                    <CIcon icon="cil-lock-locked" />
                  </CInputGroupText>
                  <CFormInput
                    type="password"
                    placeholder="Old Password"
                    autocomplete="old-password"
                    v-model="old_password"
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
                    >Update Password</CButton
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
  name: 'Password',
  data() {
    return {
      old_password: '',
      password: '',
      password_confirm: '',
      errorMessage: [],
      disableButton: false,
      access_token: '',
    }
  },
  mounted() {
    this.access_token = this.$cookies.get('access_token')
    axios({
      url: `${process.env.VUE_APP_API_URL}/users/profile`,
      headers: {
        Authorization: `Bearer ${this.access_token}`,
      },
    })
      .then((el) => {
        if (el?.status === 200) {
          this.name = el?.data?.data?.name
          this.email = el?.data?.data?.email
        }
      })
      .catch((err) => {
        this.errorMessage = err?.response?.data?.message
      })
  },
  methods: {
    async handleSubmit() {
      this.errorMessage = []
      this.disableButton = true
      axios({
        method: 'put',
        url: `${process.env.VUE_APP_API_URL}/users/reset-password`,
        headers: {
          Authorization: `Bearer ${this.access_token}`,
        },
        data: {
          password: this.password,
          password_confirm: this.password_confirm,
          old_password: this.old_password,
        },
      })
        .then((el) => {
          if (el?.status === 200) {
            alert('success')
            this.disableButton = false
            this.password = ''
            this.password_confirm = ''
            this.old_password = ''
          }
        })
        .catch((err) => {
          this.errorMessage = err?.response?.data?.message
          this.disableButton = false
        })
    },
  },
}
</script>
