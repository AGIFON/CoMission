<template>
  <q-page class="flex flex-center">
    <q-stepper vertical v-model="currentStep">
      <q-step name="first" title="Выбор дат">
        <p>Выберите предпологаемую дату поездки</p>
        <q-datetime v-model="date" placeholder="Дата туда" class="mb-3" type="date" />
        <q-stepper-navigation>
          <q-btn
            class="mb-3"
            color="primary"
            @click="dateRange"
            label="Ввести дату"
          />
        </q-stepper-navigation>
      </q-step>

      <q-step name="second" title="второй">
        2
        <q-stepper-navigation>
          <q-btn
            color="primary"
            @click="currentStep = 'second2'"
            label="Go Back"
          />
        </q-stepper-navigation>
      </q-step>
      <q-step name="second2" title="">
        3
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

<style scoped>
  .q-stepper{
    width: 100%;
    margin: 3%;
  }
  .mb-3{
    margin-bottom: 10px;
  }
</style>

<script>
export default {
  name: 'PageIndex',
  data () {
    return {
      currentStep: 'first',
      date: ''
    }
  },
  methods: {
    dateRange () {
      this.currentStep = 'second';

    }
  }
}
</script>
