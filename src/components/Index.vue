<template>
  <v-app id="inspire">
     <!-- // Nav Drawer -->
    <v-navigation-drawer fixed v-model="drawer" app>
      
        <v-list dense>
          <v-list-tile @click="">
            <v-list-tile-action>
              <v-icon>layers</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>Categories</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-expansion-panel>
            <v-expansion-panel-content v-for="(pCat,i) in parentCat" :key="i">
              <div slot="header" :key="pCat['jcr:title']">{{pCat['jcr:title']}}</div>
              <v-expansion-panel>
                <div class="child" v-for="(subCati, k) in getSubCat(pCat)" :key="k">
                  <v-expansion-panel-content >
                    <div slot="header" :key="subCati['jcr:title']">{{subCati['jcr:title']}}</div>
                      <v-expansion-panel>
                        <div class="child2" v-for="(subCatl2, v) in getSubCat(subCati)" :key="v">
                          <v-expansion-panel-content hide-actions >
                            <div v-on:click="changeProducts(subCatl2['jcr:title'])" slot="header" :key="subCatl2['jcr:title']">{{subCatl2['jcr:title']}}</div>
                          </v-expansion-panel-content>
                        </div>        
                      </v-expansion-panel>
                  </v-expansion-panel-content>
                </div>        
              </v-expansion-panel>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-list>

        <v-bottom-sheet inset>
          <v-btn slot="activator" color="red" dark>Filters</v-btn>
          <v-card tile>
            
            <v-list>
              <v-layout row wrap>
                <v-flex xs9>
                  <v-slider label="Price" :max="10000" v-model="price"></v-slider>
                </v-flex>
                <v-flex xs3>
                  <v-text-field v-model="price" type="number"></v-text-field>
                </v-flex>
              </v-layout>
            </v-list>
          </v-card>
        </v-bottom-sheet>
    </v-navigation-drawer>

    

    <!-- // Right Content -->
    <v-toolbar color="indigo" dark fixed app>
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-toolbar-title>Products</v-toolbar-title>
    </v-toolbar>
    <v-content>
      <v-container fluid fill-height>
        <v-layout
          justify-center
          align-center
        >
          <v-flex text-xs-center>
            <v-card>
              <v-container fluid grid-list-md>
                <v-layout row wrap>
                  <v-flex
                    v-bind="{ [`xs4`]: true }"
                    v-for="product in filteredProducts"
                    :key="product.title"
                  >
                    <v-card>
                      <v-card-media
                        v-if="product.display_image"
                        :src="'/static' + product.display_image"
                        height="400px"
                      >

                      </v-card-media>
                      <v-card-title primary-title>
                        <div>
                          <div class="headline">{{product['display_name']}}</div>
                          <span class="grey--text">${{product.price}}</span> 
                        </div>
                      </v-card-title>
                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn icon>
                          <v-icon>favorite</v-icon>
                        </v-btn>
                        <v-btn icon>
                          <v-icon>bookmark</v-icon>
                        </v-btn>
                        <v-btn icon>
                          <v-icon>share</v-icon>
                        </v-btn>
                        <v-btn icon @click.native="show = !show">
                          <v-icon>{{ show ? 'keyboard_arrow_down' : 'keyboard_arrow_up' }}</v-icon>
                        </v-btn>
                      </v-card-actions>
                      <v-slide-y-transition>
                        <v-card-text v-show="show" v-html="product['description']"> 
                        </v-card-text>
                      </v-slide-y-transition>
                    </v-card>
                  </v-flex>
                </v-layout>
              </v-container>
              <v-alert class='warning' v-if="filteredProducts.length <= 0" type="warning" :value="true">
                Sorry no Products present...
              </v-alert>
            </v-card>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
    
    <!-- // Footer -->
    <v-footer color="indigo" app>
      <v-flex xs12 py-3 text-xs-center white--text>
          &copy;2018 — <strong>JennAir</strong>
        </v-flex>
    </v-footer>
  </v-app>
</template>

<script>
import ExpansionList from '../components/ExpansionList'
import EventBus from '../event-bus'
import * as JsonLoaded from '../../static/jenn_air.infinity.json';
// const readJsonSync = require('read-json-sync');

