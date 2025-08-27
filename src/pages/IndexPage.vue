<template>
  <q-page class="flex flex-center" style="flex-direction: column;">
    <section class="hero" id="calculate" style="scroll-margin-top: 92px">
      <div class="hero-inner container" style="scroll-margin-top: 84px">
        <div class="booking-card">
          <div class="booking-title">Een taxi bestellen in heel Nederland</div>
          <div class="booking-grid">
            <div class="booking-left">
              <div class="route-icons">
                <q-icon name="radio_button_unchecked" size="18px" />

                <div v-if="stopovers.length > 0" class="route-line" />
                <q-icon
                  v-for="i in stopovers.length"
                  :key="i"
                  name="add"
                  size="18px"
                  class="add-dot"
                />
                <div class="route-line" />
                <q-icon name="place" size="20px" />
              </div>
              <div class="route-inputs">
                <q-input
                  ref="fromInputRef"
                  v-model="fromAddress"
                  standout="bg-white text-black"
                  dense
                  placeholder="van adres"
                  input-class="input-text"
                  class="route-input"
                />
                <q-input
                  v-for="(st, i) in stopovers"
                  :key="`st-${i}`"
                  :ref="(el) => assignStopoverRef(i, el)"
                  v-model="stopovers[i]"
                  standout="bg-white text-black"
                  dense
                  placeholder="stopover adres"
                  input-class="input-text"
                  class="route-input stopover-input"
                >
                  <template #append>
                    <q-btn
                      flat
                      round
                      dense
                      icon="close"
                      color="grey-7"
                      @click="removeStopover(i)"
                    />
                  </template>
                </q-input>
                <div class="between-row">
                  <q-btn
                    flat
                    round
                    size="sm"
                    color="black"
                    icon="swap_vert"
                    @click="swapAddresses"
                  />
                  <div class="stopover-toggle" @click="addStopover">
                    <q-icon name="add" size="18px" class="q-mr-xs" /> add stopover
                  </div>
                </div>
                <q-input
                  ref="toInputRef"
                  v-model="toAddress"
                  standout="bg-white text-black"
                  dense
                  placeholder="naar adres"
                  input-class="input-text"
                  class="route-input"
                />

                <div class="luggage-group">
                  <div class="lg-title">Heb je bagage?</div>
                  <div class="lg-options">
                    <q-radio v-model="hasLuggage" val="yes" label="Yes" color="black" />
                    <q-radio v-model="hasLuggage" val="no" label="No" color="black" />
                  </div>
                </div>
              </div>
            </div>
            <q-separator vertical inset class="booking-divider" />
            <div class="booking-right">
              <div class="picker">
                <q-input
                  dense
                  standout="bg-white text-black"
                  :model-value="formattedPickupDateTime"
                  placeholder="Selecteer datum en tijd"
                  readonly
                >
                  <template #prepend>
                    <q-icon name="event" />
                  </template>
                  <q-popup-proxy cover transition-show="scale" transition-hide="scale">
                    <div class="date-time-popup">
                      <q-date v-model="pickupDate" minimal />
                      <q-time v-model="pickupTime" format24h />
                    </div>
                  </q-popup-proxy>
                </q-input>
              </div>
              <div class="return-row">
                <q-checkbox v-model="isReturn" label="Terugreis" color="black" />
              </div>
              <div class="picker">
                <q-input
                  :disable="!isReturn"
                  dense
                  standout="bg-white text-black"
                  :model-value="tripTypeLabel"
                  placeholder="Terugreis datum en tijd"
                  readonly
                >
                  <template #prepend>
                    <q-icon name="event_repeat" />
                  </template>
                  <q-popup-proxy
                    v-if="isReturn"
                    cover
                    transition-show="scale"
                    transition-hide="scale"
                  >
                    <div class="date-time-popup">
                      <q-date v-model="returnDate" minimal />
                      <q-time v-model="returnTime" format24h />
                    </div>
                  </q-popup-proxy>
                </q-input>
              </div>
              <div class="travelers">
                <div class="tr-title">Aantal reizigers</div>
                <div class="tr-counter">
                  <q-btn flat round icon="remove" color="grey-8" @click="decrementTravelers" />
                  <div class="tr-value">{{ travelers }}</div>
                  <q-btn flat round icon="add" color="black" @click="incrementTravelers" />
                </div>
              </div>
            </div>
          </div>
          <div class="booking-actions">
            <q-btn
              color="yellow-8"
              unelevated
              class="cta-btn"
              label="Bereken je ritprijs"
              @click="calculateRidePrice"
              :loading="isCalculating"
            />
          </div>
        </div>
        <div class="hero-copy">
          <div class="hero-score">
            Beoordeeld met <b>9.3</b> — de hoogste klanttevredenheid in Nederland
          </div>
     
          <div class="hero-headline">Waar je ook heen gaat, we brengen je er veilig.</div>
          <div class="hero-sub">
            Kies uit standaard, luxe, elektrisch of taxibus tot acht personen.
          </div>
        </div>
      </div>
      <div class="usp-inner">
        <div class="usp-item-container">
          <div class="usp-item">
            <q-icon
              name="check_circle"
              color="yellow-8"
              size="20px"
              style="color: #fbc02c !important"
            />
            <span>Gratis annuleren en wijzigen</span>
          </div>
          <div class="usp-item">
            <q-icon
              name="check_circle"
              color="black"
              size="20px"
              style="color: #fbc02c !important"
            />
            <span>Altijd een privé-taxi</span>
          </div>
          <div class="usp-item">
            <q-icon
              name="check_circle"
              color="black"
              size="20px"
              style="color: #fbc02c !important"
            />
            <span>Gratis bagage mee</span>
          </div>
          <div class="usp-item">
            <q-icon
              name="check_circle"
              color="black"
              size="20px"
              style="color: #fbc02c !important"
            />
            <span>Online betalen</span>
          </div>
        </div>
      </div>
    </section>

    <!-- Price / Trip Details Dialog -->
    <q-dialog
      v-model="isPriceDialogOpen"
      persistent
      transition-show="slide-up"
      transition-hide="slide-down"
    >
      <q-card class="price-dialog-card">
        <q-card-section class="row items-center q-pb-none">
          <img src="~assets/allwayspng.png" alt="Allways Taxi" width="30px"   class="price-dialog-logo" style="background: #000000 !important; border-radius: 10px; margin-right: 10px;" />
          <div class="text-h6" style="font-weight: 700;">Ritoverzicht</div>

          <q-space />
          <q-btn icon="close" flat round dense v-close-popup />
        </q-card-section>

        <q-separator inset />

        <q-card-section>
          <div v-if="isDialogLoading" class="dialog-loading">
            <q-spinner-dots size="40px" color="deep-purple" />
            <div class="loading-text">Berekenen van je ritprijs...</div>
          </div>
          <div v-else class="trip-details">
            <div class="detail-row">
              <div class="detail-label">Van</div>
              <div class="detail-value">{{ fromAddress }}</div>
            </div>
            <div class="detail-row">
              <div class="detail-label">Naar</div>
              <div class="detail-value">{{ toAddress }}</div>
            </div>
            <div class="detail-grid">
              <div class="detail-metric">
                <div class="metric-label">Afstand</div>
                <div class="metric-value">{{ resultDistanceKm.toFixed(1) }} km</div>
              </div>
              <div class="detail-metric">
                <div class="metric-label">Duur</div>
                <div class="metric-value">{{ resultDurationText }}</div>
              </div>
              <div class="detail-metric">
                <div class="metric-label">Prijs</div>
                <div class="metric-value price">{{ formattedPrice }}</div>
              </div>
            </div>
            <div class="extra-row" v-if="isReturn">
              <q-icon name="sync" size="18px" color="black" class="q-mr-sm" /> Terugreis selected
            </div>
            <div class="extra-row">
              <q-icon name="work" size="18px" color="black" class="q-mr-sm" style="color: #fbc02c !important" /> Bagage:
              {{ hasLuggage === 'yes' ? 'Ja' : 'Nee' }}
            </div>
            <div class="extra-row">
              <q-icon name="groups" size="18px" color="black" class="q-mr-sm" style="color: #fbc02c !important" /> Reizigers:
              {{ travelers }}
            </div>
          </div>
        </q-card-section>

        <q-separator />

        <q-card-actions align="right" class="q-pa-md card-actions-btns">
          <q-btn flat label="Annuleren" color="black" v-close-popup />
          <q-btn
            :href="whatsAppLink"
            target="_blank"
            color="black"
            unelevated
            icon="fa-brands fa-whatsapp"
            label="BOEK NU"
            :disable="isDialogLoading"
          />
        </q-card-actions>
      </q-card>
    </q-dialog>
    <div class="features">
      <div class="features-list">
        <div class="feature-card" v-for="ft in features" :key="ft.title">
          <div class="feature-icon">
            <q-icon :name="ft.icon" color="yellow" size="42px" style="color: #fbc02c !important" />
          </div>
          <div class="feature-content">
            <div class="feature-title">
              <q-icon name="check" color="black" size="18px" class="feature-check" />
              {{ ft.title }}
            </div>
            <div class="feature-desc">{{ ft.description }}</div>
          </div>
        </div>
      </div>
    </div>

    <div v-if="isSplashVisible" class="splash-overlay">
      <img src="~assets/allwayspng.png" alt="Allways Taxi" class="splash-logo" />
      <q-spinner-dots size="48px" color="amber" />
    </div>
    <div class="description">
      <div class="image">
        <img src="~assets/taxi/taxi1.jpeg" alt="Allways Taxi" />
      </div>
      <div class="text">
        <h1 style="color: #000000; font-size: 2.25rem; font-weight: 400; margin-bottom: 2rem">
          Een taxi bestellen in heel Nederland
        </h1>
        <p>
          Met Allways Taxi reis je gemakkelijk van en naar iedere locatie in Nederland, en ook naar
          luchthavens in Duitsland en België. Ons platform brengt dagelijks reizigers in contact met
          betrouwbare taxichauffeurs die staan voor kwaliteit, veiligheid en service.
        </p>
        <p style="">
          Via het online boekingsplatform van Allways Taxi reserveer je snel en eenvoudig een taxi
          naar luchthavens, hotels, zakelijke afspraken, zorginstellingen, festivals of een dagje
          uit. Je hebt de keuze uit standaard taxi’s, luxe vervoer, elektrisch vervoer of een
          taxibus tot acht personen.
        </p>
        <p>
          Je reist altijd in een privé-taxi met een ervaren chauffeur. Bagage neem je gratis mee, je
          kunt je rit eenvoudig aanpassen en tot drie uur voor vertrek gratis annuleren. Zo weet je
          zeker dat je zorgeloos en comfortabel onderweg bent.
        </p>
        <p>Tot snel bij Allways Taxi!</p>
        <div class="">
            <q-btn
              unelevated
              :href="whatsAppUrl2"
              class="cta-btn"
              label="BOEK NU"
              :loading="isCalculating"
              target="_blank"
            color="black"
            icon="fa-brands fa-whatsapp"
            />
          </div>
      </div>
    </div>

    <div class="services" id="services" style="scroll-margin-top: 84px">
      <div class="header">Onze diensten</div>
      <div class="services-list">
        <div class="service" v-for="svc in services" :key="svc.title">
          <div class="service-image">
            <img :src="svc.image" :alt="svc.title" />
          </div>
          <h3 class="service-title">{{ svc.title }}</h3>
          <p class="service-desc">{{ svc.description }}</p>
        </div>
      </div>
    </div>

    <div class="reviews" id="reviews" style="scroll-margin-top: 84px">
      <div class="description " style="width: 77%; margin: 0 auto;padding: 32px 0px;">
      <div class="image">
        <img src="~assets/taxi/taxi9.jpeg" alt="Allways Taxi" />
      </div>
      <div class="text">
        <h1 style="color: #000000; font-size: 2.25rem; font-weight: 600; margin-bottom: 2rem">
          Altijd en overal een taxi
        </h1>
        <p>
          Met Allways Taxi ben je verzekerd van betrouwbaar vervoer in heel Nederland én naar
          luchthavens in Duitsland en België. Onze ervaren chauffeurs zorgen dat je veilig,
          comfortabel en op tijd aankomt.
        </p>
        <p>
          Of het nu gaat om een korte rit in de stad, een zakelijke afspraak, zorgvervoer of een
          dagje uit – bij Allways Taxi regel je het eenvoudig online. Geen zorgen, geen gedoe,
          gewoon zorgeloos onderweg.
        </p>
      </div>
    </div>
      <div class="reviews-cta">
        <q-btn
          :href="whatsAppUrl2"
          target="_blank"
          icon="fa-brands fa-whatsapp"
          unelevated
          no-caps
          label="BOEK NU"
        />
        <!-- <div class="wa-sub">We reageren /snel — 24/7 bereikbaar</div> -->
      </div>
    </div>
  </q-page>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount, watch, nextTick } from 'vue'
