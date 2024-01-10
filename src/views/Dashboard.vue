<template>
  <div v-if="is_email_verified">
    <CRow>
      <CCol :md="12">
        <CCard class="mb-4">
          <CCardHeader> Users Statistic </CCardHeader>
          <CCardBody>
            <CTable align="middle" class="mb-0 border" hover responsive>
              <CTableBody>
                <CTableRow>
                  <CTableHeaderCell color="light">
                    Total Registered
                  </CTableHeaderCell>
                  <CTableDataCell>{{ userStats.totalRegister }}</CTableDataCell>
                </CTableRow>
                <CTableRow>
                  <CTableHeaderCell color="light">
                    Active Today
                  </CTableHeaderCell>
                  <CTableDataCell>{{ userStats.activeToday }}</CTableDataCell>
                </CTableRow>
                <CTableRow>
                  <CTableHeaderCell color="light">
                    Average for one week
                  </CTableHeaderCell>
                  <CTableDataCell>{{ userStats.avgWeek }}</CTableDataCell>
                </CTableRow>
              </CTableBody>
            </CTable>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
    <CRow>
      <CCol :md="12">
        <CCard class="mb-4">
          <CCardHeader> Users Data </CCardHeader>
          <CCardBody>
            <CTable align="middle" class="mb-0 border" hover responsive>
              <CTableHead color="light">
                <CTableRow>
                  <CTableHeaderCell>Name</CTableHeaderCell>
                  <CTableHeaderCell>Email</CTableHeaderCell>
                  <CTableHeaderCell>Sign Up Date</CTableHeaderCell>
                  <CTableHeaderCell>Login Count</CTableHeaderCell>
                  <CTableHeaderCell>Last Login</CTableHeaderCell>
                </CTableRow>
              </CTableHead>
              <CTableBody>
                <CTableRow v-for="user in users" :key="user?.id">
                  <CTableDataCell> {{ user?.name ?? '' }} </CTableDataCell>
                  <CTableDataCell> {{ user?.email ?? '' }} </CTableDataCell>
                  <CTableDataCell>
                    {{ user?.created_at ?? '' }}
                  </CTableDataCell>
                  <CTableDataCell>
                    {{ user?.number_of_login ?? '' }}
                  </CTableDataCell>
                  <CTableDataCell>
                    {{ user?.last_login_at ?? '' }}
                  </CTableDataCell>
                </CTableRow>
              </CTableBody>
            </CTable>
          </CCardBody>
        </CCard>
      </CCol>
    </CRow>
  </div>
  <div v-else>
    <h1>Please Verify your email</h1>
    <CButton
      color="primary"
      class="px-4"
      :disabled="disable_button_resend"
      @click="resendEmailActivation"
    >
      Resend Email Verification
    </CButton>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'Dashboard',
  data() {
    return {
      is_email_verified: false,
      disable_button_resend: false,
      access_token: '',
      users: [],
      userStats: {
        activeToday: 0,
        avgWeek: 0,
        totalRegister: 0,
      },
    }
  },
  mounted() {
    this.is_email_verified = false
    this.access_token = this.$cookies.get('access_token')
    axios({
      url: `${process.env.VUE_APP_API_URL}/users/logged-in-user`,
      headers: {
        Authorization: `Bearer ${this.access_token}`,
      },
    })
      .then((el) => {
        if (el?.status === 200) {
          this.is_email_verified = el?.data?.data?.is_email_verified
        }
      })
      .catch((err) => {
        this.errorMessage = err?.response?.data?.message
      })
  },
  watch: {
    is_email_verified: function (val) {
      if (val) {
        axios({
          url: `${process.env.VUE_APP_API_URL}/users`,
          headers: {
            Authorization: `Bearer ${this.access_token}`,
          },
        })
          .then((el) => {
            if (el?.status === 200) {
              this.users = el.data.data
            }
          })
          .catch((err) => {
            this.errorMessage = err?.response?.data?.message
          })

        axios({
          url: `${process.env.VUE_APP_API_URL}/users/stats`,
          headers: {
            Authorization: `Bearer ${this.access_token}`,
          },
        })
          .then((el) => {
            if (el?.status === 200) {
              this.userStats = el.data.data
            }
          })
          .catch((err) => {
            this.errorMessage = err?.response?.data?.message
          })
      }
    },
  },
  methods: {
    async resendEmailActivation() {
      this.disable_button_resend = true
      axios({
        method: 'post',
        url: `${process.env.VUE_APP_API_URL}/users/resend-email-activation`,
        headers: {
          Authorization: `Bearer ${this.access_token}`,
        },
      })
        .then((el) => {
          if (el?.status === 200) {
            this.disable_button_resend = false
          }
        })
        .catch((err) => {
          this.disable_button_resend = false
        })
    },
  },
}
</script>
