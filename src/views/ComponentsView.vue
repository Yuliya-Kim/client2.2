<template>
  <v-container fluid>

    <v-row>
      <v-col cols="12">
        <h2 class="text-h5 greyDarken-4--text">Микроконтроллеры</h2>
      </v-col>
    </v-row>

    <v-row>
      <!-- ФИЛЬТРЫ -->

      <v-col cols="3" md="2">
        <v-card flat class="rounded-lg">
          
          <!-- Выбор отображаемых столбцов -->
          <v-expansion-panels flat>
            
            <v-expansion-panel class="rounded-lg">
              <v-expansion-panel-header class="px-5 py-0">Отображаемые столбцы</v-expansion-panel-header>
              <v-expansion-panel-content class="pa-0 filters-header-list">
                <v-card flat class="pa-0">
                  <v-card-text class="pa-0"> 
                    <v-list dense class="pa-0">
                      <v-list-item-group v-model="checkedHeaders" multiple >
                        <template v-for="col in headersFilterList">
                          
                          <v-list-item :value="col" :key="col" active-class="active-header-item" class="pa-0">
                            <template v-slot:default="{ active }">
                              
                              <v-list-item-action>
                                <v-checkbox dense :input-value="active" color=""></v-checkbox>
                              </v-list-item-action>

                              <v-list-item-content>
                                <v-list-item-title>{{ col.text }}</v-list-item-title>
                              </v-list-item-content>

                            </template>
                          </v-list-item>
                          
                        </template>
                      </v-list-item-group>
                    </v-list>


                  </v-card-text>
                </v-card>       
              </v-expansion-panel-content>
            </v-expansion-panel>
          </v-expansion-panels>

        </v-card>

        <br>

        <!-- Фильтры -->
        <v-card flat class="rounded-lg">
          <v-expansion-panels v-for="(value, key, i) in filters" :key="key" flat hover tile>
            <v-expansion-panel>
              <v-expansion-panel-header class="px-5 py-0 exp-panel-header-active">{{headers[i+1].text}}</v-expansion-panel-header>
              <v-expansion-panel-content class="pa-0">
                <v-card flat class="pa-0">


                  <!-- <v-card-text v-if="key == 'frequency'" class="pa-0"> 

                    <v-slider
                      v-model="activeFilters[key]"
                      class="align-center"
                      :max="200"
                      :min="10"
                      hide-details
                    >
                      <template v-slot:append>
                        <v-text-field
                          v-model="activeFilters[key]"
                          class="mt-0 pt-0"
                          hide-details
                          single-line
                          type="number"
                          style="width: 60px"
                        ></v-text-field>
                      </template>
                    </v-slider>


                    <span v-for="item in filters[key]" :key="`${item}`">{{item}}</span>

                  </v-card-text> -->

                  <v-card-text class="pa-0"> 

                    <v-list flat dense class="pa-0">

                      <v-list-item-group  multiple v-model="activeFilters[key]" class="py-2">
                        <template v-for="item in filters[key]">
                          <v-list-item :key="`${item}`" :value="item" :ripple="false" class="pa-0">
                            <template v-slot:default="{ active }">
                              <v-list-item-action>
                                <v-checkbox 
                                  :input-value="active" :true-value="item"
                                  color="deep-orange lighten-2" :ripple="false" dense
                                ></v-checkbox>
                              </v-list-item-action>
                              <v-list-item-content> 
                                <v-list-item-title v-text="item"></v-list-item-title>
                              </v-list-item-content>
                            </template>
                          </v-list-item>
                        </template>
                      </v-list-item-group>

                    </v-list>

                  </v-card-text>

                </v-card>       
              </v-expansion-panel-content>
            </v-expansion-panel>
          </v-expansion-panels>

        </v-card>
      </v-col>

      <!-- ТАБЛИЦА -->
      <v-col cols="9" md="10">
        <v-card flat class="pa-5 rounded-lg">
          

          <!-- ПОИСК И ДОБАВЛЕНИЕ -->
          <v-row>
            <!-- Поиск -->
            <v-col cols="12" md="4">
              <v-text-field v-model="search" dense outlined single-line color="grey" label="Поиск" append-icon="mdi-magnify"></v-text-field>
            </v-col>

            <v-spacer></v-spacer>


            <v-col class="text-right" cols="auto">
              <v-dialog v-model="editForm" max-width="560px">
                <!-- "Добавить" -->
                <template v-slot:activator="{ on, attrs }">
                  <v-btn v-bind="attrs" v-on="on" depressed large color="cyan" class="white--text">
                    <v-icon left>mdi-plus</v-icon>Добавить
                  </v-btn>
                </template>

                <!-- Форма добавления компонента -->
                <v-card light>

                  <v-toolbar flat dark color="cyan" class="flex-grow-0">
                    <v-toolbar-title class="ml-7">Новый компонент</v-toolbar-title>
                    <v-btn icon absolute dark right @click="closeEditForm()">
                      <v-icon>mdi-close</v-icon>
                    </v-btn>
                  </v-toolbar>


                  <validation-observer ref="observer" v-slot="{ invalid }">
                    
                    <v-card-text class="pa-7">
                      <v-form ref="editForm">

                        <validation-provider v-slot="{ errors }" name="Наименование" rules="required">
                          <v-text-field
                            v-model="form.name"
                            color="grey"
                            :error-messages="errors"
                            label="Наименование"
                            outlined
                            class="required"
                          ></v-text-field>
                        </validation-provider>

                        <validation-provider v-slot="{ errors }" name="Ссылка на сайт">
                          <v-text-field
                            v-model="form.site"
                            color="grey"
                            :error-messages="errors"
                            label="Ссылка на сайт"
                            outlined
                            append-icon="mdi-link"
                          ></v-text-field>
                        </validation-provider>

                        <validation-provider v-slot="{ errors }" name="Страна" rules="required">
                          <v-autocomplete
                            v-model="form.country"
                            :items="getCountries()"
                            no-data-text="Ничего не найдено"
                            color="grey"
                            :error-messages="errors"
                            label="Страна"
                            outlined
                            class="required"
                            append-icon="mdi-map"
                          >

                            <template slot="item" slot-scope="{ item }">
                              <div class="d-flex align-center">
                                <countryFlag :country="countryFlag(item)" size='normal' class="ma-0 pa-0"/>
                                <span>{{ item }}</span>
                              </div>                              
                            </template>

                            <template v-slot:selection="{ item }">
                              <div class="d-flex align-center">
                                <countryFlag :country="countryFlag(item)" size='normal' class="ma-0 pa-0"/>
                                <span>{{ item }}</span>
                              </div>
                            </template>

                          </v-autocomplete>
                        </validation-provider>

                        <validation-provider v-slot="{ errors }" name="Типы компонентов">
                          <v-autocomplete
                            v-model="form.compTypes"
                            :items="getCompTypes()"
                            no-data-text="Ничего не найдено"
                            multiple
                            color="grey"
                            :error-messages="errors"
                            label="Типы компонентов"
                            outlined
                            chips
                            append-icon="mdi-memory"
                          >

                            
                            <template #item="{ item }">                          
                              <v-list-item class="d-flex" @click="toggleItem(item)">
                                <div>
                                  <v-simple-checkbox color="primary" :value="isSelected(item)" @click="toggleItem(item)" />
                                </div>
                                <div class="ml-2" >
                                  <v-chip :color="typeColor(item)">
                                    <span class="text-caption">{{ item }}</span>
                                  </v-chip> 
                                </div>
                              </v-list-item>
                            </template>



                            <template v-slot:selection="{ attrs, item, select, selected }">
                              <v-chip 
                                dark small
                                v-bind="attrs"
                                :input-value="selected"
                                :color="typeColor(item)"
                                close
                                @click="select"
                                @click:close="remove(item)"
                              >
                                <span class="text-caption black--text">{{ item }}</span>
                              </v-chip>
                            </template>

                          </v-autocomplete>

                        </validation-provider>

                      </v-form>

                    </v-card-text>

                    <v-alert v-if="errorMessageForm" type="warning" text dismissible class="mx-7 mb-7">
                      {{ errorMessageForm }}
                    </v-alert>

                    <v-card-actions class="pa-7 pt-0">
                      <v-spacer></v-spacer>
                      <v-btn @click="closeEditForm()" color="pink" text>
                        Отмена
                      </v-btn>
                      <v-btn :disabled="invalid" @click.prevent="addBrand()" color="cyan" class="white--text">
                        Добавить
                      </v-btn>
                    </v-card-actions>

                  </validation-observer>
                </v-card>
              </v-dialog>
            </v-col> 

          </v-row>

          <!-- ТАБЛИЦА КОМПОНЕНТОВ -->
          <v-data-table 
            :items="components" :headers="selectedHeaders" :search="search"
            :rows-per-page-items="[10, 20, 30, 40]"
            :footer-props="{ 'items-per-page-options': [-1, 20, 30, 40, 50, ], 'itemsPerPageText': 'Количество записей', 'items-per-page-all-text' : 'Все', }" 
            class="components-table custom-scrollbar"
          >

            <template v-for="(col, i) in filters" v-slot:[`header.${i}`]="{ header }">
              <div :key="col" class="d-inline-flex align-center justify-space-between">
              
                <span :key="col">{{ header.text }}</span>
                
                <div :key="col">

                  <v-menu :close-on-content-click="false" :nudge-width="200" offset-y transition="slide-y-transition" left fixed style="position: absolute; right: 0">
                    
                    <template v-slot:activator="{ on, attrs }">
                      <v-btn color="indigo" icon v-bind="attrs" v-on="on">
                        <v-icon small :color="activeFilters[header.value] && activeFilters[header.value].length < filters[header.value].length ? 'red' : 'default'">
                          mdi-filter-variant
                        </v-icon>
                      </v-btn>
                    </template>

                    <v-list flat dense class="pa-0">

                      <v-list-item-group multiple v-model="activeFilters[header.value]" class="py-2">
                        <template v-for="item in filters[header.value]">
                          <v-list-item :key="`${item}`" :value="item" :ripple="false">
                            <template v-slot:default="{ active }">
                              <v-list-item-action>
                                <v-checkbox 
                                  :input-value="active" :true-value="item" 
                                  color="primary" :ripple="false" dense
                                ></v-checkbox>
                              </v-list-item-action>
                              <v-list-item-content> 
                                <v-list-item-title v-text="item"></v-list-item-title>
                              </v-list-item-content>
                            </template>
                          </v-list-item>
                        </template>
                      </v-list-item-group>

                      <v-divider></v-divider>

                      <v-row no-gutters>
                        <v-col cols="6">
                          <v-btn text block @click="toggleAll(header.value)" color="cyan darken-2">Выбрать всё</v-btn>
                        </v-col>
                        <v-col cols="6">
                          <v-btn text block @click="clearAll(header.value)" color="pink">Очистить</v-btn>
                        </v-col>
                      </v-row>

                    </v-list>
                  </v-menu>
                </div>

              </div>
            </template>

            <!-- <template v-slot:[`header.name`] >
              <span @click="fixColumn()">Наименование</span>
            </template> -->

            <template v-slot:item="{ item }">
              <tr>

                <!-- "Название" -->
                <td class="cursor-default fixed-col">
                  <div class="text-no-wrap">
                    <router-link :to="{name: 'component' , params:{ id: item.name.replaceAll(' ', '_') }}" target="_blank" class="componentLink">
                      <span>{{ item.name}}</span>
                    </router-link>
                  </div>
                </td>

                <!-- "Производитель" -->
                <td v-if="selectedHeaders.some(e => e.value === 'brand')" class="cursor-default  text-no-wrap"><div>{{ item.brand }}</div></td>

                <!-- "Страна производителя" -->
                <td v-if="selectedHeaders.some(e => e.value === 'country')" class="cursor-default text-center">
                  <div class="d-flex align-center">
                    <!-- <countryFlag :country="countryFlag(item.country)" size='normal' class="ma-0 pa-0"/> -->
                    <span>{{ item.country }}</span>
                  </div>
                </td>

                <!-- "SubFamily" -->
                <td v-if="selectedHeaders.some(e => e.value === 'subFamily')" class="text-justify text-no-wrap">             
                  <div class="text-caption">{{ item.subFamily }}</div>
                </td>

                <!-- "CPU" -->
                <td v-if="selectedHeaders.some(e => e.value === 'CPU')" class="text-justify text-no-wrap">             
                  <div class="text-caption">
                    <span>{{ item.CPU.join(', ') }}</span>
                  </div>
                </td>

                <!-- "Frequency (MHz)" -->
                <td v-if="selectedHeaders.some(e => e.value === 'frequency')" class="text-justify text-no-wrap">             
                  <span class="text-caption">{{ item.frequency.join(', ') }}</span>
                </td>

                <!-- "Flash memory (KB)" -->
                <td v-if="selectedHeaders.some(e => e.value === 'flashMemory')" class="text-justify text-no-wrap">             
                  <span class="text-caption">{{ item.flashMemory.join(', ') }}</span>
                </td>

                <!-- "RAM (KB)" -->
                <td v-if="selectedHeaders.some(e => e.value === 'RAM')" class="text-justify text-no-wrap">             
                  <div class="text-caption">{{ item.RAM.join(', ') }}</div>
                </td>

                <!-- "ADC" -->
                <td v-if="selectedHeaders.some(e => e.value === 'ADC')" class="text-justify text-no-wrap">             
                  <div class="text-caption">{{ item.ADC.join(', ') }}</div>
                </td>

                <!-- "GPIO" -->
                <td v-if="selectedHeaders.some(e => e.value === 'GPIO')" class="text-justify text-no-wrap">             
                  <div class="text-caption">{{ item.GPIO.join(', ') }}</div>
                </td>

                <!-- "Communication interface" -->
                <td v-if="selectedHeaders.some(e => e.value === 'commInterface')" class="text-justify text-no-wrap">             
                  <div class="text-caption">{{ item.commInterface.join(', ') }}</div>
                </td>

                <!-- "UART" -->
                <td v-if="selectedHeaders.some(e => e.value === 'UART')" class="text-justify text-no-wrap">             
                  <div class="text-caption">{{ item.UART }}</div>
                </td>

                <!-- "Features" -->
                <td v-if="selectedHeaders.some(e => e.value === 'features')" class="text-justify text-no-wrap">             
                  <div class="text-caption">{{ item.features.join(', ') }}</div>
                </td>

                <!-- "Package Group" -->
                <td v-if="selectedHeaders.some(e => e.value === 'packageGroup')" class="text-justify text-no-wrap">       
                  <div v-for="pack in item.packageGroup" :key="pack">
                    <span>{{ pack.type }}</span> | <span>{{ pack.pinsNum }}</span>
                  </div>
                </td>

                <!-- "Operating temperature range" -->
                <td v-if="selectedHeaders.some(e => e.text === 'Operating temperature range')" class="text-justify text-no-wrap">             
                  <div class="text-caption">{{ item.tempMin }} - {{ item.tempMax }}</div>
                </td>


                <!-- ACTIONS -->
                <td>
                  <div class="d-flex">
                    <v-btn icon small color="cyan" @click="editItem(item)">
                      <v-icon>mdi-pencil</v-icon>
                    </v-btn>
                    <v-btn icon small color="pink" @click="deleteItem(item)">
                      <v-icon>mdi-delete</v-icon>
                    </v-btn>
                  </div>
                </td>


                <!-- Раскрывашка графика -->
                <!-- <td class="py-3">
                  <v-btn icon 
                        @click="expand(!isExpanded)" 
                        class="v-data-table__expand-icon"
                        :class="{'v-data-table__expand-icon--active' : isExpanded}">
                    <v-icon></v-icon>
                  </v-btn>
                </td> -->

              </tr>
            </template>
          
            <template v-slot:[`footer.page-text`]="items"> 
              {{ items.pageStart }} - {{ items.pageStop }} из {{ items.itemsLength }} 
            </template>

          </v-data-table>

        </v-card>

        <!-- <br><br>
        <strong>filters: </strong> {{filters}}<br><br>
        <strong>activeFilters: </strong> {{activeFilters}}<br><br>
        <strong>filtersMerged: </strong> {{filtersMerged}}<br><br>
        {{filters.frequency}} -->
      </v-col>
    </v-row>



  </v-container>
