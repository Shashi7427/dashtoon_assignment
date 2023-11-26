<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title>
          Dashtoon AI App
        </q-toolbar-title>

        <div>Made by Shashi</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
    >
      <q-list>
        <q-item-label
          header
        >
          Essential Links
        </q-item-label>

        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'
import EssentialLink from 'components/EssentialLink.vue'

const linksList = [
  {
    title: 'Code Repository',
    caption: 'github',
    icon: 'code',
    link: 'https://github.com/Shashi7427/dashtoon_assignment'
  },
  {
    title: 'LinkedIn',
    caption: 'Shashi D. Tarode',
    icon: 'public',
    link: 'https://www.linkedin.com/in/shashi-tarode-281a2a1a6/'
  },
  {
    title: 'Github',
    icon: 'rss_feed',
    link: 'https://github.com/Shashi7427'
  },
  {
    title: 'Gitlab',
    caption: 'https://gitlab.com/shashitarode1',
    icon: 'favorite',
    link: 'https://gitlab.com/shashitarode1'
  }
]

export default defineComponent({
  name: 'MainLayout',

  components: {
    EssentialLink
  },

  setup () {
    const leftDrawerOpen = ref(false)

    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      }
    }
  }
})
</script>
