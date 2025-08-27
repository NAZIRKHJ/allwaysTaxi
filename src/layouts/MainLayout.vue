<template>
  <q-layout view="lHh Lpr lff" style="">
    <q-header>
      <q-toolbar class="flex flex-center header-container">
        <div class="container flex flex-center" style="height: 72px; justify-content: flex-start">
          <div class="header-brand">
            <a href="/">
              <img
              alt="Allways Taxi"
              src="~assets/allwayspng.png"
              style="width: auto; height: 100%"
              loading="lazy"
            />
            </a>
       
          </div>
          <div class="header-actions">
            <div class="spacer"></div>
            <div class="header-nav gt-sm">
              <q-btn
                class="nav-btn"
                flat
                dense
                no-caps
                color="black"
                label="Bereken je rit"
                @click="goToSection('calculate')"
              />
              <q-btn
                class="nav-btn"
                flat
                dense
                no-caps
                color="black"
                label="Onze diensten"
                @click="goToSection('services')"
              />
              <q-btn
                class="nav-btn"
                flat
                dense
                no-caps
                color="black"
                label="BOEK NU"
                @click="goToSection('reviews')"
              />
              <q-btn
                class="nav-btn whatsapp-btn"
                flat
                dense
                no-caps
                icon="fa-brands fa-whatsapp"
                label="WhatsApp"
                :href="whatsAppUrl"
                target="_blank"
              />
            </div>
            <div class="header-menu lt-md">
              <q-btn
                flat
                round
                icon="fa-brands fa-whatsapp"
                color="green-6"
                :href="whatsAppUrl"
                target="_blank"
                class="q-mr-sm"
              />
              <q-btn flat round icon="menu" color="black" @click="leftDrawerOpen = true" />
            </div>
          </div>
        </div>
      </q-toolbar>
    </q-header>
    <q-drawer
      v-model="leftDrawerOpen"
      side="left"
      :overlay="true"
      elevated
      color="white"
      :width="drawerWidth"
      class="app-drawer drawer--blur-backdrop"
      @hide="onDrawerHide"
    >
      <div class="drawer-header">
        <div class="drawer-brand">
          <div class="drawer-brand-logo">
            <img alt="Allways Taxi" src="~assets/allwayspng.png" />
          </div>
          <div class="drawer-brand-text">
            <div class="drawer-title">Allways Taxi</div>
            <div class="drawer-subtitle">Een taxi bestellen in heel Nederland</div>
          </div>
        </div>
        <q-btn icon="close" flat round dense color="black" @click="leftDrawerOpen = false" />
      </div>
      <q-separator />
      <q-list class="drawer-list">
        <q-item clickable v-ripple @click="navigateAndClose('calculate')">
          <div class="q-item-type flex">
          <q-item-section avatar>
            <q-icon name="directions_car" size="24px" />
          </q-item-section>
          <q-item-section>Bereken je rit</q-item-section>
          </div>
        </q-item>
        <q-item clickable v-ripple @click="navigateAndClose('services')">
          <div class="q-item-type flex">
          <q-item-section avatar>
            <q-icon name="miscellaneous_services" size="24px" />
          </q-item-section>
          <q-item-section>Onze diensten</q-item-section>
          </div>
        </q-item>
        <q-item clickable v-ripple @click="navigateAndClose('reviews')">
          <div class="q-item-type flex">
          <q-item-section avatar>
            <q-icon name="reviews" size="24px" />
          </q-item-section>
          <q-item-section>Onze reviews</q-item-section>
          </div>
        </q-item>
      </q-list>
    </q-drawer>
    <q-page-container class="container">
      <router-view />
    </q-page-container>

    <q-footer class="app-footer">
      <div class="container footer-top">
        <div class="footer-brand">
          <img alt="Allways Taxi" src="~assets/allwayspng.png" class="footer-logo" loading="lazy" />
        </div>
        <div class="footer-social">
          <q-btn
            round
            flat
            color="black"
            :ripple="false"
            icon="fa-brands fa-facebook"
            href="#"
            target="_blank"
          />
     
          <q-btn
            round
            flat
            color="black"
            :ripple="false"
            icon="fa-brands fa-instagram"
            href="#"
            target="_blank"
          />
    
        </div>
      </div>
      <div class="footer-separator"></div>
      <div class="container footer-bottom">
        <div class="footer-copy">© 2025 AllwaysTaxi B.V.</div>
        <div class="footer-links">
          <router-link to="/over-ons" class="footer-link">Over ons</router-link>
          <span class="dot">•</span>
          <router-link to="/privacy-cookies" class="footer-link">Privacy & Cookies</router-link>
          <span class="dot">•</span>
          <a href="#" class="footer-link">Disclaimer</a>
        </div>
      </div>
    </q-footer>
  </q-layout>