import { useQuasar } from 'quasar'
import taxi_1 from 'assets/taxi/taxi1.jpeg'
import taxi_2 from 'assets/taxi/taxi2.jpeg'
import taxi_3 from 'assets/taxi/taxi3.jpeg'
import taxi_4 from 'assets/taxi/taxi4.jpeg'
import taxi_5 from 'assets/taxi/taxi5.jpeg'
import taxi_6 from 'assets/taxi/taxi6.jpeg'

const $q = useQuasar()
const isSplashVisible = ref(true)

// Simple splash hide after 2s and wire up 3D tilt listeners for service images
let imageContainers = []
function handleImageMove(e) {
  const container = e.currentTarget
  const rect = container.getBoundingClientRect()
  const px = (e.clientX - rect.left) / rect.width
  const py = (e.clientY - rect.top) / rect.height
  const rotateY = (px - 0.5) * 16
  const rotateX = (0.5 - py) * 16
  const img = container.querySelector('img')
  if (img) {
    img.style.transform = `rotateX(${rotateX}deg) rotateY(${rotateY}deg) scale(1.05)`
  }
}
function handleImageLeave(e) {
  const img = e.currentTarget.querySelector('img')
  if (img) {
    img.style.transform = 'rotateX(0deg) rotateY(0deg) scale(1)'
  }
}

