<template>
    <v-app>
        <v-container>
            <h1 class="text-center mt-6 mb-6">Furniture Finder</h1>
            <input type="text" placeholder="Search Furniture..." v-model="search" class="text-center underlined col-12 mb-5 mb-0">
            <div>
                <v-row>
                    <div class="col-lg-6 col-md-6 col-sm-12">
                        <v-autocomplete
                                v-model="style"
                                :items="items"
                                outlined
                                dense
                                chips
                                small-chips
                                label="Furniture Style"
                                multiple
                                class="ma-5 mb-0"
                        ></v-autocomplete>
                    </div>
                    <div class="col-lg-6 col-md-6 col-sm-12">
                        <v-autocomplete
                                v-model="times"
                                :items="deliveryTime"
                                outlined
                                dense
                                chips
                                small-chips
                                label="Delivery Time (Days)"
                                multiple
                                class="ma-5 mb-0"
                        ></v-autocomplete>
                    </div>
                </v-row>
                <button class="button ml-5" @click="resetFilter">Reset</button>
            </div>
            <div v-if="data.length === 0" class="text-center mt-6 mb-6"> Loading... </div>
            <v-row style="height: 400px">
                <div v-for="(item,index) in filteredProduct"
                     :key="index" class="col-md-6 col-sm-12">
                    <v-card outlined class="ma-5">
                        <v-list-item three-line>
                            <v-list-item-content class="pa-5">
                                <v-list-item-title class="row upper-section pa-3">
                                    <b>{{ item.name }}</b>
                                    <span>IDR {{ item.price }}</span>
                                </v-list-item-title>
                                <v-list-item-subtitle class="subtitle">{{ item.description }}</v-list-item-subtitle>
                                <div class="row">
                                    <div v-for="(style,index) in item.furniture_style" :key="index" class="pa-3">
                                        <small style="color: #FFD441; font-weight: 600">{{ style }}</small>
                                    </div>
                                </div>
                                <small class="pa-1 pb-2 text-right">{{ item.delivery_time }} Day(s)</small>
                            </v-list-item-content>
                        </v-list-item>
                    </v-card>
                </div>
            </v-row>
        </v-container>
    </v-app>
</template>

<script>
    import axios from 'axios'
    export default {
        name: 'App',
        data: () => ({
            data: [],
            search: '',
            items: [],
            style: [],
            times: [],
            deliveryTime: []
        }),

        created() {
          this.getDatas()
        },

        computed: {
          filteredProduct() {
              return this.data.filter( datas => {
                  if ( this.search.length > 0 ) {
                      return datas.name.toLowerCase().match(this.search.toLowerCase())
                  } else if ( this.style.length > 0 ) {
                      for ( let i = 0; i < this.style.length; i++ ) {
                          if ( datas.furniture_style.includes(this.style[i]) === true ) {
                              return datas
                          }
                      }
                  } else if ( this.times.length > 0 ) {
                      for ( let i = 0; i < this.times.length; i++ ) {
                          return datas.delivery_time.match(this.times[i])
                      }
                  } else {
                      return datas
                  }
              })
          }
        },

        methods: {
          resetFilter() {
            this.style = []
            this.times = []
          },

          getDatas() {
              axios({
                  method: 'POST',
                  url : 'http://www.mocky.io/v2/5c9105cb330000112b649af8',
                  headers: {
                      'Accept': 'application/json, text/plain, */*',
                      'Content-Type': 'application/json'
                  },
                  data: []
              })
                  .then( response => {
                      response.data.products.map( item => {
                          this.data.push(item)
                          this.deliveryTime.push(item.delivery_time)
                      });
                      this.items = response.data.furniture_styles
                      this.deliveryTime = this.deliveryTime.sort((a, b) => a - b)
                  //    eslint-disable-next-line no-console
                  //     console.log(this.deliveryTime)
                  })
          }
        }
    };
</script>

<style>
    .underlined {
        border-bottom: thin solid rgba(0, 0, 0, 0.12);
    }

    .button {
        background-color: #FFD441;
        color: #333333;
        padding: 10px;
        font-size: 13px;
        cursor: pointer;
        width: 100px;
    }

    .upper-section {
        display: inline-flex;
        justify-content: space-between;
        align-items: center;
    }

    .upper-section b {
        font-size: 20px;
    }

    .upper-section span {
        font-weight: bold;
        color: #FFD441;
    }

    .subtitle {
        white-space: nowrap;
        overflow: hidden;
        text-overflow: ellipsis;
    }
</style>