</template>

<script>
  import microcontrollersJson from '../microcontrollers.json'

  export default {
    name: 'ComponentsView',

    components: {
      // HelloWorld,
    },

    data() {
      return {
        search: '',
        components: [],

        headers: [
          {id: 1, text: "Наименование", value: "name", class: "fixed-column"},
          {id: 2, text: "Производитель", value: "brand", },
          {id: 3, text: "Страна производителя", value: "country", align: "center"},
          
          {id: 4, text: "SubFamily", value: "subFamily", align: "center"},
          {id: 5, text: "CPU", value: "CPU", align: "center"},
          {id: 6, text: "Frequency (MHz)", value: "frequency", align: "center"},   
          {id: 7, text: "Flash memory (KB)", value: "flashMemory", align: "center"},
          {id: 8, text: "RAM (KB)", value: "RAM", align: "center"},
          {id: 9, text: "ADC", value: "ADC", align: "center"},   
          {id: 10, text: "GPIO", value: "GPIO", align: "center"},        
          {id: 11, text: "Communication interface", value: "commInterface", align: "center"},
          {id: 12, text: "UART", value: "UART", align: "center"},
          {id: 13, text: "Features", value: "features", align: "center"},
          
          {id: 14, text: "Package Group", value: "packageGroup", align: "center"},
          {id: 15, text: "Operating temperature range", value: "", align: "center"},
          {id: 16, text: "", value: "actions"},
        ],
        // selectedHeaders: [],
        checkedHeaders: [],
        headersFilterList: [],

        filters: { 
          brand: [], 
          country: [],
          subFamily: [],
          CPU: [],
          frequency: [],
          flashMemory: [],

          RAM: [],
          ADC: [],
          GPIO: [],
          commInterface: [],
          UART: [],
          features: [],
          packageGroup: [],
        },
        filtersNames: [],
        activeFilters: {},

        filtersMerged: [],
        activeFiltersMerged: {},
      }    
    },
    
    computed: {
      selectedHeaders() {
        let headers = [
          {id: 1, text: "Наименование", value: "name", class: "fixed-col"},
        ]

        if (this.checkedHeaders.some(e => e.value === 'brand')) {
          headers.push({id: 2, text: "Производитель", value: "brand", filter: v => { return this.activeFilters.brand ? this.activeFilters.brand.includes(v) : true;}, class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'country')) {
          headers.push({id: 3, text: "Страна производителя", value: "country", filter: v => { return this.activeFilters.country ? this.activeFilters.country.includes(v) : true;}, class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'subFamily')) {
          headers.push({id: 4, text: "SubFamily", value: "subFamily", filter: v => { return this.activeFilters.subFamily ? this.activeFilters.subFamily.includes(v) : true;}, class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'CPU')) {     
          headers.push({id: 5, text: "CPU", value: "CPU", filter: v => { return this.activeFilters.CPU ? this.activeFilters.CPU.some( r => v.includes(r) ) : true;}, class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'frequency')) {
          headers.push({id: 6, text: "Frequency (MHz)", value: "frequency", filter: v => { return this.activeFilters.frequency ? this.activeFilters.frequency.some( r => v.includes(r) ) : true;}, class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'flashMemory')) {
          headers.push({id: 7, text: "Flash memory (KB)", value: "flashMemory", filter: v => { return this.activeFilters.flashMemory ? this.activeFilters.flashMemory.some( r => v.includes(r) ) : true;}, class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'RAM')) {
          headers.push({id: 8, text: "RAM (KB)", value: "RAM", filter: v => { return this.activeFilters.RAM ? this.activeFilters.RAM.some( r => v.includes(r) ) : true;},  class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'ADC')) {
          headers.push({id: 9, text: "ADC", value: "ADC", filter: v => { return this.activeFilters.ADC ? this.activeFilters.ADC.some( r => v.includes(r) ) : true;},  class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'GPIO')) {
          headers.push({id: 10, text: "GPIO", value: "GPIO", filter: v => { return this.activeFilters.GPIO ? this.activeFilters.GPIO.some( r => v.includes(r) ) : true;}, class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'commInterface')) {
          headers.push({id: 11, text: "Communication interface", value: "commInterface", filter: v => { return this.activeFilters.commInterface ? this.activeFilters.commInterface.some( r => v.includes(r) ) : true;}, class: "text-no-wrap"})
        }      
        if (this.checkedHeaders.some(e => e.value === 'UART')) {
          headers.push({id: 12, text: "UART", value: "UART", filter: v => { return this.activeFilters.UART ? this.activeFilters.UART.some( r => v.includes(r) ) : true;},  class: "text-no-wrap"})
        }
        if (this.checkedHeaders.some(e => e.value === 'features')) {
          headers.push({id: 13, text: "Features", value: "features", filter: v => { return this.activeFilters.features ? this.activeFilters.features.some( r => v.includes(r) ) : true;},  class: "text-no-wrap"})
        } 
        if (this.checkedHeaders.some(e => e.value === 'packageGroup')) {
          headers.push({id: 14, text: "Package Group", value: "packageGroup", filter: v => { return this.activeFilters.packageGroup ? this.activeFilters.packageGroup.some( r => v.includes(r) ) : true;},  class: "text-no-wrap"})
        }


        // if (this.checkedHeaders.some(e => e.text === 'Operating temperature range')) {
        //   headers.push({id: 15, text: "Operating temperature range", value: "", align: "center", class: "text-no-wrap"})
        // }             

        headers.push({id: 16, text: "", value: "actions"})

        return headers
      }
    },

    watch: {
      components () {
        this.initFilters()
        //this.activeFilters = {} // TODO change this
        //this.activeFilters = Object.assign({}, this.filters)
      }
    },

    mounted() {
      document.title = "Микроконтроллеры"
      this.initialize()
      
      // this.filtersNames = Object.keys(this.filters)

      this.headersFilterList = this.headers.filter(v => v.value !="name" && v.value !="actions")
      this.checkedHeaders = this.headersFilterList
      
      var elements = document.getElementsByClassName("v-data-table__wrapper");
      elements[0].addEventListener('scroll', this.fixColumn)
      
      
    },

    methods: {


      initialize () {
        this.components = microcontrollersJson.microcontrollers
        //this.initFilters()
      },

      // Формирование значений в фильтрах
      initFilters() {
        // for (let col in this.filters) {
        //   this.filters[col] = this.components.map((d) => { return d[col] || d.params[col] }).filter(
        //     (value, index, self) => { return self.indexOf(value) === index }
        //   )
        // }
        // this.activeFilters = Object.assign({}, this.filters)
        
        // this.filtersMerged = this.filtersMerged.concat.apply([], this.filters.frequency)
        
        
        for (let col in this.filters) {
          
          this.filters[col] = this.components.map((d) => { return d[col] })
          this.filters[col] = this.filters[col].concat.apply([], this.filters[col]).filter(
            (value, index, self) => { return self.indexOf(value) === index }
          )

        }


        this.activeFilters = Object.assign({}, this.filters)
        
        this.filtersMerged = this.filtersMerged.concat.apply([], this.filters.frequency)
      },

      // Выбор всех значений фильтра
      toggleAll (col) {
        this.activeFilters[col] = this.components.map((d) => { return d[col] }).filter(
          (value, index, self) => { return self.indexOf(value) === index }
        )
      },
      // Очищение списка выбранных значений фильтра
      clearAll (col) {
        this.activeFilters[col] = []
      },

      // Фиксирует столбец при скролле таблицы 
      fixColumn(e) {
        console.log("Привет, я вызвалась")
        let fixedCol = document.getElementsByClassName('fixed-col')
        let scrollPosition = e.target.scrollLeft;
        console.log(scrollPosition)
        if (scrollPosition != 0) {
          for (var i = 0; i < fixedCol.length; i++) {
            fixedCol[i].classList.add("fixed-col-active")
          }  
        } else {
          for (i = 0; i < fixedCol.length; i++) {
            fixedCol[i].classList.remove("fixed-col-active")
          }                 
        }       
      }

      // fixColumn() {
      //   var elements = document.getElementsByClassName("v-data-table__wrapper");

      //   elements[0].addEventListener('scroll',  function(e) {
      //     let fixedCol = document.getElementsByClassName('fixed-col')
      //     let scrollPosition = e.target.scrollLeft;

      //     if (scrollPosition != 0) {
      //       for (var i = 0; i < fixedCol.length; i++) {
      //         fixedCol[i].classList.add("fixed-col-active")
      //       }  
      //     } else {
      //       for (i = 0; i < fixedCol.length; i++) {
      //         fixedCol[i].classList.remove("fixed-col-active")
      //       }                 
      //     }
      //   })
      // }
    },
  }
</script>


<style scoped>
.active-header-item::before { /* Убирает выделение активного элемента */
  opacity: 0 !important;
}

.filters-header-list { /* Высота списка headers в фильтрах */
  max-height: 300px;
  overflow-y: scroll; 
}

>>>.v-list-item__action { /* Убирает странный отступ у чекбоксов в списках фильтров */
  margin-right: 8px !important;
}

>>>.v-expansion-panel-header--active {
  font-weight: 500 !important;
}

.exp-panel-header-active:hover {
  /* background: #FF9E80; */
}
/* .components-table .v-data-table-header */


</style>