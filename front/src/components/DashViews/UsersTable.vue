<template>
    
  <v-container
    fill-height
    fluid
    grid-list-xl
  >
      <v-flex
        md12
      >
        <material-card
          color="#553D80"
          title="Выполненные проверки"
          text="Данные за 2021 год"
        >
          <template>
            <v-layout row wrap>
                <v-flex xs12>


                    <v-data-table
                            :headers="headers"
                            :items="check"
                            item-key="name"
                            class="elevation-1"

                            :search="filters"
                            :custom-filter="customFilter"
                    >
                        <template
                          slot="headerCell"
                          slot-scope="{ header }"
                        >
                          <span
                            class="font-weight-light text-warning text--darken-3"
                            v-text="header.text"
                          />
                        </template>

                        <template slot="items"
                        slot-scope="{ index, item }"
                        >
                            <tr @click="selected(item)">
                                <td>{{ index+1 }}</td>
                                <td class="text-xs-left">{{ item.kursant.fakultet }}</td>
                                <td class="text-xs-left">{{ item.kursant.kurs }}</td>
                                <td class="text-xs-left">{{ item.kursant.surname }}</td>
                                <td class="text-xs-left">{{ item.kursant.name }}</td>
                              </tr>
                        </template>

                    </v-data-table>

                </v-flex>
            </v-layout>
        </template>
        </material-card>
      </v-flex>
      <!-- <v-btn @click="gotofile()" class='general mb-4'>
        Cкачать отчет
      </v-btn> -->
  </v-container>
</template>

<script>
import axios from 'axios'
export default {
  data: () => ({
    headers: [
      {
        sortable: true,
        text: 'ID',
        value: 'id'
      },
      {
        sortable: true,
        text: 'Модель',
        value: 'kursant.fakultet'
      },
      {
        sortable: true,
        text: 'Тип обрабатываемых данных',
        value: 'kursant.kurs'
      },
      {
        sortable: true,
        text: 'Формат выходных данных',
        value: 'kursant.surname'
      },
      {
        sortable: true,
        text: 'Шаблон',
        value: 'kursant.name'
      },
    ],
    dialog:false,
    dialog2:false,
    check:[
      {kursant:{name:'Шаблон 1',
      surname:'Word',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 2',
      surname:'Excel',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 1',
      surname:'PDF',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 1',
      surname:'Word',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 2',
      surname:'PDF',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 1',
      surname:'Word',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 1',
      surname:'PDF',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 3',
      surname:'Word',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 1',
      surname:'Excel',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 2',
      surname:'Word',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 1',
      surname:'Excel',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 1',
      surname:'Word',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 2',
      surname:'Excel',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 3',
      surname:'Word',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
      {kursant:{name:'Шаблон 1',
      surname:'Word',
      fakultet:'Yandex model',
      kurs:'Аудио'}},
    ],
    id:'',
    fakultet:['Все',1,2,3,4,5,6,7,8,9],
    kurs:['Все',1,2,3,4,5],
    filters: {
      fakultet: 'Все',
      kurs: 'Все',
      result: 'Все',
      surname:'',
      name:'',
      source:'',
    },
    category:['Все','Утечка персональных данных', 'Утечка фото/видео', 'Распространение секретных данных','Нарушений не выявлено'],
    select:{},
    result:'',
  }),
  methods: {
    gotofile() {
      axios.
      post( 'http://127.0.0.1:8000/file/get/',
      'привет',
      {
        headers: {
            "Content-Type": 'multipart/form-data',
            'Authorization': 'jwt '.concat(localStorage['token'])
        }
      })
      .then(response => (this.result = response.data))
    },
    delay(ms) {
        return new Promise(r => setTimeout(() => r(), ms))
      },
    gotoprofile(id) {
      console.log(id)
      this.$router.push({ path: `user-profile/${id}` });
      },
    selected(item){
      this.select = item
      console.log(this.select)
    },

    customFilter(items, filters, filter, headers) {
      // Init the filter class.
      const cf = new this.$MultiFilters(items, filters, filter, headers);

      cf.registerFilter('surname', function (searchWord, items) {
        if (searchWord.trim() === '') return items;

        return items.filter(item => {
          return item.kursant.surname.toLowerCase().includes(searchWord.toLowerCase());
        }, searchWord);
      });

      cf.registerFilter('name', function (searchWord, items) {
        if (searchWord.trim() === '') return items;

        return items.filter(item => {
          return item.kursant.name.toLowerCase().includes(searchWord.toLowerCase());
        }, searchWord);
      });

      cf.registerFilter('source', function (searchWord, items) {
        if (searchWord.trim() === '') return items;

        return items.filter(item => {
          return item.source.toLowerCase().includes(searchWord.toLowerCase());
        }, searchWord);
      });

      cf.registerFilter('fakultet', function (fakultet, items) {
        if (fakultet === 'Все') return items;

        return items.filter(item => {
          return item.kursant.fakultet === fakultet;
        }, fakultet);

      });

      cf.registerFilter('kurs', function (kurs, items) {
        if (kurs === 'Все') return items;

        return items.filter(item => {
          return item.kursant.kurs=== kurs;
        }, kurs);

      });

      cf.registerFilter('result', function (result, items) {
        if (result === 'Все') return items;

        return items.filter(item => {
          return item.done === result;
        }, result);

      });

      // Its time to run all created filters.
      // Will be executed in the order thay were defined.
      return cf.runFilters();
    },


      /**
       * Handler when user input something at the "Filter" text field.
       */
    filterSurname(val) {
      this.filters = this.$MultiFilters.updateFilters(this.filters, {surname: val});
    },

    filterName(val) {
      this.filters = this.$MultiFilters.updateFilters(this.filters, {name: val});
    },

    filterSource(val) {
      this.filters = this.$MultiFilters.updateFilters(this.filters, {source: val});
    },
    /**
     * Handler when user  select some author at the "Author" select.
     */
    filterFak(val) {
      this.filters = this.$MultiFilters.updateFilters(this.filters, {fakultet: val});
    },

    filterKurs(val) {
      this.filters = this.$MultiFilters.updateFilters(this.filters, {kurs: val});
    },

    filterRes(val) {
      this.filters = this.$MultiFilters.updateFilters(this.filters, {result: val});
    },

  },
  async created() {
    let headers = {
        'Content-Type': 'application/json',
        'Authorization': 'jwt '.concat(localStorage['token'])
          }
    axios
      .get('http://127.0.0.1:8000/space/list/check',{headers})
      .then(response => (this.check = response.data));
  }

}
</script>