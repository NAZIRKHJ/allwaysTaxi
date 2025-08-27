<template>
  <q-page class="container">
    <div class="page-wrap">
      <h1 class="page-title">Privacy & Cookies</h1>
      <div class="content">
        <p>Bij Allways Taxi hechten we veel waarde aan jouw privacy.</p>

        <h2 class="section-title">Persoonsgegevens</h2>
        <p>
          Wij verzamelen en gebruiken persoonsgegevens uitsluitend voor het uitvoeren van onze dienstverlening, zoals:
        </p>
        <ul class="bullet-list">
          <li>Het verwerken van boekingen en betalingen.</li>
          <li>Het contact opnemen bij wijzigingen of bijzonderheden.</li>
          <li>Het verbeteren van onze website en service.</li>
        </ul>

        <h2 class="section-title">Cookies</h2>
        <p>
          Onze website maakt gebruik van cookies om jouw gebruikerservaring te verbeteren. Deze cookies verzamelen anonieme
          informatie over websitegebruik. Je kunt cookies altijd uitschakelen via je browserinstellingen.
        </p>

        <h2 class="section-title">Beveiliging</h2>
        <p>Wij nemen passende maatregelen om jouw gegevens te beschermen tegen verlies of onrechtmatig gebruik.</p>

        <h2 class="section-title">Delen van gegevens</h2>
        <p>
          Allways Taxi deelt nooit persoonsgegevens met derden, tenzij dit noodzakelijk is voor de uitvoering van de rit of
          wettelijk verplicht is.
        </p>

        <p>
          Voor vragen over privacy of het opvragen/verwijderen van gegevens kun je contact opnemen via ons contactformulier.
        </p>

        <q-btn class="q-mt-md" color="black" icon="arrow_back" label="Terug" @click="$router.back()" />
      </div>
    </div>
  </q-page>
</template>

<script setup>
</script>

<style scoped>
.container {
  width: 77%;
  margin: 0 auto;
  padding-top: 1.25rem;
}
.page-wrap { padding: 1rem 0 3rem 0; }
.page-title {
  color: #000000;
  font-size: clamp(1.5rem, 1.2vw + 1rem, 2.25rem);
  font-weight: 800;
  margin-bottom: 1rem;
}
.section-title {
  color: #000000;
  font-size: 1.25rem;
  font-weight: 700;
  margin: 1rem 0 0.5rem 0;
}
.content { color: #2d2a2a; line-height: 1.75; }
.bullet-list { padding-left: 1.25rem; }
.bullet-list li { margin: 0.25rem 0; }
@media (max-width: 1024px) { .container { width: 85%; } }
@media (max-width: 768px) { .container { width: 90%; padding: 0 1rem; } }
</style>



