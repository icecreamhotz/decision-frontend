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
            :class="{'active' : $route.name.includes('problems') && $route.name !== 'add-problems___th'}"
          >
            <nuxt-link to="/problems" class="nav-text text-decoration-none">
              วิเคราะห์ปัญหา
            </nuxt-link>
          </v-toolbar-title>
          <v-toolbar-title
            class="d-flex align-center px-3 nav-item"
            :class="{'active' : $route.name === 'add-problems___th'}"
          >
            <nuxt-link to="/add-problems" class="nav-text text-decoration-none">
              เพิ่มปัญหา
            </nuxt-link>
          </v-toolbar-title>
          <v-toolbar-title
            class="d-flex align-center px-3 nav-item"
            :class="{'active' : $route.name === 'calendar___th'}"
          >
            <nuxt-link to="/calendar" class="nav-text text-decoration-none">
              ปฏิทิน
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
            <nuxt-link to="/" class="primary--text text-decoration-none">
              Decision System
            </nuxt-link>
          </div>
          <v-list-item class="mt-3" to="/">
            <v-list-item-icon>
              <v-icon>mdi-home-variant-outline</v-icon>
            </v-list-item-icon>
            <v-list-item-title>หน้าแรก</v-list-item-title>
          </v-list-item>

          <v-list-item to="/news">
            <v-list-item-icon>
              <v-icon>mdi-laptop</v-icon>
            </v-list-item-icon>
            <v-list-item-title>ข่าวสาร</v-list-item-title>
          </v-list-item>

          <v-list-item to="/problems">
            <v-list-item-icon>
              <v-icon>mdi-account-outline</v-icon>
            </v-list-item-icon>
            <v-list-item-title>วิเคราะห์ปัญหา</v-list-item-title>
          </v-list-item>
          <v-list-item to="/add-problems">
            <v-list-item-icon>
              <v-icon>mdi-account-outline</v-icon>
            </v-list-item-icon>
            <v-list-item-title>เพิ่มปัญหา</v-list-item-title>
          </v-list-item>
          <v-list-item to="/calendar">
            <v-list-item-icon>
              <v-icon>mdi-calendar-outline</v-icon>
            </v-list-item-icon>
            <v-list-item-title>ปฏิทิน</v-list-item-title>
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