</template>

<script setup>
import { ref, computed, nextTick } from 'vue'
import { useRouter } from 'vue-router'
import { useQuasar } from 'quasar'
// import EssentialLink from 'components/EssentialLink.vue'

// const linksList = [
//   {
//     title: 'Docs',
//     caption: 'quasar.dev',
//     icon: 'school',
//     link: 'https://quasar.dev',
//   },
//   {
//     title: 'Github',
//     caption: 'github.com/quasarframework',
//     icon: 'code',
//     link: 'https://github.com/quasarframework',
//   },
//   {
//     %title: 'Discord Chat Channel',
//     caption: 'chat.quasar.dev',
//     icon: 'chat',
//     link: 'https://chat.quasar.dev',
//   },
//   {
//     title: 'Forum',
//     caption: 'forum.quasar.dev',
//     icon: 'record_voice_over',
//     link: 'https://forum.quasar.dev',
//   },
//   {
//     title: 'Twitter',
//     caption: '@quasarframework',
//     icon: 'rss_feed',
//     link: 'https://twitter.quasar.dev',
//   },
//   {
//     title: 'Facebook',
//     caption: '@QuasarFramework',
//     icon: 'public',
//     link: 'https://facebook.quasar.dev',
//   },
//   {
//     title: 'Quasar Awesome',
//     caption: 'Community Quasar projects',
//     icon: 'favorite',
//     link: 'https://awesome.quasar.dev',
//   },
// ]

const $q = useQuasar()
const leftDrawerOpen = ref(false)
const router = useRouter()
const drawerWidth = computed(() => ($q.screen.lt.md ? 260 : 320))
const pendingSectionId = ref(null)
const whatsAppUrl = computed(() => {
  // TODO: add your phone number (e.g., 31612345678) and optional text
  const phone = '+31628208457'
  const text = encodeURIComponent('Hallo! Ik heb een vraag over een taxirit.')
  return phone ? `https://wa.me/${phone}?text=${text}` : 'https://wa.me/'
})

function goToSection(id) {
  const performScroll = () => {
    nextTick(() => {
      setTimeout(() => smoothScrollTo(id), 0)
    })
  }
  if (router.currentRoute.value.path !== '/') {
    router.push('/').then(performScroll)
  } else {
    performScroll()
  }
}

function navigateAndClose(id) {
  pendingSectionId.value = id
  leftDrawerOpen.value = false
}

function smoothScrollTo(id) {
  const el = document.getElementById(id)
  if (!el) return
  el.scrollIntoView({ behavior: 'smooth', block: 'start' })
}

// Drawer width is responsive via $q.screen

function onDrawerHide() {
  const id = pendingSectionId.value
  if (!id) return
  pendingSectionId.value = null
  const performScroll = () => {
    nextTick(() => setTimeout(() => smoothScrollTo(id), 0))
  }
  if (router.currentRoute.value.path !== '/') {
    router.push('/').then(performScroll)
  } else {
    performScroll()
  }
}
</script>

<style scoped>
.container {
  width: 77%;
  margin: 0 auto;
}
@media (max-width: 1024px) {
  .container {
    width: 85%;
  }
}
@media (max-width: 768px) {
  .container {
    width: 90%;
    padding: 0 1rem;
  }
}

.header-container {
  position: relative;
  background: rgb(0, 0, 0);
  backdrop-filter: saturate(180%) blur(8px);
}

.header-brand {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 6px 12px;
  width: 100px;
  height: auto;
  max-height: 50px;
  margin-top: 10px;
  border-radius: 14px;
  /* background: linear-gradient(135deg, #000000 0%, #7e7e7e 100%); */
  /* box-shadow: 0 10px 24px rgba(0,0,0,0.25); */
}
.drawer-brand-logo {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 6px 12px;
  width: 100px;
  height: auto;
  max-height: 50px;
  margin-top: 10px;
  border-radius: 14px;
  /* background: linear-gradient(135deg, #000000 0%, #7e7e7e 100%); */
}

.header-brand img {
  width: fit-content;
  height: fit-content;
  max-height: 122px;
  /* filter: drop-shadow(0 1px 1.5px rgba(0, 0, 0, 0.25)); */
}

