<template>
  <div class="decision--navbar">
    <v-app-bar color="primary">
      <div class="h-100 w-100 d-flex d-md-none justify-space-between align-center">
        <v-app-bar-nav-icon @click="drawer = true" />
        <v-img src="/images/logo.png" max-width="90" max-height="70" class="logo-navbar" />
        <a href="https://line.me/ti/p/EdVPqFEnWH " target="_blank"><img class="nav-text" src="/images/line_icon.svg" width="40px" height="auto"></a>
      </div>

      <div class="w-100 h-100 d-md-flex justify-space-between hidden-md-and-down">
        <v-toolbar-title class="h-100 d-flex align-center">
          <nuxt-link to="/" class="white--text text-decoration-none">
            Decision System
          </nuxt-link>
        </v-toolbar-title>
        <div class="d-flex h-100 nav-list">
          <v-toolbar-title
            class="d-flex align-center px-3 nav-item"
            :class="{'active' : $route.name.includes('index')}"
          >
            <nuxt-link to="/" class="nav-text text-decoration-none">
              หน้าแรก
            </nuxt-link>
          </v-toolbar-title>

          <v-toolbar-title
            class="d-flex align-center px-3 nav-item"
            :class="{'active' : $route.name.includes('news')}"
          >
            <nuxt-link to="/news" class="nav-text text-decoration-none">
              ข่าวสาร
            </nuxt-link>
          </v-toolbar-title>
          <v-toolbar-title
            class="d-flex align-center px-3 nav-item"
            :class="{'active' : $route.name.includes('problems')}"
          >
            <nuxt-link to="/problems" class="nav-text text-decoration-none">
              ปัญหาที่พบเจอ
            </nuxt-link>
          </v-toolbar-title>
        </div>
      </div>
    </v-app-bar>

    <v-navigation-drawer
      v-model="drawer"
      absolute
      temporary
    >
      <v-list
        nav
        dense
      >
        <v-list-item-group
          v-model="group"
          active-class="active"
        >
          <div class="py-3 background-navbar v-list-item-topper">
            <v-img src="/images/logo.png" max-width="90" max-height="90" class="logo-navbar" />
          </div>
          <v-list-item class="mt-3" to="/">
            <v-list-item-icon>
              <v-icon>mdi-home-variant-outline</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Home</v-list-item-title>
          </v-list-item>

          <v-list-item to="/jobs">
            <v-list-item-icon>
              <v-icon>mdi-laptop</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Job</v-list-item-title>
          </v-list-item>

          <v-list-item to="/about-us">
            <v-list-item-icon>
              <v-icon>mdi-account-outline</v-icon>
            </v-list-item-icon>
            <v-list-item-title>About us</v-list-item-title>
          </v-list-item>

          <v-list-item to="/contact">
            <v-list-item-icon>
              <v-icon>mdi-contacts-outline</v-icon>
            </v-list-item-icon>
            <v-list-item-title>Contact</v-list-item-title>
          </v-list-item>

          <v-list-item :class="{'active' : $route.name.includes('resume')}">
            <v-list-item-icon>
              <v-icon>mdi-file-document-outline</v-icon>
            </v-list-item-icon>
            <v-list-item-title>ฝากประวัติ</v-list-item-title>
          </v-list-item>

          <v-list-item>
            <v-list-item-icon>
              <v-icon>mdi-cellphone-iphone</v-icon>
            </v-list-item-icon>
            <v-list-item-title>085 154 9265</v-list-item-title>
          </v-list-item>
        </v-list-item-group>
      </v-list>
    </v-navigation-drawer>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from '@nuxtjs/composition-api'

export default defineComponent({
  setup () {
    const drawer = ref(false)
    const group = ref(null)

    return {
      drawer,
      group
    }
  }
})
</script>

<style lang="scss">
.decision--navbar {
  .v-sheet {
    .v-toolbar__content {
      padding-bottom: 0px !important;
      padding-top: 0px !important;
    }
  }
}
.nav-list {
  cursor: pointer;
  .nav-item {
    a {
      color: $white;
    }
    .nav-text {
      font-size: 14px;
    }
  }
  .nav-item.active {
    a {
      color: $primary;
    }
    background-color: $secondary;
  }
}

.v-list--nav {
  padding: 0px !important;
  .v-list-item {
    padding: 0 0 0 12px !important;
    border-bottom-right-radius: 23px !important;
    border-top-right-radius: 23px !important;
    border-top-left-radius: 0px !important;
    border-bottom-left-radius: 0px !important;
    width: 95% !important;
    .v-list-item__icon {
      margin-right: 12px !important;
      i {
        color: $primary !important;
      }
    }
    .v-list-item__title {
      color: $primary !important;
    }
  }
  .v-list-item.active {
    background-color: $secondary !important;
    .v-list-item__icon {
      i {
        color: $white !important;
      }
    }
    .v-list-item__title {
      color: $white !important;
    }
  }
}

.v-list-item-topper {
  padding-left: 12px;
}

.v-list-item--active {
  &:before {
    opacity: 0 !important;
  }
}
</style>