export default {
  name: 'Index',
  data() {
    return {
      drawer: null,
      source: null,
      parentCat: [],
      subCat: [],
      subCatL2: [],
      products: [],
      price: 2000,
      show: true
    }
  },
  computed: {
     filteredProducts: function () {
      let filteredProducts = []
      for(var product of this.products){
        if (product.price <= this.price){
          filteredProducts.push(product)
        }
      }
      return filteredProducts
    }
  },
  methods: {
    loadJSON: function(callback) {
      var xobj = new XMLHttpRequest()
      xobj.overrideMimeType("application/json")
      xobj.open('GET', '/static/products.json', true);
      xobj.onreadystatechange = function () {
            if (xobj.readyState == 4 && xobj.status == "200") {
              // Required use of an anonymous callback as .open will NOT return a value but simply returns undefined in asynchronous mode
              callback(xobj.responseText);
            }
      };
      xobj.send(null)
    },
    keysThatMatch: function (data) {
      // console.log("KEYS THAT MATCH")
      let pattern = "^[^:]*$"
      return Object.keys(data).filter(key => key.match(pattern))
    },
    log: (something) => {
      console.log(something)
    },
    changeProducts: function(someData) {
      console.log("NEWWWWW",someData)
      console.log("SUBACAT", this)
      console.log("This is absurd", this.products)
      // console.log(this.data.products)
      for(var sVal of this.subCatL2){
        if (sVal['jcr:title'] == someData){
          console.log("Ohhhh they match")
          this.products = []
          Object.keys( sVal ).forEach( key => {
              if (key.match("^[^:]*$")) {
                this.products.push(sVal[key])
              }
            })
        }
      }
    },
    getSubCat: function(obj) {
      console.log("INSIDE SUBCAT", obj)
      let subCat = []
      Object.keys( obj ).forEach( key => {
              if (key.match("^[^:]*$")) {
                subCat.push(obj[key])
              }
            })
      console.log("THE SUBCAT IS ", subCat)      
      return subCat
    }
  },
  // mounted: () =>
  // { 
  //   EventBus.$on('myEvent', (someData)=>{
  //     console.log(this.data)
  //     console.log("INSIDE PARENT", someData)
  //     for(var sVal of someData.data.subCat){
  //       if (sVal['jcr:title'] == someData.data.subCatKey){
  //         console.log("Ohhhh they match")
  //         this.products = sVal
  //       }
  //     }
  //     // console.log("NEW KEYSsss",Object.keys(this.subCat))
  //     // console.log(this.subCat.hasOwnProperty(someData.data))
  //   })
  // },
  mounted() {
    
    
    // let jsonresponse = jsonreadJsonSync('/Users/kwanso-ios/Documents/Projects/vue-aem/static/products.json');
  
    let jsonresponse = JsonLoaded  

    let keys = this.keysThatMatch(jsonresponse)
    
    Object.keys(jsonresponse).forEach( key => {
      let pattern = "^[^:]*$"
      if (key.match(pattern)){
        // console.log( "black", jsonresponse[key] );
        this.parentCat.push(jsonresponse[key])
        Object.keys( jsonresponse[key] ).forEach( key2 => {
          if (key2.match(pattern)) {
            this.subCat.push(jsonresponse[key][key2])
            // console.log( jsonresponse[key][key2])
            Object.keys( jsonresponse[key][key2] ).forEach( key3 => {
              if (key3.match(pattern) && key3 != "display_image") {
                this.subCatL2.push(jsonresponse[key][key2][key3])
                Object.keys( jsonresponse[key][key2][key3] ).forEach( key4 => {
                  if (key4.match(pattern) && key4 != "display_image") {
                    this.products.push(jsonresponse[key][key2][key3][key4])
                    // console.log( jsonresponse[key][key2][key3])
                  }
                })
                // console.log( jsonresponse[key][key2][key3])
              }
            })
          }
        })
      }
    });
    
    // for(var val of keys){
    //   // self.parentCat.push(jsonresponse[val])
    //   let suKeys = this.keysThatMatch(jsonresponse[val])
    //   for(var va of suKeys){
    //     // this.subCat.push(jsonresponse[val][va])
    //     let prodKeys = this.keysThatMatch(jsonresponse[val][va])
    //     for(var pk of prodKeys){
    //       // this.products.push(jsonresponse[val][va][pk])
    //     }
    //   }
    // }
      
    }
  
}
</script>

<style>
.child {
  width: 100%;
  
}
.child2 {
  width: 100%;
  
}
.child .expansion-panel__header
{
  background-color: lightslategray;
}
.child2 .expansion-panel__header
{
  background-color: lightgrey;
}
span.grey--text{
  float: left;
}

.card.card--tile{
  padding-left: 70px;
    padding-right: 70px;
}
.warning{
      margin-top: -15px;
}
</style>
