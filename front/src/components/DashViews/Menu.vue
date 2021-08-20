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
          color="#553D80"
          title="Расширенные настройки обработки аудио, видео файлов"
          text="Параметры"
        >
          <v-form>
            <v-container py-0>
              <v-layout wrap>
                <v-flex md4>
                    <v-select
                            :items="model"
                            label="Модель"
                            v-model='mod'
                            @change="filterAuthor"
                    ></v-select>
                </v-flex>

                <v-flex md4>
                    <v-select
                            :items="text_num"
                            label="Тип обрабатываемых данных"
                            v-model='text_n'
                            @change="filterKurs"
                    ></v-select>
                </v-flex>

                <v-flex md4>
                    <v-select
                            :items='output_type'
                            label="Формат выходных данных"
                            @change="donwload_table()"
                            v-model='word_n'
                    ></v-select>
                  </v-flex>
                <v-flex md4>
                      <v-select
                            :items='shablons'
                            label="Использовать шаблоны"
                            @change="donwload_table()"
                            v-model='shablon'
                    ></v-select>
                </v-flex>
                <v-flex md2>
                  <v-btn
                        color='#553D80'
                        class="text-none"
                        depressed
                        >
                            Добавить новый шаблон 
                        </v-btn>
                </v-flex>
                  <v-flex md4>
                    <v-select
                            :items="regims"
                            label="Специфические термины"
                            v-model='regim'
                    ></v-select>
                  </v-flex>
                  <v-flex md2 >
                      <v-btn
                        color='#553D80'
                        class="text-none"
                        depressed
                        >
                            Редактировать словарь терминов 
                        </v-btn>
                    </v-flex>
                    <v-flex
                      md12
                      lg12
                    >
                      <material-card
                        class="card-tabs"
                        color="#553D80">
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
                                      color='#553D80'
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
      <v-flex md6>
        <material-card class="v-card-profile">
          <v-card-title class="text-xs-center">
            <h4 class="card-title font-weight-light"></h4>
            <h3>Обработанные данные</h3>
            <p class="card-description font-weight-light"></p>
          </v-card-title>
          <v-divider></v-divider>
          <material-card v-for="(txt,index) in this.process_text" :key="index">
            <v-card-title>
              <h5>{{"Номер текста: " + index }}</h5>
            </v-card-title>
            <v-textarea
              :value="txt"
              filled
              background-color="grey lighten-2"
              color="cyan"
              readonly
              m10
              >
              </v-textarea>
              <v-flex md12></v-flex>
          </material-card>
        </material-card>
      </v-flex>
      <v-flex md6>
        <material-card class="v-card-profile">
          <v-card-title class="text-xs-center">
            <h4 class="card-title font-weight-light"></h4>
            <h3>Сгенерированный протоколы</h3>
            <p class="card-description font-weight-light"></p>
          </v-card-title>
          <v-divider></v-divider>
          <material-card v-for="(txt,index) in this.generated_text" :key="index">
            <v-card-title>
              <h5>{{"Номер текста: " + index }}</h5>
            </v-card-title>
            <v-textarea
              :value="txt"
              filled
              background-color="grey lighten-2"
              color="cyan"
              readonly
              m10
              >
              </v-textarea>
              <v-flex md12></v-flex>
        </material-card>
        </material-card>
      </v-flex>
      <v-flex md6>
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