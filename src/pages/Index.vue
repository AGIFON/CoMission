<template>
  <q-page class="flex flex-center">

    <q-stepper vertical v-model="currentStep">
      <q-step name="first" title="Выбор дат">
        <p>Выберите предпологаемую дату поездки</p>
    <q-alert v-if="this.message" color="warning">
      {{ this.message }}
    </q-alert>
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
        <q-btn @click="handleThirdStep(0)" class="mb-3" color="positive" label="Меньше 6 месяцев"/>
        <q-btn @click="handleThirdStep(1)" color="secondary" label="Больше 6 месяцев"/>
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
          v-model="selected"
          float-label="Выберите визу"
          :options="selectOptions"
        />
        <p v-if="showMoneyBlock()">
          Нужно показать, что у Вас есть деньги, в том числе на поездку (1500 долларов США). Нет денег? Вот <a target="_blank" href="https://docs.google.com/document/d/1BwxPmn80mwfItDtumLwrBkHVY2f58QkhT7tR8pZ3FcQ/edit?usp=sharing">несколько советов</a>. 
        </p>

        <q-stepper-navigation>
          <q-btn
            color="primary"
            @click="currentStep = 'second5'"
            label="Далее"
          />

        </q-stepper-navigation>
      </q-step>

      <q-step name="second5" title="Вы кто?">

        <q-select
          class="mb-3"
          v-model="occupation"
          float-label="Ваша занятость"
          :options="getOccupationList()"
        />
        <p v-if="occupation">
          {{ this.showOccupationBlock() }} 
        </p>

        <q-stepper-navigation>
          <q-btn
            color="primary"
            @click="getReportAction"
            label="Получить отчёт"
          />

        </q-stepper-navigation>
      <q-spinner v-if="this.loading" size="60px" />
      <a v-if="this.showLink" target="_blank" style="font-size: 160%" href="#">Скачать PDF</a>
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

import axios from 'axios';

function encodeQueryData(data) {
   const ret = [];
   for (let d in data)
     ret.push(encodeURIComponent(d) + '=' + encodeURIComponent(data[d]));
   return ret.join('&');
}

  export default {
    name: 'PageIndex',
    data() {
      return {
        currentStep: 'first',
        selectOptions: [
          {
            label: 'Standart Visitor visa',
            value: 'standartVisitor'
          },
          {
            label: 'Short-term study visa',
            value: 'shortTermStudy'
          },
          {
            label: 'Marriage Visitor visa',
            value: 'marriageVisitor'
          },
          {
            label: 'Direct Airside Transit visa (DATV)',
            value: 'datv'
          },
          {
            label: 'Visitor in Transit visa',
            value: 'visitorInTransit'
          }
        ],
        date: '',
        selected: null,
        occupation: null,
        loading: false,
        showLink: false,
        isMoreThanSixMonth: 0,
        message: null
      }
    },
    methods: {
      dateRange() {
        this.currentStep = 'second2'
        this.message = null;
      },
      getOccupationList() {
        return [
          { label: 'Наемный работчий', value: 'employee' },
          { label: 'Пенсионер', value: 'retiree' },
          { label: 'Студент', value: 'student' },
          { label: 'Владелец бизнеса', value: 'businessman' },
          { label: 'ИП', value: 'ip' }
        ]
      },
      showMoneyBlock () {
        switch (this.selected) {
            case 'standartVisitor':
            case 'shortTermStudy':
            case 'marriageVisitor':
              return true;
            default:
              return false;
        }
      },
      showOccupationBlock() {
        const employeeText = '→Ваша доход больше 1000 рублей в месяц?  Да → Возьмите справку от работодателя с указанием даты начала работы, зарплаты, роли, контактных данных компании.  Агентства визовой поддержки советуют брать справку о зарплате за последние 6 месяцев, расписанной помесячно с указанием занимаемой должности и дате начала работы в компании.  •  Нет → Сама по себе невысокая зарплата – не повод получения отказа в визе. «Но если вы получаете 600 рублей в месяц и за 5 лет ездили только в Литву и Швецию, к примеру (о чем «расскажут» отметки в паспорте. – Прим. авт.), то вероятность отказа повышается», – отмечают консультанты Агентства визовой поддержки Visavisa.by.  Привлечь в качестве спонсора супруга или родителей и, опять же предъявить посольству справку о зарплате и/или справку из банка о движении средств по счету с достаточной итоговой суммой, ксерокопии свидетельства о браке или паспорта спонсора (см. как оформлять ксерокопии в статье «Заверение ксерокопий»).';
        const retireeText = 'Пенсионерам понадобятся копия пенсионного удостоверения, оригинал выписки из банка о начислении пенсии за последние 6 месяцев, сообщают в “Homo Touristus”.';
        const studentText = 'Подготовьте документы о том, что вы трудоустроены или обучаетесь на дневной форме.\n\n  Справку от работодателя на фирменном бланке с подробностями о вашей зарплате, роли в компании и продолжительности работы. \n\n Показывайте заплату за последние 6 месяцев, рекомендуют в агентствах. \n\n Письмо из учреждения образования на фирменном бланке, что являетесь студентом дневной формы обучения, с подтверждением предоставления отпуска на период предполагаемой поездки. \n\n   Туристическая компания “Homo Touristus” советует еще сделать копии студенческого билета и всех заполненных страниц зачетной книжки.';
        const businessmanText = 'Документы о регистрации бизнеса с указанием имени владельца и датой регистрации – в случае самозанятости. Кроме того, сообщают в “Homo Touristus”, нужны: оригинал справки о полученных дивидендах за полгода-год (для владельцев бизнеса).';
        const ipText = 'Для индивидуальных предпринимателей это свидетельство о регистрации, для владельцев бизнеса – копия устава предприятия (страницы с печатями, данными о владельцах, долях) и копия свидетельства о государственной регистрации предприятия, сообщают в компании “Homo Touristus”. Кроме того, сообщают в “Homo Touristus”, нужны: оригинал справки из банка о движении средств  по счету за 6 месяцев и финальном положительном балансе на счету.';
        switch(this.occupation) {
          case 'employee':
             return employeeText;
          case 'retiree':
            return retireeText;
          case 'student':
            return studentText;
          case 'businessman':
            return businessmanText;
          case 'ip':
            return ipText;
          default:
            return '';
        }
      },
      handleThirdStep(isMoreThanSixMonth) {
        this.currentStep = 'second3';
        this.isMoreThanSixMonth = isMoreThanSixMonth;
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
      },

      getReportAction() {
        console.log('sending');
    //    const getData = {
    //      entryDate: this.date.substring(0,10),
    //      isMoreThanSixMonth: this.isMoreThanSixMonth, 
    //      visaType: this.selected 
    //    };
    //    const queryString = encodeQueryData(getData);
    //    window.open('http://example.com/foo?' + queryString, '_blank');
        this.loading = true;
        this.showLink = false;
        setTimeout(() => {
          this.loading = false;
          this.showLink = true;
        }, 2000)

      }

    }
  }
</script>
