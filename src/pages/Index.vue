<template>
  <q-page class="flex flex-center">
    <q-alert v-if="this.message" color="info">
      {{ this.message }}
    </q-alert>

    <q-stepper vertical v-model="currentStep">
      <q-step name="first" title="Выбор дат">
        <p>Выберите предпологаемую дату поездки</p>
        <q-datetime v-model="date" @change="onDateSelect" placeholder="Дата туда" class="mb-3" type="date"/>
        <q-stepper-navigation>
          <q-btn
            class="mb-3"
            color="primary"
            @click="dateRange"
            v-if="date != ''"
            label="Далее"
          />
        </q-stepper-navigation>
      </q-step>

      <q-step name="second2" title="Время пребывания в стране">
        <p>Сколько Вы будите в Великобритании?</p>
        <q-btn @click="currentStep = 'second3'" class="mb-3" color="positive" label="Меньше 6 месяцев"/>
        <q-btn @click="currentStep = 'second3'" color="secondary" label="Больше 6 месяцев"/>
      </q-step>

      <q-step name="second3" title="Определяемся с типом визы">


        <p>Есть <a href="https://www.gov.uk/browse/visas-immigration">более 30</a> типов виз, так просто не
          определиться. Поэтому лучше сразу идти на сайт Правительства Великобритании.
          Определите тип визыа страничке <a target="_blank" href="https://www.gov.uk/check-uk-visa/y">Check if you need
            a UK visa</a> (перейдите по <a target="_blank" href="https://www.gov.uk/check-uk-visa/y">ссылке</a>)
        </p>
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
          filter
          v-model="select"
          float-label="Выберите визу"
          :options="selectOptions"
        />

        <q-stepper-navigation>
          <q-btn
            color="primary"
            @click="currentStep = 'first'"
            label="Получить отчёт"
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
          },
          {
            label: 'twit',
            value: 'fb'
          },
          {
            label: 'face',
            value: 'fb'
          },
          {
            label: 'book',
            value: 'fb'
          },
          {
            label: 'lab',
            value: 'fb'
          }
        ],
        date: '',
        message: null
      }
    },
    methods: {
      dateRange() {
        this.currentStep = 'second2'
        this.message = null;
      },
      onDateSelect() {
        var today = new Date()
        const dateToReply = new Date(this.date)
        var timeinmilisec = dateToReply.getTime() - today.getTime()
        const diffNum = Math.ceil(timeinmilisec / (1000 * 60 * 60 * 24))
        const oneMess = 'Самое ранее когда вы можете подать визу, это за три месяца' // больше 135 дней
        const secondMess = 'Обычно весь процесс получения визы занимает 1,5 месяца, вы прекрасно успеваете' // меньше 135 дней, больше 45 дней
        const thirdMess = 'Чуть сжатые сроки. Нужно будет заплатить консультационный сбор и услуги prime time' // от 35 до 45 дней
        const fourthMess = 'Сильно сжатые сроки. Поэтому нужно заплатить консультацинный сбор Priority Service' //  меньше 35 дней
        const fiveMess = 'Слишком короткий срок , вы не сможете получить визу в срок ' // меньше 10
        if(diffNum < 10){
          this.message = fiveMess;
        }
        if (diffNum < 35 && diffNum > 10) {
          this.message = fourthMess;
        }
        if (diffNum >= 35 && diffNum < 45) {
          this.message = thirdMess;
        }
        if (diffNum >= 45 && diffNum < 135) {
          this.message = secondMess;
        }
        if (diffNum >= 135) {
          this.message = oneMess;
        }
      }

    }
  }
</script>
