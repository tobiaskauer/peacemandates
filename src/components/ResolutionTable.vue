<template>
    <v-container fluid>
        <span style="font-size: 18pt">Listing <strong>{{ sortedResolutions.length }} resolution<span v-if="sortedResolutions.length > 1">s</span></strong>  in <strong>{{ mandates.length }} mission<span v-if="mandates.length > 1">s</span></strong></span>
        
        <div class="v-data-table__wrapper">
        <v-table fixed-header height="100vh" v-if="sortedResolutions">
            <thead>
                <tr>
                <th class="v-data-table__td v-data-table-column--align-start v-data-table__th v-data-table__th" v-for="header in headers" :key="header.key">
                    <span class="big">
                        <v-btn variant="plain" style="padding: 0" @click="setSort(header.key)" size="small">
                            {{ header.text }}
                            <span v-if="sortBy && sortBy.key == header.key">
                                <v-icon v-if="sortBy.asc" icon="mdi-arrow-down"></v-icon>
                                <v-icon v-else icon="mdi-arrow-up"></v-icon>
                            </span>
                        </v-btn>
                    </span>
                </th>
                
                </tr>
            </thead>
            
            <tbody>
            <tr v-for="resolution in sortedResolutions" :key="resolution.Signature">
                <td v-for="header in headers" :key="header.key">{{ resolution[header.key] }}</td>
            </tr>
            </tbody>
        </v-table>
    </div>
    </v-container>
  </template>
  
  <script setup>
import {defineProps, computed, ref} from 'vue'
  const props = defineProps(['resolutions','filters','tasks'])
  const sortBy =  ref({key: null, asc: true})
  const headers = ref([
    {text: "Signature", key: "Signature", description: "Unique Identifier"},
    {text: "Year", key: "Year"},
    {text: "Country", key: "Country"},
  ])

  props.tasks.forEach(task => {
    headers.value.push({text: task.text, key: task.key,})
    //TODO: Filter empty columns
  })

//SORT WHEN CLICKED ON TABLE HEADER
  const sortedResolutions = computed(() => {
    if(props.resolutions.length < 1) return false
    const resolutions = props.resolutions

    if(sortBy.value && sortBy.value.key) {
        resolutions.sort((a,b) => {
            let isNumeric = typeof a[sortBy.value.key] == "number" ? true : false //determine column type to set comparelocale to numeic if needed
            let reverseFactor = sortBy.value.asc ? 1 : -1 //factor -1 for desc sorting
            let compareResult = a[sortBy.value.key].toString().localeCompare(b[sortBy.value.key], undefined, {numeric: isNumeric  })
            return compareResult * reverseFactor
        })
        
    }
    return resolutions
  })

  const mandates = computed(() => {
    let allMandates = props.resolutions.map(resolution => resolution.NamePKO)
    return [...new Set(allMandates)]

  })

  const setSort = ((key) => {
    if(sortBy.value.key == key) {
        sortBy.value.asc = !sortBy.value.asc
    } else {
        sortBy.value.key = key
        sortBy.value.asc = true
    }
  })
  </script>
  
<style>
    th .big { 
        vertical-align: top;
        white-space: nowrap;
        overflow: hidden;
        text-overflow:ellipsis; 
    }
    th .small {
        font-size: 8pt;
        font-weight: normal;
    }

    ._Assist {
        background: #dc267f;
    }

    ._Monitor {
        background: #648fff;
    }

    ._Security {
        background: #fe6100;
    }

    ._Encouraged {
        background: #ffb000;
    }

</style>