onMounted(() => {
  setTimeout(() => {
    isSplashVisible.value = false
  }, 2000)

  imageContainers = Array.from(document.querySelectorAll('.service-image'))
  imageContainers.forEach((el) => {
    el.addEventListener('mousemove', handleImageMove)
    el.addEventListener('mouseleave', handleImageLeave)
  })

  loadGoogleMaps()
  setupAutocomplete()
})

onBeforeUnmount(() => {
  imageContainers.forEach((el) => {
    el.removeEventListener('mousemove', handleImageMove)
    el.removeEventListener('mouseleave', handleImageLeave)
  })
  imageContainers = []
})

const services = [
  {
    title: 'Lokaal Taxivervoer',
    description:
      'Met Allways Taxi reis je zorgeloos van en naar iedere locatie in Nederland. Of je nu direct een taxi nodig hebt of je rit liever van tevoren reserveert – wij staan altijd voor je klaar. Voor al je ritten van A naar B ben je bij ons aan het juiste adres!.',
    image: taxi_1,
  },
  {
    title: 'Luchthaven taxi',
    description:
      'Met Allways Taxi boek je eenvoudig vervoer van en naar alle luchthavens in Nederland, Duitsland en België. Geef je vluchtnummer door en wij volgen je vlucht nauwkeurig. Zo staan we altijd op tijd voor je klaar – ook bij vertraging.',
    image: taxi_2,
  },
  {
    title: 'Zorgvervoer',
    description:
      'Allways Taxi biedt betrouwbaar zorgvervoer op maat voor iedereen die niet zelfstandig kan reizen. Wij zorgen voor comfortabel vervoer van en naar ziekenhuizen, verpleeghuizen, revalidatiecentra, dagbesteding en alle andere locaties waar (medische) zorg nodig is.',
    image: taxi_3,
  },
  {
    title: 'Zakelijk vervoer',
    description:
      'Voor al je zakelijke ritten is Allways Taxi de betrouwbare vervoerspartner in Nederland. Via ons boekingsplatform regel je eenvoudig al het benodigde zakelijk vervoer. Met een zakelijk account boek je snel en overzichtelijk ritten op rekening – ideaal voor bedrijven en organisaties die gemak en professionaliteit waarderen.',
    image: taxi_4,
  },
  {
    title: 'Rides naar populaire locaties',
    description:
      'Met Allways Taxi reis je zorgeloos van en naar de leukste bestemmingen. Of je nu een dagje uit hebt gepland naar een pretpark, dierentuin, theater of stadion – wij brengen je ontspannen en veilig naar je bestemming én weer terug.',
    image: taxi_5,
  },
  {
    title: 'Evenementenvervoer',
    description:
      'Ga je naar een feest, festival of ander evenement? Met Allways Taxi geniet je onbezorgd van je dag of avond. Niemand hoeft de bob te zijn: wij brengen je comfortabel naar het evenement en zorgen er na afloop voor dat je weer veilig thuis komt.',
    image: taxi_6,
  },
]

