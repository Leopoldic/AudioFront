<template>
  <v-container
    fill-height
    fluid
    grid-list-xl>
    <v-layout
      justify-center
      wrap
    >
      <v-flex
        xs12
        md12
      >
        <material-card
          color="#356276"
          title="Добавление новых сотрудников"
          text="Параметры"
        >
          <v-form>
            <v-container py-0>
              <v-layout wrap>
                <v-flex md4>
                    <v-text-field
                            :items="model"
                            label="Имя сотрудника"
                            @change="filterAuthor"
                    ></v-text-field>
                </v-flex>

                <v-flex md4>
                    <v-text-field
                            :items="text_num"
                            label="Фамилия  сотрудника"
                            @change="filterKurs"
                    ></v-text-field>
                </v-flex>

                <v-flex md4>
                    <v-text-field
                            :items='output_type'
                            label="Отчество сотрудник"
                            @change="donwload_table()"
                    ></v-text-field>
                  </v-flex>
                    <v-flex
                      md12
                      lg12
                    >
                      <material-card
                        class="card-tabs"
                        color="#356276">
                        <v-flex
                          slot="header"
                        >
                          <v-tabs
                            color="transparent"
                            slider-color="white"
                            v-model="tabs"
                          >
                              <span
                                class="subheading font-weight-light mr-3"
                                style="align-self: center"
                              >Аудиодорожки:</span>
                              <v-tab class="mr-3" v-model='tabs'>
                                <v-icon class="mr-2">mdi-bug</v-icon>
                                Добавить
                              </v-tab>
                          </v-tabs>
                        </v-flex>
                        <v-tabs-items v-model="tabs">
                          <v-tab-item
                          >
                          <div>
                            <v-list three-line v-for="(txt,index) in text" :key="txt">
                              <v-list-tile>
                                <v-list-tile-action>
                                  <v-radio-group v-model="selected" >
                                  <v-radio :value="index"
                                  />
                                  </v-radio-group>
                                </v-list-tile-action>
                                <v-list-tile-title>
                                  {{ txt }}
                                </v-list-tile-title>
                              </v-list-tile>
                              <v-divider/>
                            </v-list>
                          </div>
                          </v-tab-item>
                          <v-row align='right'>
                                      <v-btn
                                      color="#356276"
                                      class="text-none"
                                      depressed
                                      >
                                          <v-icon left>folder</v-icon>
                                          <input type="file" id="file" ref="file" v-on:change="handleFileUpload()">
                                      </v-btn>
                                      <v-btn color="#BBAE50" dark @click="removeElement()">Удалить пункт</v-btn>
                            </v-row>
                        </v-tabs-items>
                      </material-card>
                    </v-flex>
                    <v-flex md6></v-flex>
                    <v-flex md4></v-flex>
                    <v-flex md2>
                        <v-btn color="primary"
                        v-if="process_text.length > 0"
                            dark @click="noText()">
                          Обработать аудиодорожку</v-btn>
                    </v-flex>
              </v-layout>
            </v-container>
          </v-form>
        </material-card>
      </v-flex>
       <v-flex
        md4
        sm12
      >
        <material-chart-card
          :data="dailySalesChart.data"
          :options="dailySalesChart.options"
          color="#5E34AA"
          type="Line"
        >
          <h4 class="title font-weight-light">Средняя тренировочная точность</h4>
          <template slot="actions">
            <v-icon
              class="mr-2"
              small
            >
              mdi-clock-outline
            </v-icon>
            <span class="caption grey-lighten-1--text font-weight-light">обновлено 5 часов назад</span>
          </template>
        </material-chart-card>
      </v-flex>
      <v-flex
        md4
        sm12
      >
        <material-chart-card
          :data="dataCompletedTasksChart.data"
          :options="dataCompletedTasksChart.options"
          color="#5E34AA"
          type="Line"
        >
          <h4 class="title font-weight-light">Средняя валидационная точность</h4>

          <template slot="actions">
            <v-icon
              class="mr-2"
              small
            >
              mdi-clock-outline
            </v-icon>
            <span class="caption grey-lighten-1--text font-weight-light">обновлено 7 часов назад</span>
          </template>
        </material-chart-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import axios from 'axios'
