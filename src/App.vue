<template>
  <div>
    <HeaderTop v-show="topHead"/>
    <Header v-show="mainHead"/>
    <!-- <Sidemenu v-show="sidemenu" v-if="showSideMenu"/> -->
    <Sidemenu v-show="sidemenu" />
    <div v-bind:class="{'container-sidemenu-is-open': sidemenu}" style="display:flex; flex-direction: column;">
      <AdminBar></AdminBar>
        <div>
          <router-view></router-view>
        </div>
    </div>
    <FooterLinks v-show="!sidemenu"/>
  </div>
</template>

<script>
import 'keen-ui/dist/keen-ui.css'
import Header from './views/common/Header'
import FooterLinks from './views/common/FooterLinks'
import HeaderTop from './views/common/HeaderTop'
import Sidemenu from './views/common/Sidemenu'
import AdminBar from './views/dashboard/AdminBar'

export default {
  data () {
    return {
      loggedIn: false,
      topHead: false,
      mainHead: true,
      sidemenu: false,
    }
  },
  computed: {
    showSideMenu () {
      if (typeof this.$route.meta === 'undefined') {
        return true
      } else if (typeof this.$route.meta.hideSideMenu === 'undefined') {
        return true
      } else {
        return !this.$route.meta.hideSideMenu
      }
    },
  },
  created () {
    if (this.$route.path.startsWith('/dashboard') || this.$route.path.startsWith('/settings')) {
      this.topHead = false
      this.mainHead = false
      this.sidemenu = true
    } else {
      this.mainHead = true
      this.topHead = false
      this.sidemenu = false
    }
  },
  mounted () {
    // include intercom chat widget
    this.$store.dispatch('getUserAttributes').then((attributes) => {
      this.$intercom.boot({
        name: attributes['name'],
        email: attributes['email'],
      })
    }).catch(() => {
      this.$intercom.boot({})
    })
  },
  watch: {
    $route (to, from) {
      this.$store.dispatch('getUserAttributes').then((attributes) => {
        this.$intercom.update({ email: attributes['email'], page: this.$route.path, })
      })
      if (this.$route.path.startsWith('/dashboard') || this.$route.path.startsWith('/settings')) {
        this.topHead = false
        this.mainHead = false
        this.sidemenu = true
      } else {
        this.mainHead = true
        this.topHead = false
        this.sidemenu = false
      }
    },
  },
  components: {
    Header,
    FooterLinks,
    HeaderTop,
    Sidemenu,
    AdminBar,
  },
}
</script>

<style lang="scss">
@import "css/design-app.css";
@import "css/sweetalert.css";
@import "css/vendor.min.css";
@import "css/style.css";
@import "bootstrap/dist/css/bootstrap.css";
@import "bootstrap-vue/dist/bootstrap-vue.css";

  *,
  *::before,
  *::after {
      box-sizing: border-box;
  }

  html {
      font-size: 100%;
  }

  .ui-modal__mask {
    z-index: 5000;
  }

  .ui-modal.has-footer .ui-modal__body {
    max-height: calc(100vh - 10.875rem);
  }

  .main {
    position: relative;
  }

  .container-sidemenu-is-open {
    margin-left: 72px;
  }
</style>
