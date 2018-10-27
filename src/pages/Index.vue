<template>
  <q-page class="flex flex-center">
    <q-stepper v-model="currentStep">
      <q-step name="first">
        <p>Выберите предпологаемую дату поездки</p>
        <q-datetime v-model="model" placeholder="Дата туда" class="mb-3" type="date" />
        <q-datetime v-model="model" placeholder="Дата оттуда" type="date" />
        <q-stepper-navigation>
          <q-btn
            color="primary"
            @click="currentStep = 'second'"
            label="Ввести дату"
          />
        </q-stepper-navigation>
      </q-step>

      <q-step name="second">
        Two
        <q-stepper-navigation>
          <q-btn
            color="primary"
            @click="currentStep = 'first'"
            label="Go Back"
          />
        </q-stepper-navigation>
      </q-step>
    </q-stepper>
  </q-page>
</template>

<style>
</style>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      // we initialize it with first step's name
      currentStep: 'first'
    }
  }
}
</script>
