<template>
  <q-page class="flex flex-center">
    <q-stepper vertical v-model="currentStep">
      <q-step name="first" title="Выбор дат">
        <p>Выберите предпологаемую дату поездки</p>
        <q-datetime v-model="date" placeholder="Дата туда" class="mb-3" type="date"/>
        <q-stepper-navigation>
          <q-btn
            class="mb-3"
            color="primary"
            @click="dateRange"
            label="Ввести дату"
          />
        </q-stepper-navigation>
      </q-step>

      <q-step name="second2" title="Цель поездки">
        <p>Сколько Вы будите в Великобритании?</p>
        <q-btn @click="currentStep = 'second3'" class="mb-3" color="positive" label="Меньше 6 месяцев" />
        <q-btn  @click="currentStep = 'second3'" color="secondary" label="Больше 6 месяцев" />

      </q-step>

      <q-step name="second3" title="Цель поездки">

        <p class="mb-3">Определите тип визы по <a target="_blank" href="https://www.gov.uk/check-uk-visa/y">ссылке</a> :</p>
        <q-stepper-navigation>
          <q-btn
            class="mb-3"
            color="primary"
            @click="currentStep = 'second4'"
            label="Далее"
          />
        </q-stepper-navigation>
      </q-step>

      <q-step name="second4" title="Выбор визы">

        <q-select
          class="mb-3"
          v-model="select"
          float-label="Выберите визу"
          :options="selectOptions"
        />

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
  .q-stepper {
    width: 100%;
    margin: 3%;
  }

  .mb-3 {
    margin-bottom: 10px;
  }
</style>

<script>
  export default {
    name: 'PageIndex',
    data() {
      return {
        currentStep: 'first',
        selectOptions: [
          {
            label: 'Google',
            value: 'goog'
          },
          {
            label: 'Facebook',
            value: 'fb'
          }
        ],
        date: ''
      }
    },
    methods: {
      dateRange() {
        this.currentStep = 'second2'
      }
    }
  }
</script>
