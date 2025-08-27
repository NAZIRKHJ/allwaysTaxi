<template>
  <q-page class="container">
    <div class="page-wrap">
      <h1 class="page-title">Over ons</h1>
      <div class="content">
        <p>
          Welkom bij Allways Taxi, jouw betrouwbare partner voor taxivervoer in Nederland en daarbuiten.
          Wij zorgen voor comfortabel, veilig en betaalbaar vervoer – of het nu gaat om een korte rit in de stad,
          luchthavenvervoer, zorgvervoer of zakelijke afspraken.
        </p>
        <p>
          Ons team van ervaren chauffeurs staat 24/7 voor je klaar en rijdt altijd met oog voor service en professionaliteit.
          Bij Allways Taxi reis je met de zekerheid dat je op tijd, veilig en zorgeloos op je bestemming aankomt.
        </p>
        <p>
          Wij geloven in kwaliteit, duidelijkheid en klantgerichtheid. Daarom bieden wij:
        </p>
        <ul class="bullet-list">
          <li>Transparante prijzen zonder verrassingen.</li>
          <li>Betrouwbare chauffeurs met ervaring en vergunningen.</li>
          <li>Flexibel boeken: direct of vooraf reserveren.</li>
        </ul>
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
.content { color: #2d2a2a; line-height: 1.75; }
.bullet-list { padding-left: 1.25rem; }
.bullet-list li { margin: 0.25rem 0; }
@media (max-width: 1024px) { .container { width: 85%; } }
@media (max-width: 768px) { .container { width: 90%; padding: 0 1rem; } }
</style>



