<template>
  <CDropdown variant="nav-item">
    <CDropdownToggle
      placement="bottom-end"
      class="py-0"
      :caret="false"
      @click.prevent
    >
      <CAvatar :src="avatar" size="md" />
    </CDropdownToggle>
    <CDropdownMenu class="pt-0">
      <CDropdownHeader component="h6" class="bg-light fw-semibold py-2">
        Account
      </CDropdownHeader>
      <CDropdownItem @click="this.$router.push({ path: 'profile' })">
        <CIcon icon="cil-user" /> Profile
      </CDropdownItem>
      <CDropdownHeader component="h6" class="bg-light fw-semibold py-2">
        Settings
      </CDropdownHeader>
      <CDropdownItem @click="this.$router.push({ path: 'password' })">
        <CIcon icon="cil-settings" /> Change Password
      </CDropdownItem>
      <CDropdownDivider />
      <CDropdownItem @click="logout">
        <CIcon icon="cil-lock-locked" /> Logout
      </CDropdownItem>
    </CDropdownMenu>
  </CDropdown>
</template>

<script>
import avatar from '@/assets/images/avatars/8.jpg'
export default {
  name: 'AppHeaderDropdownAccnt',
  setup() {
    return {
      avatar: avatar,
      itemsCount: 42,
    }
  },
  methods: {
    async logout() {
      this.$cookies.remove('access_token')
      this.$router.push({ path: '/pages/login' })
    },
  },
}
</script>