// const reviews = [
//   {
//     title: 'Top',
//     description:
//       'Zondagmorgen, in het midden van niets... Een taxi snel en zonder problemen met een super vriendelijke en zeer professionele chauffeur!',
//     rating: 10,
//     name: 'Tijmen',
//     city: 'Culemborg',
//   },
//   {
//     title: 'Uitstekend',
//     description:
//       'Beide ritten naar en van onze bestemming hadden uitstekende chauffeurs, die op tijd waren en in een frisse en mooie elektrische auto reden. Dit waren onze eerste taxi-ritjes ooit en ze waren zeker waard om nog eens te herhalen. Hoe geweldig ontspannend!',
//     rating: 9,
//     name: 'Ruud and Marja',
//     city: 'Heiloo',
//   },
//   {
//     title: 'Gedachtzame en vriendelijke chauffeur',
//     description:
//       'Contacteerd via telefoon en WhatsApp, vond de taxi snel via live locatie. De chauffeur was aardig en we hadden een prettige rit!',
//     rating: 10,
//     name: 'Gaby',
//     city: 'Spijkenisse',
//   },
// ]

// Hero booking form state
const fromAddress = ref('')
const toAddress = ref('')
const fromInputRef = ref(null)
const toInputRef = ref(null)
const stopovers = ref([])
const stopoverInputRefs = ref([])
const pickupDate = ref(null)
const pickupTime = ref(null)
const returnDate = ref(null)
const returnTime = ref(null)
const isReturn = ref(false)
const hasLuggage = ref('yes')
const travelers = ref(1)
const isCalculating = ref(false)
const calculatedPrice = ref(null)
const isPriceDialogOpen = ref(false)
const isDialogLoading = ref(false)
const resultDistanceKm = ref(0)
const resultDurationText = ref('')

const formattedPickupDateTime = computed(() => {
  const d = pickupDate.value
  const t = pickupTime.value
  if (!d && !t) return ''
  if (d && t) return `${d} ${t}`
  return d || t || ''
})

const tripTypeLabel = computed(() => (isReturn.value ? 'Return trip' : 'Single trip'))

// const formattedReturnDateTime = computed(() => {
//   const d = returnDate.value
//   const t = returnTime.value
//   if (!d && !t) return ''
//   if (d && t) return `${d} ${t}`
//   return d || t || ''
// })

function incrementTravelers() {
  travelers.value += 1
}
function decrementTravelers() {
  if (travelers.value === 1) return
  travelers.value = Math.max(1, travelers.value - 1)
}

function swapAddresses() {
  const temp = fromAddress.value
  fromAddress.value = toAddress.value
  toAddress.value = temp
}

function assignStopoverRef(index, el) {
  stopoverInputRefs.value[index] = el
}
function addStopover() {
  stopovers.value.push('')
  nextTick(() => initStopoverAutocomplete(stopovers.value.length - 1))
}
function removeStopover(index) {
  stopovers.value.splice(index, 1)
  stopoverInputRefs.value.splice(index, 1)
}

const START_TARIEF_EUR = 4.15
const PRICE_PER_MIN_EUR = 0.5
const PRICE_PER_KM_EUR = 3.05
const GOOGLE_MAPS_API_KEY = 'AIzaSyDWOI0SRQIV6R3VfPjU81N54yVLEj295DI'

async function calculateRidePrice() {
  if (!fromAddress.value || !toAddress.value) {
    isPriceDialogOpen.value = true
    isDialogLoading.value = false
    $q.notify &&
      $q.notify({
        type: 'negative',
        message: 'Please enter both pickup and destination addresses.',
      })
    return
  }
  isCalculating.value = true
  isPriceDialogOpen.value = true
  isDialogLoading.value = true
  try {
    const points = [
      fromAddress.value,
      ...stopovers.value.filter((s) => s && s.trim().length > 0),
      toAddress.value,
    ]
    if (points.length < 2) {
      throw new Error('Please provide valid origin and destination.')
    }

    let dirRes
    try {
      dirRes = await fetchDirectionsViaSDK(points)
    } catch {
      dirRes = await fetchDirectionsRESTForPoints(points)
    }
    const totalDistanceMeters = dirRes.totalDistanceMeters
    const totalDurationSeconds = dirRes.totalDurationSeconds
    const distanceKm = totalDistanceMeters / 1000
    const durationMinutes = totalDurationSeconds / 60

    resultDurationText.value = formatDuration(totalDurationSeconds)
    let multiplier = 1
    let multiplier2 = 1
    if (isReturn.value) {
      multiplier = 2 // simple return pricing: double distance price
    }
    if (travelers.value > 1) {
      multiplier2 = travelers.value
    }
    const tripPriceSingle =
      START_TARIEF_EUR + Math.max(0, distanceKm) * PRICE_PER_KM_EUR + Math.max(0, durationMinutes) * PRICE_PER_MIN_EUR
    const price = Math.round(tripPriceSingle * multiplier * multiplier2 * 100) / 100
    calculatedPrice.value = price
    resultDistanceKm.value = distanceKm
    isDialogLoading.value = false
  } catch (err) {
    isDialogLoading.value = false
    $q.notify &&
      $q.notify({ type: 'negative', message: err?.message || 'Price calculation failed.' })
  } finally {
    isCalculating.value = false
  }
}