.header-tagline {
  margin-left: 1rem;
  color: #111827;
  font-weight: 600;
}
.header-actions {
  margin-left: auto;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
}
.header-container > .container {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

/* .header-actions has no specific styles */
.header-nav :deep(.q-btn) {
  color: #111827;
}

.header-nav {
  background: rgb(255, 255, 255);
  border: 1px solid rgba(17, 24, 39, 0.08);
  border-radius: 9999px;
  padding: 4px;
  display: flex;
  text-transform: uppercase !important;
  align-items: center;
  gap: 2px;
  box-shadow: 0 8px 18px rgba(0, 0, 0, 0.06);
  backdrop-filter: saturate(160%) blur(6px);
}
.nav-btn :deep(.q-btn__content) {
  font-weight: 700;
}

.nav-btn:hover {
  background: rgb(0, 0, 0) !important;
  color: #fbc02c !important;
  padding: 4px 13px 4px 9px;
  border-radius: 18px;
}
.whatsapp-btn {
  color: #fbc02c !important;
  background: black;
  border-radius: 18px;
  padding: 4px 13px 4px 9px;
}



/* Drawer styling */
.app-drawer {
  background: rgba(255, 255, 255, 0.5);
  -webkit-backdrop-filter: blur(18px) saturate(160%);
  backdrop-filter: blur(18px) saturate(160%);
  border-right: 1px solid rgba(255, 255, 255, 0.35);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.18);
  background-clip: padding-box;
  overflow: hidden;
}
/* Blur the page under the overlay backdrop when drawer is open */
:deep(.q-drawer__backdrop) {
  backdrop-filter: blur(6px);
  background: rgba(0, 0, 0, 0.08);
}
.drawer-header {
  display: flex;
  align-items: center;
  justify-content: space-around;
  padding: 12px 12px 8px 12px;
}
.drawer-brand {
  display: inline-flex;
  align-items: center;
  gap: 10px;
}
.drawer-brand img {
  width: fit-content;
  height: fit-content;
  max-height: 122px;
}
.drawer-brand-text {
  display: flex;
  flex-direction: column;
  line-height: 1.1;
}
.drawer-title {
  font-weight: 800;
  color: #fbc02c;
}
.drawer-subtitle {
  font-size: 12px;
  color: #ffffff;
}
.drawer-list :deep(.q-item) {
  border-radius: 10px;
  margin: 6px 8px;
  padding: 6px 8px;
  color: #ffffff;
  min-height: 56px;
}
.drawer-list :deep(.q-item:hover) {
  /* background: rgba(17, 24, 39, 0.06);
  color: #fbc02c; */
}
.drawer-list :deep(.q-item__section--avatar) {
  min-width: 40px;
  display: flex;
  align-items: center;
  justify-content: center;
}

@media (max-width: 480px) {
  .container {
    width: 100%;
    padding: 0 1rem;
  }
}

.app-footer {
  background: #f6f6f6;
  border-radius: 10px;
  box-shadow: 0 3px 6px #00000029, 0 3px 6px #0000003b;
  color: #000000;
  padding-top: 1.25rem;
  padding-bottom: 1rem;
}
.footer-top {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1rem;
}
.footer-brand {
  position: relative;
  background: linear-gradient(135deg, #000000 0%, #000000 100%);
  border-radius: 14px;
  box-shadow: 0 10px 24px rgba(103, 40, 161, 0.25);
  overflow: hidden;
  width: auto;
  height: auto;
  max-height: 80px;
  display: flex;
  align-items: center;
  justify-content: center;
}
/* .footer-brand::after {
  content: "";
  position: absolute;
  top: -60%;
  left: -20%;
  width: 160%;
  height: 220%;
  background: radial-gradient(50% 50% at 70% 30%, rgba(255, 214, 79, 0.28) 0%, rgba(255, 214, 79, 0) 60%);
  transform: rotate(12deg);
} */
.footer-logo {
  width: fit-content;
  height: fit-content;
  max-height: 122px;

  object-fit: contain;
  object-position: center;
  display: block;
  filter: drop-shadow(0 1px 1.5px rgba(0, 0, 0, 0.25));
}
.footer-social :deep(.q-btn) {
  margin-left: 0.25rem;
  margin-right: 0.25rem;
}
.footer-separator {
  height: 1px;
  width: 100%;
  background: #e8e8e8;
  margin: 1rem 0;
}
.footer-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 0.75rem;
}
.footer-copy {
  color: #000000;
}
.footer-links {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
.footer-link {
  color: #000000;
  text-decoration: none;
}
.footer-link:hover {
  color: #000000;
}
.dot {
  color: rgba(0, 0, 0, 0.45);
}

@media (max-width: 1024px) {
  .container {
    width: 85%;
  }
}
@media (max-width: 768px) {
  .container {
    width: 90%;
    padding: 0 1rem;
  }
  .header-nav {
    display: none;
  }
  .footer-brand {
    width: 132px;
    height: 66px;
  }
}
@media (max-width: 480px) {
  .container {
    width: 100%;
    padding: 0 1rem;
  }
}
</style>