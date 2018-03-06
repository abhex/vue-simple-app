<template>
  <v-expansion-panel>
    <v-expansion-panel-content v-for="(pCat,i) in pCategories" :key="i">
      {{log("Inside", pCat)}}
      <div slot="header" :key="pCat['jcr:title']">{{pCat['jcr:title']}}</div>
      <v-card>
        <v-expansion-panel focusable>
          <div v-for="(subCat, k) in getSubCat(pCat)" :key="k">
            {{log("INSIDE 2", subCat)}}
            <v-expansion-panel-content >
              <div v-on:click="warn(subCat['jcr:title'], $event)" slot="header" :key="subCat['jcr:title']">{{subCat['jcr:title']}}</div>
            </v-expansion-panel-content>
          </div>
        </v-expansion-panel>
      </v-card>
    </v-expansion-panel-content>
  </v-expansion-panel>
</template>



<script>
import EventBus from '../event-bus';

export default {
  name: 'ExpansionList',
  data () {
    return {
      subCatNnl: [5, 6]
    }
  },
  props: ['pCategories', 'sCategories'],
  methods: {
    log: (identifier, something) => {
      console.log(identifier, something)
    },
    pushToSubCat: (value) => {
      console.log("SUBCAT",value)
      // console.log("SUBCAT",subCat)
      console.log("this inside 2",this)
      this.subCat.push(value)
    },
    getSubCat: (obj) => {
      let subCat = []
      Object.keys( obj ).forEach( key => {
              if (key.match("^[^:]*$")) {
                subCat.push(obj[key])
              }
            })
      return subCat
    },
    warn: function (message, event) {
      
      console.log("EVENNNNNNTT", event)

      EventBus.$emit('myEvent', {data: {subCatKey: message, subCat: this.sCategories}})
      alert(message)
  }
  }
}
</script>