const formattedPrice = computed(() => {
  if (calculatedPrice.value == null) return ''
  return new Intl.NumberFormat('nl-NL', { style: 'currency', currency: 'EUR' }).format(
    calculatedPrice.value
  )
})

const whatsAppLink = computed(() => {
  const base = 'https://wa.me/'
  const phone = '31628208457'
  const validStopovers = stopovers.value.filter((s) => s && s.trim().length > 0)
  const lines = [
    'Taxi booking request',
    `From: ${fromAddress.value || '-'}`,
    validStopovers.length ? `Stopovers: ${validStopovers.join(' | ')}` : null,
    `To: ${toAddress.value || '-'}`,
    `Pickup: ${formattedPickupDateTime.value || '-'}`,
    isReturn.value ? `Return: ${returnDate.value || ''} ${returnTime.value || ''}` : null,
    `Trip type: ${isReturn.value ? 'Return trip' : 'Single trip'}`,
    `Distance: ${resultDistanceKm.value ? resultDistanceKm.value.toFixed(1) + ' km' : '-'}`,
    `Duration: ${resultDurationText.value || '-'}`,
    `Luggage: ${hasLuggage.value === 'yes' ? 'Yes' : 'No'}`,
    `Travelers: ${travelers.value}`,
    `Price: ${formattedPrice.value || '-'}`,
  ].filter(Boolean)
  const text = encodeURIComponent(lines.join('\n'))
  return `${base}${phone}?text=${text}`
})

const whatsAppUrl2 = computed(() => {
  // TODO: add your phone number (e.g., 31612345678) and optional text
  const phone = '+31628208457'
  const text = encodeURIComponent('Hallo! Ik heb een vraag over een taxirit.')
  return phone ? `https://wa.me/${phone}?text=${text}` : 'https://wa.me/'
})

function loadGoogleMaps() {
  if (window.google && window.google.maps && window.google.maps.places) return
  const src = `https://maps.googleapis.com/maps/api/js?key=${GOOGLE_MAPS_API_KEY}&libraries=places&language=en&region=NL`
  const s = document.createElement('script')
  s.src = src
  s.async = true
  document.head.appendChild(s)
}

function setupAutocomplete() {
  const tryInit = () => {
    if (!(window.google && window.google.maps && window.google.maps.places)) {
      setTimeout(tryInit, 300)
      return
    }
    const options = { componentRestrictions: { country: 'nl' }, fields: ['formatted_address'] }
    if (fromInputRef.value?.$el) {
      const inputEl = fromInputRef.value.$el.querySelector('input')
      const ac = new window.google.maps.places.Autocomplete(inputEl, options)
      ac.addListener('place_changed', () => {
        const place = ac.getPlace()
        fromAddress.value = place?.formatted_address || fromAddress.value
      })
    }
    if (toInputRef.value?.$el) {
      const inputEl = toInputRef.value.$el.querySelector('input')
      const ac = new window.google.maps.places.Autocomplete(inputEl, options)
      ac.addListener('place_changed', () => {
        const place = ac.getPlace()
        toAddress.value = place?.formatted_address || toAddress.value
      })
    }
    stopoverInputRefs.value.forEach((refEl, idx) => {
      if (refEl?.$el) {
        const inputEl = refEl.$el.querySelector('input')
        if (!inputEl) return
        initAutocompleteInstance(inputEl, options, idx)
      }
    })
  }
  tryInit()
}

function initAutocompleteInstance(inputEl, options, index) {
  const ac = new window.google.maps.places.Autocomplete(inputEl, options)
  ac.addListener('place_changed', () => {
    const place = ac.getPlace()
    const value = place?.formatted_address || stopovers.value[index]
    stopovers.value[index] = value
  })
}

function initStopoverAutocomplete(index) {
  if (!(window.google && window.google.maps && window.google.maps.places)) return
  const options = { componentRestrictions: { country: 'nl' }, fields: ['formatted_address'] }
  const refEl = stopoverInputRefs.value[index]
  if (refEl?.$el) {
    const inputEl = refEl.$el.querySelector('input')
    if (inputEl) initAutocompleteInstance(inputEl, options, index)
  }
}

// Call Google Directions via Maps JavaScript SDK (avoids CORS in browser)
async function fetchDirectionsViaSDK(points) {
  await new Promise((resolve) => {
    if (window.google && window.google.maps) {
      resolve()
      return
    }
    const check = () => {
      if (window.google && window.google.maps) {
        resolve()
        return
      }
      setTimeout(check, 200)
    }
    check()
  })

  const origin = points[0]
  const destination = points[points.length - 1]
  const middle = points.slice(1, -1)
  const svc = new window.google.maps.DirectionsService()
  const res = await new Promise((resolve, reject) => {
    svc.route(
      {
        origin,
        destination,
        waypoints: middle.map((loc) => ({ location: loc, stopover: true })),
        travelMode: window.google.maps.TravelMode.DRIVING,
        unitSystem: window.google.maps.UnitSystem.METRIC,
        drivingOptions: {
          departureTime: new Date(),
          trafficModel: 'bestguess',
        },
        provideRouteAlternatives: false,
      },
      (response, status) => {
        if (status === 'OK' && response) resolve(response)
        else reject(new Error(status || 'Directions SDK failed'))
      }
    )
  })

  const route = res.routes?.[0]
  const legs = route?.legs || []
  let totalDistanceMeters = 0
  let totalDurationSeconds = 0
  for (const leg of legs) {
    totalDistanceMeters += leg?.distance?.value || 0
    const dur = (leg?.duration_in_traffic?.value ?? leg?.duration?.value) || 0
    totalDurationSeconds += dur
  }
  return { totalDistanceMeters, totalDurationSeconds }
}

