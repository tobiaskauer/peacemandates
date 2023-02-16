<template>
  <v-container>
    <div class="d-flex justify-end">
    <v-btn size="x-small" variant="outlined" @click="emit('hideFilters')">Hide Filters</v-btn>
  </div>
    <v-row>
      <v-col>
<strong>Mandates</strong>
<div class="filterContainer">
              <v-chip-group
                v-model="selectedMandates"
                column
                active-class="text-primary"
                multiple
                >
                <v-chip 
                  size="small"
                  v-for="mandate in mandates"
                  :value="mandate"
                  :key="mandate">{{mandate}}</v-chip>
              </v-chip-group>
            </div>
              </v-col>
              
              </v-row>
        <!--      <v-row>
      <v-col>
<strong>Countries</strong>
              <v-chip-group
                v-model="selectedCountries"
                column
                selected-class="text-primary"
                multiple
                >
                <v-chip 
                  size="small"
                  v-for="country in countries"
                  :value="country"
                  :key="country">{{country}}</v-chip>
              </v-chip-group>
              </v-col>
              
              </v-row>-->
              <v-row>
      <v-col>
<div class="mb-4"><strong>Years</strong></div>
<v-range-slider
  :min="1991"
  :max="2019"
  step="1"
  thumb-label="always"
  thumb-size="10"
  v-model="selectedYears"
></v-range-slider>
              </v-col>
              
              </v-row>
              <v-row>
      <v-col>
<strong>Tasks</strong>
<div class="filterContainer">
              <v-chip-group
                v-model="selectedTasks"
                column
                selected-class="text-primary"
                multiple
                >
                <v-chip 
                  size="small"
                  v-for="task in props.tasks"
                  :value="task.key"
                  :key="task.key">{{task.text}}</v-chip>
              </v-chip-group>
            </div>
              </v-col>
              
              </v-row>
  </v-container>
</template>

<script setup>
import {ref, watch, defineProps, defineEmits, computed, onMounted} from 'vue';
const props = defineProps(['resolutions', 'mandates','tasks'])
const emit = defineEmits(['changeFilters','hideFilters'])

let selectedYears = ref([]) //RangeSlider needs primitife array, will not work with rective(). 
//const selectedCountries = ref([])
const selectedTasks = ref([])
const selectedMandates  = ref([])



const mandates = computed(() => {
  let allMandates = props.resolutions.map(resolution => resolution.NamePKO)
  return [...new Set(allMandates)]
})

let filters = {
  mandates: [],
  years: [],
  tasks: []
}

function emitFilters(facette) {
  Object.keys(facette).forEach(key => filters[key] = facette[key])
  emit("changeFilters", filters)

}

watch(selectedYears, (timeFilter) => emitFilters({years: timeFilter}))
//watch(selectedCountries, (selectedCountries) => emitFilters({countries: selectedCountries}))
watch(selectedMandates, (selectedMandates) => emitFilters({mandates: selectedMandates}))
watch(selectedTasks, (selectedTasks) => emitFilters({tasks: selectedTasks}))
//TODO: adapt to other filters



onMounted(() => {
  //figure out date range of resolutions, do that manually (for now) to avoid reactivity chaos
  let allYears = props.resolutions.map(resolution => resolution.Year)
  selectedYears.value = [Math.min(...allYears),Math.max(...allYears)]

  
})


</script>

<style scoped>
.filterContainer {
  height:120px;
  overflow-y: scroll
}
</style>