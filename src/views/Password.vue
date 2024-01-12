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
                <CAlert
                  v-if="is_oauth && !is_password_changed"
                  color="info"
                  class="text-sm"
                >
                  You are logged in as google account, you can change your
                  password without old password for the first time
                </CAlert>
                <CInputGroup
                  v-if="is_oauth && is_password_changed"
                  class="mb-3"
                >
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
      is_oauth: false,
      is_password_changed: false,
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
          this.is_oauth = el?.data?.data?.is_oauth ?? false
          this.is_password_changed =
            el?.data?.data?.is_password_changed ?? false
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
      if (this.is_oauth && !this.is_password_changed) {
        this.old_password = this.password
      }
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
            this.is_password_changed = el?.data?.data.is_password_changed
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