// Call Google Directions API to compute total distance and duration (with traffic)
async function fetchDirectionsRESTForPoints(points) {
  const origin = points[0]
  const destination = points[points.length - 1]
  const middle = points.slice(1, -1)
  const encode = (v) => encodeURIComponent(v)
  const originParam = encode(origin)
  const destinationParam = encode(destination)
  const waypointsParam = middle.length > 0 ? `&waypoints=${middle.map(encode).join('%7C')}` : ''
  const url = `https://maps.googleapis.com/maps/api/directions/json?origin=${originParam}&destination=${destinationParam}${waypointsParam}&mode=driving&units=metric&language=en&departure_time=now&traffic_model=best_guess&key=${GOOGLE_MAPS_API_KEY}`

  const resp = await fetch(url)
  if (!resp.ok) {
    throw new Error(`HTTP ${resp.status}`)
  }
  const data = await resp.json()
  if ((data.status || 'OK') !== 'OK') {
    throw new Error(data.error_message || data.status || 'Directions API failed')
  }

  const route = data.routes?.[0]
  const legs = route?.legs || []
  let totalDistanceMeters = 0
  let totalDurationSeconds = 0
  for (const leg of legs) {
    totalDistanceMeters += leg?.distance?.value || 0
    const dur = (leg?.duration_in_traffic?.value ?? leg?.duration?.value) || 0
    totalDurationSeconds += dur
  }

  return { totalDistanceMeters, totalDurationSeconds }
}

function formatDuration(totalSeconds) {
  const s = Math.max(0, Math.round(totalSeconds))
  const hours = Math.floor(s / 3600)
  const minutes = Math.round((s % 3600) / 60)
  if (hours > 0) return `${hours} uur ${minutes} min`
  return `${minutes} min`
}

const features = [
  {
    title: 'Competitieve prijzen',
    description: 'Tot 70% goedkoper dan de meterprijs. Vaste prijzen zonder verrassingen.',
    icon: 'percent',
  },
  {
    title: 'Overal in Nederland',
    description:
      'Reis naar en vanuit elk adres in Nederland, en vliegvelden in België en Duitsland.',
    icon: 'map',
  },
  {
    title: 'Betrouwbaar',
    description: 'We houden onze afspraken. De chauffeur is op tijd klaar.',
    icon: 'schedule',
  },
]

watch(isReturn, (val) => {
  if (!val) {
    returnDate.value = null
    returnTime.value = null
  }
})
</script>

<style scoped>
.splash-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: #000000;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 1rem;
  z-index: 9999;
}

