<template>
  <div class="bg-light min-vh-100 d-flex flex-row align-items-center">
    <CContainer>
      <CRow class="justify-content-center">
        <CCol :md="9" :lg="7" :xl="6">
          <CCard class="mx-4">
            <CCardBody class="p-4">
              <CForm @submit.prevent="handleSubmit">
                <h1>Profile</h1>
                <CAlert color="danger" v-if="errorMessage.length">
                  <ul v-for="(err, idx) in errorMessage" :key="idx">
                    <li>{{ err }}</li>
                  </ul>
                </CAlert>
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
                    disabled
                    v-model="email"
                  />
                </CInputGroup>
                <div class="d-grid">
                  <CButton
                    color="success"
                    type="submit"
                    :disabled="disableButton"
                    >Update Profile</CButton
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
  name: 'Profile',
  data() {
    return {
      name: '',
      email: '',
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
        url: `${process.env.VUE_APP_API_URL}/users/update-profile`,
        data: {
          name: this.name,
        },
        headers: {
          Authorization: `Bearer ${this.access_token}`,
        },
      })
        .then((el) => {
          if (el?.status === 200) {
            this.disableButton = false
            alert('success')
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
