<template>
  <div class="my-account" :key="$route.fullPath">
    <SfContentPages
      title="My account"
      :active="activePage"
      class="my-account__content"
      @click:change="updateActivePage"
    >
      <SfContentCategory title="Personal Details">
        <SfContentPage title="My profile">
          <nuxt-child />
        </SfContentPage>
        <SfContentPage title="My addresses">
          <SfTabs :open-tab="1">
            <nuxt-child />
          </SfTabs>
        </SfContentPage>
      </SfContentCategory>
      <SfContentCategory title="Order details">
        <SfContentPage :title="`Order history (${user && user.orderCount})`">
          <nuxt-child />
        </SfContentPage>
      </SfContentCategory>
      <SfContentPage title="Logout"></SfContentPage>
    </SfContentPages>
  </div>
</template>
<script>
import { SfContentPages, SfTabs } from '@storefront-ui/vue'
import { useUser } from '@shopware-pwa/composables'
import { PAGE_LOGIN } from '@shopware-pwa/default-theme/helpers/pages'

import authMiddleware from '@shopware-pwa/default-theme/middleware/auth'

export default {
  name: 'Account',
  components: {
    SfContentPages,
    SfTabs,
  },
  middleware: authMiddleware,
  setup() {
    const { logout, user, loadOrders, orders } = useUser()
    return { logout, user, loadOrders, orders }
  },
  data() {
    return {
      activePage: 'My profile',
      allAddresses: [],
    }
  },
  computed: {
    activeBillingAddress() {
      return (this.user && this.user && this.user.activeBillingAddress) || {}
    },
    activeShippingAddress() {
      return (this.user && this.user && this.user.activeShippingAddress) || {}
    },
  },
  mounted() {
    this.updateActivePage(this.activePage)
  },
  watch: {
    $route(to, from) {
      if (to.name === 'account-profile') {
        this.activePage = 'My profile'
      }
    },
  },
  methods: {
    async updateActivePage(title) {
      switch (title) {
        case 'My profile':
          this.$router.push('/account/profile')
          break
        case 'My addresses':
          this.$router.push('/account/addresses')
          break
        case `Order history (${this.user && this.user.orderCount})`:
          this.$router.push('/account/orders')
          break
        case 'Logout':
          await this.logout()
          this.$router.push(PAGE_LOGIN)
          break
      }
      this.activePage = title
    },
  },
}
</script>
<style lang="scss" scoped>
@import '~@storefront-ui/vue/styles.scss';

.my-account {
  @include for-desktop {
    max-width: 1272px;
    margin: 0 auto;
    padding: 0 var(--spacer-sm);
  }
  &__content {
    @include for-mobile {
      --content-pages-sidebar-category-title-font-weight: var(--font-normal);
      --content-pages-sidebar-category-title-margin: var(--spacer-sm)
        var(--spacer-sm) var(--spacer-sm) var(--spacer-base);
    }
    @include for-desktop {
      --content-pages-sidebar-category-title-margin: var(--spacer-xl) 0 0 0;
      --content-pages-sidebar-padding: var(--spacer-xl);
      --content-pages-content-padding: var(--spacer-xl);
      --content-pages-sidebar-title-font-weight: var(--font-normal);
    }
  }
}
</style>
