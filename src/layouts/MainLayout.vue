<template>
  <q-layout view="hHh lpR fFf" class="bg-grey-1">
    <q-header elevated class="bg-white text-grey-8">
      <q-toolbar>
        <q-btn flat dense round icon="perm_data_setting" aria-label="Menu" class="q-ma-sm" @click="a='leftDrawerOpen = !leftDrawerOpen'"/>

        <q-separator vertical inset />

        <q-toolbar-title>
          MongoDB实验
        </q-toolbar-title>

        <q-tabs v-model="tab" inline-label shrink class="bg-transparent">
          <q-route-tab name="home" icon="home" label="首页" to="/index" exact/>
          <q-route-tab name="experience2" icon="perm_data_setting" label="实验二" to="/experience2" exact/>
          <q-route-tab name="experience3" icon="perm_data_setting" label="实验三" to="/experience3" exact/>
          <q-route-tab name="experience4" icon="perm_data_setting" label="实验四" to="/experience4" exact/>
          <q-route-tab name="experience5" icon="perm_data_setting" label="实验五" to="/experience5" exact/>
          <q-route-tab name="experience6" icon="perm_data_setting" label="实验六" to="/experience6" exact/>
        </q-tabs>

        <q-separator vertical inset/>

        <q-btn flat dense round icon="account_circle" aria-label="Menu" class="q-ma-sm" @click="rightDrawerOpen = !rightDrawerOpen"/>

      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" bordered side="left" content-class="bg-white">
      <q-list>
        <q-item-label header class="text-grey-8">
          Essential Links
        </q-item-label>
        <EssentialLink v-for="link in essentialLinks" :key="link.title" v-bind="link"/>
      </q-list>
    </q-drawer>

    <q-drawer v-model="rightDrawerOpen" bordered side="right" content-class="bg-white">
      <q-list>
        <q-item-label header class="text-grey-8">
          用户中心
        </q-item-label>

        <router-link v-for="link in userLinks" :key="link.title" :to="'/'+link.tab">
          <q-item clickable tag="a" target="_blank" @click="tab = link.tab;rightDrawerOpen = false;">
            <q-item-section v-if="link.icon" avatar>
              <q-icon :name="link.icon" />
            </q-item-section>
            <q-item-section>
              <q-item-label>{{ link.title }}</q-item-label>
              <q-item-label caption>{{ link.caption }}</q-item-label>
            </q-item-section>
          </q-item>
        </router-link>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink.vue'

const linksData = [
  {
    title: 'Docs',
    caption: 'quasar.dev',
    icon: 'school',
    link: 'https://quasar.dev'
  },
  {
    title: 'Github',
    caption: 'github.com/quasarframework',
    icon: 'code',
    link: 'https://github.com/quasarframework'
  },
  {
    title: 'Discord Chat Channel',
    caption: 'chat.quasar.dev',
    icon: 'chat',
    link: 'https://chat.quasar.dev'
  },
  {
    title: 'Forum',
    caption: 'forum.quasar.dev',
    icon: 'record_voice_over',
    link: 'https://forum.quasar.dev'
  },
  {
    title: 'Twitter',
    caption: '@quasarframework',
    icon: 'rss_feed',
    link: 'https://twitter.quasar.dev'
  },
  {
    title: 'Facebook',
    caption: '@QuasarFramework',
    icon: 'public',
    link: 'https://facebook.quasar.dev'
  },
  {
    title: 'Quasar Awesome',
    caption: 'Community Quasar projects',
    icon: 'favorite',
    link: 'https://awesome.quasar.dev'
  }
];

const userLinksData = [
  {
    title: '登录',
    caption: 'Login',
    icon: 'login',
    tab: 'login'
  },
  {
    title: '乘客管理',
    caption: 'Passengers',
    icon: 'chat',
    tab: 'passenger'
  },
  {
    title: '订单管理',
    caption: 'Orders',
    icon: 'record_voice_over',
    tab: 'order'
  }
];

export default {
  name: 'MainLayout',
  components: {
    EssentialLink
  },
  data () {
    return {
      leftDrawerOpen: false,
      rightDrawerOpen: false,
      essentialLinks: linksData,
      userLinks: userLinksData,
      tab: 'home'
    }
  }
}
</script>

<style scoped>
.q-toolbar{
  padding-left: calc(50% - 512px);
  padding-right: calc(50% - 512px);
  padding-bottom: 0px;
}
</style>