.hero {
  width: 100vw;
  margin-left: calc(50% - 50vw);
  margin-right: calc(50% - 50vw);
  /* background: linear-gradient(165deg, #dbdbdb 0%, #dbdbdb 60%, #dbdbdb 100%); */
  background: linear-gradient(175deg, #000000 60%, #1b1818 0%);

  color: #000000;
  padding: 2.25rem 0 0 0;
}

.hero-inner {
  display: grid;
  grid-template-columns: 1.1fr 1fr;
  gap: 2rem;
  padding-bottom: 2.5rem;
  align-items: center;
  width: 77%;
  margin: 6% auto;
}
.hero-copy {
  display: grid;
  gap: 0.35rem;
  max-width: 640px;
  text-align: left;
}
.hero-score {
  color: #ffffff;
  font-size: 0.95rem;
}
.hero-headline {
  color: #fbc02c;
  font-size: clamp(1.5rem, 1.25vw + 1.25rem, 2.25rem);
  font-weight: 800;
  line-height: 1.25;
  letter-spacing: -0.01em;
  margin-top: 0.25rem;
}
.hero-sub {
  color: #ffffff;
  font-size: 1rem;
  line-height: 1.6rem;
}
.hero-rating {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  margin-top: 0.25rem;
}
.hero-link {
  color: #e5e7eb;
  text-decoration: none;
  border-bottom: 1px dashed rgba(229, 231, 235, 0.5);
  padding-bottom: 1px;
  transition: color 160ms ease, border-color 160ms ease;
}
.hero-link:hover {
  color: #fbc02c;
  border-bottom-color: #d8c311;
}
.booking-card {
  background: #ffffff;
  border-radius: 14px;
  padding: 1.25rem 1.25rem 1rem 1.25rem;
  color: #111827;
  box-shadow: 0 10px 24px rgba(0, 0, 0, 0.12);
  border: 1px solid rgba(17, 24, 39, 0.08);
}
.booking-title {
  font-size: clamp(1.25rem, 1.2vw + 0.75rem, 2rem);
  font-weight: 800;
  margin-bottom: 0.85rem;
  color: #111827;
}
.booking-grid {
  display: grid;
  grid-template-columns: 1fr 1px 1fr;
  gap: 1rem;
}
.booking-divider {
  background: rgba(0, 0, 0, 0.15);
}
.booking-left {
  display: grid;
  grid-template-columns: 24px 1fr;
  gap: 0.75rem;
}
.route-icons {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding-top: 6px;
}
.route-line {
  width: 2px;
  height: 40px; /* match q-input dense height */
  background: rgba(17, 24, 39, 0.18);
  border-radius: 2px;
}
.add-dot {
  background: #fbc02c;
  color: #fff;
  margin-bottom: 26px;
  border-radius: 50%;
  padding: 2px;
}
.route-inputs {
  display: flex;
  flex-direction: column;
  gap: 0.6rem;
}
.route-input :deep(.q-field__control) {
  min-height: 40px;
}
.stopover-input :deep(.q-field__control) {
  background: #f9fafb;
}
.route-input :deep(input.input-text) {
  font-size: 0.95rem;
}
.between-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  color: #060606;
  font-weight: 600;
}
.stopover-toggle {
  display: inline-flex;
  align-items: center;
  cursor: pointer;
  color: #111827;
}
.luggage-group {
  margin-top: 0.5rem;
}
.lg-title {
  font-weight: 700;
  margin-bottom: 0.35rem;
}
.lg-options {
  display: flex;
  gap: 1rem;
}
.booking-right {
  display: flex;
  flex-direction: column;
  gap: 0.6rem;
}
.picker :deep(.q-field__native) {
  cursor: pointer;
}
.date-time-popup {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 0.5rem;
  padding: 0.5rem;
}
.travelers {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.tr-title {
  font-weight: 700;
}
.tr-counter {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}
.tr-value {
  min-width: 24px;
  text-align: center;
}
.booking-actions {
  margin-top: 0.75rem;
  display: flex;
  justify-content: flex-end;
}
.cta-btn {
  border-radius: 12px;
  font-weight: 800;
  padding: 12px 20px;
  background: #fbc02c !important;
  color: #000000 !important;
  text-transform: capitalize;
}

/* Dialog styling */
.price-dialog-card {
  width: min(680px, 92vw);
  border-radius: 16px;
  overflow: hidden;
  border: 1px solid rgba(17, 24, 39, 0.08);
  box-shadow: 0 24px 48px rgba(0, 0, 0, 0.22);
}
.price-dialog-card :deep(.q-card__section) {
  padding: 18px 20px;
}
.dialog-loading {
  display: flex;
  align-items: center;
  gap: 12px;
  color: rgb(216, 195, 17) !important;

}
.trip-details {
  display: grid;
  grid-template-columns: 1fr;
  gap: 12px;
}
.detail-row {
  display: grid;
  grid-template-columns: 120px 1fr;
  gap: 12px;
}
.detail-label {
  color: #111827;
  font-weight: 700;
}
.detail-value {
  color: #2d2a2a;
}
.detail-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 12px;
  margin-top: 4px;
}
.detail-metric {
  background: #ffffff;
  border: 1px solid rgba(17, 24, 39, 0.08);
  border-radius: 12px;
  padding: 10px 12px;
}
.metric-label {
  color: #6b7280;
  font-weight: 600;
  font-size: 12px;
}
.metric-value {
  color: #2d2a2a;
  font-size: 18px;
}
.metric-value.price {
  color: #065f46;
  font-weight: 800;
}
.extra-row {
  display: flex;
  align-items: center;
  gap: 6px;
  color: #000000 !important;
}
.description {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  gap: 1.5rem;
}
.description .image {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.description .image img {
  border-radius: 10px;
  width: 100%;
  max-width: 584px;
  max-height: 506px;
  height: auto;
  box-shadow: 0 3px 6px #00000029, 0 3px 6px #0000003b;
  object-fit: cover;
  object-position: bottom;
  display: block;
}
.description .text {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: left;
}
.description .text h1 {
  color: #000000;
  font-size: clamp(1.75rem, 2.2vw + 0.5rem, 2.75rem);
  font-weight: 400;
  line-height: 2.5rem;
  font-family: Ubuntu, sans-serif;
}
.description .text p {
  width: 100%;
  vertical-align: baseline;
  font-size: 14.5px !important;
  color: #7d7c7c;
  font-size: clamp(0.95rem, 0.35vw + 0.75rem, 1.125rem);
  line-height: 1.6rem !important;
}

@media (max-width: 1024px) {
  .description {
    flex-direction: column;
    align-items: center;
    justify-content: center;
    text-align: center;
  }
  .description .image,
  .description .text {
    width: 100%;
  }
  .description .image {
    margin-bottom: 1rem;
  }
}

@media (min-width: 1025px) {
  .description .text {
    padding-left: 1.5rem;
  }
}

.services {
  width: 100%;
  margin-top: 2rem;
  /* padding: 0 1rem; */
}
.services .header {
  font-size: clamp(1.25rem, 1.2vw + 0.75rem, 2rem);
  color: #000000;
  font-weight: 700;
  margin-bottom: 1rem;
  text-align: left;
}
.services-list {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1.25rem;

  width: 100%;
}
.service {
  background: #ffffff;

  border-radius: 10px;
  /* box-shadow: 0 3px 6px #00000029, 0 3px 6px #0000003b; */
  overflow: hidden;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.reviews {
  width: 100vw;
  margin-left: calc(50% - 50vw);
  margin-right: calc(50% - 50vw);
  margin-bottom: 10rem;
  /* background: linear-gradient(175deg, #dbdbdb 60%, #ededed 0%); */
  background: linear-gradient(175deg, #ffffff 60%, #ffffff 0%);
  padding: 2rem 0;
}
.reviews-cta {
  width: 77%;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 12px;
}
.big-wa-btn {
  width: min(860px, 92%);
  height: 64px;
  border-radius: 16px;
  font-size: 1.125rem;
  font-weight: 800;
  background: #25d366 !important;
  color: #111827 !important;
}
.big-wa-btn :deep(.q-btn__content) {
  gap: 10px;
}
.wa-sub {
  color: #4b5563;
  font-size: 0.95rem;
}
.reviews-header {
  width: 77%;
  margin: 0 auto 1rem auto;
  font-size: clamp(1.25rem, 1.2vw + 0.75rem, 2rem);
  color: #000000;
  font-weight: 700;
  text-align: left;
}
.reviews-list {
  width: 77%;
  margin: 0 auto;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(416px, 1fr));
  gap: 1rem;
  justify-items: center;
}
.review-card {
  position: relative;
  width: 416px;
  height: 437px;
  background: #ffffff;
  border-radius: 10px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.review-top {
  text-align: center;
  color: #fbc02c !important;
  font-size: 22px;
  font-weight: 700;
  padding: 1rem 1rem 0 1rem;
}
.review-quote {
  font-style: italic;
  color: #6a6a6a;
  padding: 0 1.25rem;
  text-align: center;
}
.review-bottom {
  display: flex;
  align-items: center;
  justify-content: space-between;
  width: 100%;
  background: rgba(0, 0, 0, 0.05);
  padding: 0.9rem 1rem;
}
.review-score {
  display: flex;
  align-items: center;
  gap: 0.35rem;
}
.review-score-value {
  font-size: 1.25rem;
  color: #000000;
  font-weight: 700;
}
.review-author {
  text-align: right;
}
.review-name {
  color: #000000;
  font-weight: 600;
}
.review-city {
  color: #7d7c7c;
  font-size: 0.85rem;
}
.review-rating-value {
  font-weight: 600;
  color: #ff0000;
}
.review-label {
  position: absolute;
  bottom: 0;
  left: 0;
  background: rgba(0, 0, 0, 0.6);
  color: #ffffff;
  font-size: 0.75rem;
  padding: 0.25rem 0.6rem;
  border-top-right-radius: 8px;
}
.service-image {
  width: 100%;
  height: 220px;
  overflow: hidden;
  perspective: 800px;
}
.service-image img {
  width: 100%;
  height: 100%;
  border-radius: 10px;
  object-fit: cover;
  display: block;
  transform-style: preserve-3d;
  transition: transform 240ms ease;
  will-change: transform;
}
.service-title {
  font-size: 1.125rem;
  font-weight: 600;
  color: #fbc02c !important;
  line-height: 2.5rem;
  font-family: Ubuntu, sans-serif;
}
.service-desc {
  color: #7d7c7c;
  font-size: 0.95rem;
  line-height: 1.5rem;
}

.usp-bar {
  position: relative;
  bottom: 0;
}
.usp-inner {
  background: #5b5b5b14;
  width: 100vw;
  margin: 0rem auto;
  padding: 0.5rem 0;
  display: grid;
  gap: 1rem;
}
.usp-item-container {
  width: 77%;
  margin: 0 auto;
  display: grid;
  color: white !important;
  grid-template-columns: repeat(4, minmax(0, 1fr));
}
.usp-item {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  justify-content: center;
  color: #ffffff !important;
  font-weight: 600;
}
@media (max-width: 1024px) {
  .usp-inner {
    grid-template-columns: repeat(2, minmax(0, 1fr));
    width: 85%;
    display: none;
  }
}
@media (max-width: 640px) {
  .usp-inner {
    grid-template-columns: 1fr;
    width: 100%;
    padding: 0 1rem;
  }
}

@media (max-width: 640px) {
  .hero-inner{
    width: 100%;
    }
    .hero-copy{
      padding: 0 10px;
    }
}
@media (max-width: 1024px) {
  .hero-inner {
    grid-template-columns: 1fr;
  }
  .booking-grid {
    grid-template-columns: 1fr;
  }
  .booking-divider {
    display: none;
  }
  .services-list {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
  .reviews-header,
  .reviews-list {
    width: 85%;
  }
  .reviews-list {
    grid-template-columns: 1fr;
  }
  .review-card {
    width: 96%;
    margin: 0 auto;
  }
  .review-bottom {
    flex-direction: column;
    align-items: center;
  }
  .review-author {
    text-align: center;
  }
  .review-name {
    margin-bottom: 0.5rem;
  }
  .review-card {
    height: 350px;
  }
}

@media (max-width: 640px) {
  .services-list {
    grid-template-columns: 1fr;
  }
  .reviews-list {
    grid-template-columns: 1fr;
  }
  .review-card {
    width: 100%;
  }
  .review-card {
    height: 300px;
  }
}

.features {
  width: 100%;
  margin-left: calc(50% - 50vw);
  margin-right: calc(50% - 50vw);
  /* background: #f6f5f9; */
  padding: 2rem 0;
}
.features-list {
  width: 100%;
  margin: 0 auto;
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 1.25rem;
}
.feature-card {
  display: flex;
  gap: 1rem;
  background: #ffffff;
  border-radius: 12px;
  padding: 1.25rem 1.5rem;
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.06);
}
.feature-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  color: #000000;
}
.feature-title {
  display: flex;
  align-items: center;
  gap: 0.35rem;
  color: #000000;
  font-weight: 700;
  font-size: 1.125rem;
  margin-bottom: 0.5rem;
}
.feature-desc {
  color: #7d7c7c;
}

@media (max-width: 1024px) {
  .features-list {
    width: 85%;
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }
}
@media (max-width: 640px) {
  .features-list {
    width: 100%;
    grid-template-columns: 1fr;
    padding: 0 1rem;
  }
}
</style>