export default {
    data: () => ({
        model:['Yandex model','Google model'],
        text_num:['Аудиофайл','Видеофайл'],
        shablons:['Шаблон 1','Шаблон 2', 'Шаблон 3'],
        output_type:['Word','Excel','Pdf'],
        shablon:'Шаблон 1',
        mod:'Yandex model',
        text_n: 'Аудиофайл',
        regims:['Расшифровывать','Не расшифрововать'],
        word_n:'Word',
        text: [],
        process_text: [],
        upload_docs: [],
        upload_texts: [],
        generated_text: [],
        buf:'',
        content:'',
        comment: '',
        regim:'Расшифровывать',
        backgroundColor:'',
        dialog1:false,
        selected:'',
      dailySalesChart: {
        data: {
          labels: [1,2,3,4,5,6,7,8,9],
          series: [[0,0.1,0.15,0.2,0.33,0.47,0.67,0.78,0.98]]
        },
        options: {
          // lineSmooth: this.$chartist.Interpolation.cardinal({
          //   tension: 0
          // }),
          low: 0,
          high: 1, // creative tim: we recommend you to set the high sa the biggest value + something for a better look
          chartPadding: {
            top: 0,
            right: 0,
            bottom: 0,
            left: 0
          }
        }
      },
      dataCompletedTasksChart: {
        data: {
          labels: [1,2,3,4,5,6,7,8,9],
          series: [[0,0.1,0.15,0.2,0.56,0.47,0.83,0.78,0.94]]
        },
        options: {
          // lineSmooth: this.$chartist.Interpolation.cardinal({
          //   tension: 0
          // }),
          low: 0,
          high: 1, // creative tim: we recommend you to set the high sa the biggest value + something for a better look
          chartPadding: {
            top: 0,
            right: 0,
            bottom: 0,
            left: 0
          }
        }
      },
    }),
    methods:{
        delay(ms) {
          return new Promise(r => setTimeout(() => r(), ms))
        },
        removeElement(a){
          this.text.splice(this.selected,1)
          this.process_text.splice(this.selected,1)
          this.selected = ''
        },
        apply(){
          this.text.push('Cозданный текст')
          this.process_text.push(this.comment)
          this.comment = ''
        },
       async handleFileUpload(){
          this.file = this.$refs.file.files[0];
          this.text.push(this.file.name)
          this.file2txt(this.file)
          await this.delay(500)
          this.process_text.push(this.buf)
        },
       async file2txt(file){
          var buf = '';
          let formData = new FormData();
          formData.append('file_uploaded',file);
          axios.
          post( 'http://127.0.0.1:8000/space/upload/',
          formData,
          {
            headers: {
                "Content-Type": 'multipart/form-data',
                'Authorization': 'jwt '.concat(localStorage['token'])
            }
          })
          .then(response  => (
              this.buf = response.data
            ))
        } ,
        sendText(){
          let formData = new FormData();
          formData.append('text',this.process_text.join(' '))
          formData.append('model',this.mod)
          formData.append('text_num',this.text_n)
          formData.append('word_num',this.word_n)
          axios.
          post( 'http://127.0.0.1:8000/space/upload/generate/',
          formData,
          {
            headers: {
                "Content-Type": 'multipart/form-data',
                'Authorization': 'jwt '.concat(localStorage['token'])
            }
          })
          .then(response  => (
              this.generated_text = response.data
            ))
        },
        noText(){
          let formData = new FormData();
          formData.append('text','')
          formData.append('model',this.mod)
          formData.append('text_num',this.text_n)
          formData.append('word_num',this.word_n)
          axios.
          post( 'http://127.0.0.1:8000/space/upload/generate/',
          formData,
          {
            headers: {
                "Content-Type": 'multipart/form-data',
                'Authorization': 'jwt '.concat(localStorage['token'])
            }
          })
          .then(response  => (
              this.generated_text = response.data
            ))
        }
        },
}
</script>