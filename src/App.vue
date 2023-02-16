<template>
<v-app>
  <v-overlay v-model="store.overlay" class="justify-center align-center">
      <v-card class="mx-auto" max-width="800px" light>
        <v-img
      class="align-end text-white"
      height="150"
      src="https://ssl.gstatic.com/atari/images/impression-header.png"
      cover
    > 
    
    <v-card-title>The <strong>Peacekeeping Mandates</strong> Dataset</v-card-title>
  </v-img>
    <!--<v-card-subtitle class="pt-4">
      Visualizing Conflict Resolution Tasks
    </v-card-subtitle>-->
    <v-card-text>
      <div><p style="padding: 10px; border-radius: 10px; border: 1px dashed orange; font-family:monospace; color: orange">Di Salvatore, Jessica, Lundgren, Magnus, Oksamytna, Kseniya, & Smidt, Hannah M. (2022). Introducing the Peacekeeping Mandates (PEMA) Dataset. <a href="https://doi.org/10.1177/00220027211068897">Journal of Conflict Resolution</a> 66 (4-5): 924-951.</p></div>
      <p>Since the end of the Cold War, UN peacekeeping operations have been given an increasing num-ber of ambitious tasks, ranging from promoting human rights to organizing elections. </p>
      <p>Drawing on UN Security Council resolutions that establish, extend, or revise mandates of 48 UN peacekeeping operations in 1991–2016, the Peacekeeping Mandates (PEMA) Dataset records 41 distinct tasks, three modalities of engagement (monitoring, assisting, and securing), and whether the task is requested or encouraged.</p>
      <p>The Codebook and the data (with future updates) can be found on the <a href="https://dataverse.harvard.edu/dataset.xhtml?persistentId=doi:10.7910/DVN/OT14Z9">Harvard Dataverse</a>.</p>
      <v-card-actions>
      <v-btn block variant="outlined" @click="store.overlay = !store.overlay">
        Explore Peacekeeping Mandates
      </v-btn>
    </v-card-actions> 
    </v-card-text>
      </v-card>
      
  </v-overlay>
  
    <v-app-bar
       app
       clipped-left
       color="primary"
       dark
     >
        <v-btn @click="store.showFilters = !store.showFilters" icon="mdi-menu"></v-btn>
        <v-toolbar-title>
          Peacekeeping Mandates Dataset
          <v-btn @click="store.overlay = !store.overlay" icon="mdi-information"></v-btn>
        </v-toolbar-title>
 
        <v-spacer></v-spacer>

        <v-btn-toggle
        style="padding-right: 20px"
        
      v-model="store.view"
      color="primary"
      mandatory
      size="x-small"
    >
      <v-btn size="small" value="vis"><v-icon icon="mdi-chart-timeline"></v-icon>Visualization</v-btn>
      <v-btn size="small" value="table"><v-icon icon="mdi-format-align-center"></v-icon>Table</v-btn>
    </v-btn-toggle>
     </v-app-bar>
      <v-navigation-drawer
       v-model="store.showFilters"
       width="400"
     >
    
     <ResolutionFilters
      v-if="resolutions.length > 0"
      :resolutions="resolutions"
      :filters="filters"
      :tasks="tasks"
      @change-filters="updateFilters"
      @hide-filters="store.showFilters = !store.showFilters"/>
     </v-navigation-drawer>
       <v-main>
          

        <VisualFingerprint v-if="store.view == 'vis'" :resolutions="filteredResolutions" :tasks="tasks" :modalities="modalities" :activeTasks="filters.tasks"/>
        <ResolutionTable v-if="store.view == 'table'" :resolutions="filteredResolutions" :filters="filters" :tasks="tasks"/>
   </v-main>
   </v-app>
</template>

<script setup>
import ResolutionFilters from './components/ResolutionFilters';
import ResolutionTable from './components/ResolutionTable';
//import VisualFingerprint from './components/VisualFingerprint';

import { ref, reactive, computed, onMounted } from 'vue'
import VisualFingerprint from './components/VisualFingerprint.vue';
import { useTheme } from "vuetify";

const theme = useTheme();
theme.global.name.value = "dark"


const resolutions = ref([])
let store = ref({
  view: "vis",
  showFilters: true,
  overlay: true

})
let filters = reactive({
  tasks: []
})

let modalities = [
            {name: "_Assist", color:"#dc267f", OldColor: "#23B0DC"},
            {name: "_Monitor", color:"#648fff", OldColor: "#FE70DC"},
            {name: "_Security", color:"#fe6100", OldColor: "#3D7D79"},
            {name: "_Encouraged", color:"#ffb000", OldColor: "#EB6057"}
        ]
let tasks = [
        {key: "DisarmDemob", description: "I'm a short description", text: "Disarmament & Demobilization", modalities: true},
        {key: "Reintegration", description: "I'm a short description", text: "Reintegration", modalities: true},
        //{key: "ControlSALW", description: "I'm a short description", text: "Control of Small Arms and Light Weapons (SALWs)", modalities: true},
        {key: "ControlSALW", description: "I'm a short description", text: "Small Arms and Light Weapons", modalities: true},
        {key: "Demil", description: "I'm a short description", text: "Demilitarization", modalities: true},
        {key: "ArmsEmbargo", description: "I'm a short description", text: "Arms Embargo", modalities: true},
        {key: "CivProtect", description: "I'm a short description", text: "Civilian Protection", modalities: true},
        {key: "HumanRights", description: "I'm a short description", text: "Human Rights", modalities: true},
        {key: "ChildRights", description: "I'm a short description", text: "Children's Rights", modalities: true},
        {key: "SGBViolence", description: "I'm a short description", text: "Sexual and Gender-based Violence", modalities: true},
        {key: "PoliceReform", description: "I'm a short description", text: "Police Reform", modalities: true},
        {key: "MilitaryReform", description: "I'm a short description", text: "Military Reform", modalities: true},
        {key: "OffensOper", description: "I'm a short description", text: "Offensive Operations", modalities: true},
        {key: "JusticeReform", description: "I'm a short description", text: "Justice Sector Reform", modalities: true},
        {key: "TransJustice", description: "I'm a short description", text: "Transitional Justice", modalities: true},
        {key: "CorrectionsReform", description: "I'm a short description", text: "Corrections Reform", modalities: true},
        {key: "BorderControl", description: "I'm a short description", text: "Border Control", modalities: true},
        {key: "Demining", description: "I'm a short description", text: "Demining", modalities: true},
        {key: "Resources", description: "I'm a short description", text: "Resources", modalities: true},
        {key: "StateAuthority", description: "I'm a short description", text: "State Authority Extension", modalities: true},
        {key: "Democratization", description: "I'm a short description", text: "Democratization", modalities: true},
        {key: "ElectSec", description: "I'm a short description", text: "Electoral Security", modalities: true},
        {key: "ElectAssist", description: "I'm a short description", text: "Electoral Assistance", modalities: true},
        {key: "VoterEducation", description: "I'm a short description", text: "Voters Education", modalities: true},
        {key: "PolPartyAssist", description: "I'm a short description", text: "Political Party Assistance", modalities: true},
        //{key: "PartyAssistance", description: "I'm a short description", text: "Political Party Assistance", modalities: true},
        {key: "CivilSociety", description: "I'm a short description", text: "Civil Society", modalities: true},
        {key: "Media", description: "I'm a short description", text: "Media Development", modalities: true},
        {key: "PublicInfo", description: "I'm a short description", text: "Public Information", modalities: true},
        {key: "PowerSharing", description: "I'm a short description", text: "Power Sharing", modalities: true},
        {key: "Reconcil", description: "I'm a short description", text: "National Reconciliation", modalities: true},
        {key: "LocalReconcil", description: "I'm a short description", text: "Local Reconciliation", modalities: true},
        {key: "RegReconcil", description: "I'm a short description", text: "Regional Reconciliation", modalities: true},
        {key: "EcDevelop", description: "I'm a short description", text: "Economic Development", modalities: true},
        {key: "HumanRelief", description: "I'm a short description", text: "Humanitarian Relief", modalities: true},
        {key: "PublicHealth", description: "I'm a short description", text: "Public Health", modalities: true},
        //{key: "RefugeesIDPs", description: "I'm a short description", text: "Refugees/Internally Displaced Person (IDPs)", modalities: true},
        {key: "RefugeesIDPs", description: "I'm a short description", text: "Refugees/Internally Displaced Person (IDPs)", modalities: true},
        {key: "Gender", description: "I'm a short description", text: "Gender", modalities: true},
        {key: "LegalReform", description: "I'm a short description", text: "Legal Reform", modalities: true},
        {key: "Ceasefire", description: "I'm a short description", text: "Ceasefire", modalities: true},
        {key: "PeaceProcess", description: "I'm a short description", text: "Peace Process", modalities: true},
        {key: "CulturalHeritage", description: "I'm a short description", text: "Cultural Heritage Protection", modalities: true},
        //{key: "Mandate_Renewal", description: "I'm a short description", text: "Mandate Renewal", modalities: false }
        {key: "UseOfForce", description: "I'm a short description", text: "Use of Force", modalities: true},
      ]

    const updateFilters = (changedFilter) => {
      let keys = Object.keys(changedFilter) //iterate over keys for deep reactivity
      if(keys.length > 0) keys.forEach(key => filters[key] = changedFilter[key]) 
    }

    const filteredResolutions = computed(() => {
      //if(resolutions.value.length < 0) return false //ignore emty filter sets
    let filtered = resolutions.value
    
    if(filters.mandates) {
        filtered = filtered.filter(resolution => {
            if(!filters.mandates.includes(resolution.NamePKO)) { 
                return false
            }
            return true
        })
    }


    /*if(filters.countries && filters.countries.length > 0) {
        filtered = filtered.filter(resolution => {
            if(!filters.countries.includes(resolution.Country)) { 
                return false
            }
            return true
        })
    }*/

    if(filters.years && filters.years.length > 0) {
        filtered = filtered.filter(resolution => {
            if(resolution.Year > filters.years[1] || resolution.Year < filters.years[0]) {
                return false
            }
            return true
        })
    }

    if(filters.tasks) {
        filtered = filtered.filter(resolution => {
          let hasAnyTask = false
          filters.tasks.forEach(task => {
            if(resolution[task] != "") hasAnyTask = true
          })
          return hasAnyTask
        })
    }
    return filtered
  })


function storeResolutions(csv) {
  //combine resolutions to mandates to carry forward tasks
  let pkos = csv.map(resolution => resolution.NamePKO)
  let uniquePkos = [...new Set(pkos)]
  let mandates = uniquePkos.map(pko => {
    return {
      NamePKO: pko,
      resolutions: csv.filter(resolution => resolution.NamePKO == pko)
    }
  })

  csv.forEach((resolution) => {
    //find this resolution in mandates once
    //let resolutionInMandates = mandates.find(mandate => mandate.NamePKO == resolution.NamePKO).resolutions.find(mandatedResolution => resolution.Signature == mandatedResolution.Signature)
    //console.log(resolutionInMandates)
    
    tasks.forEach(task => {
     //iterate over all modalities in all tasks, combine them in one string
      resolution[task.key] = ""
      if(task.modalities) modalities.forEach(modality => {
        if(resolution[task.key+modality.name]) {
          resolution[task.key] += "<span class='"+modality.name+"'>"+modality.name+": "+resolution[task.key+modality.name] + "</span>"
        }
      })
    })

    //if mandate has no new Tasks
    if(resolution.Mandate_CompleteAdjustment != 1) { //if there is no complete adjustment of the resolution(Alternative Carry Forward suggested by Hannah)
      let haystack = mandates.find(mandate => mandate.NamePKO == resolution.NamePKO).resolutions //array of resolutions that contain the resolution we want to inherit from
      let haystackIndex = haystack.findIndex(hayStackResolution => hayStackResolution.Signature == resolution.Signature) //position of current resolution in csv in the haystack (so we can search backwards from there)

      while(haystack[haystackIndex]) {
        let priorResolution = haystack[haystackIndex-  1]
        if(priorResolution) {
          tasks.forEach(task => {
            //resolution[task.key] = priorResolution[task.key]
            
            if(task.modalities) modalities.forEach(modality => {
              let mention = priorResolution[task.key+modality.name]
              
              if(mention) {
                if(!mention.toString().includes("inherited")) {
                  resolution[task.key+modality.name] = mention+" (inherited from " + priorResolution.Signature + ")"
                } else {
                  resolution[task.key+modality.name] = mention
                }
                //console.log("[",resolution.NamePKO,"] carrying forward:", task.key, modality.name, "from", priorResolution.Signature, "to", resolution.Signature)
              }
            })
          })
        }
        haystackIndex--
      }
      console.log("--")

      
      
      //carry forward tasks from prior resolution
      /*while(haystack[emptyResolutionIndex]) {
        if(haystack[emptyResolutionIndex].newTasks) {
          let priorResolution =  haystack[emptyResolutionIndex]  <-- this was the problem last time......
          tasks.forEach(task => {
            resolution[task.key] = priorResolution[task.key]
            
            if(task.modalities) modalities.forEach(modality => {
              if(priorResolution[task.key+modality.name]) {
                if(priorResolution.Signature != resolution.Signature) //added Signature check to ensure that there is an inheritance from a prior resolution (not sure WHY identical resolutions end up this deep here, but here we are)
                resolution[task.key+modality.name] = priorResolution[task.key+modality.name]+" (inherited from " + priorResolution.Signature + ")"
                //console.log("[",resolution.NamePKO,"] carrying forward:", task.key, modality.name, "from", priorResolution.Signature, "to", resolution.Signature)
              }
            })
          })
          break;
        } 
        emptyResolutionIndex--
      }*/
    }
  })
  resolutions.value = csv
}

onMounted(() => {
  let csv = require('@/assets/PEMA_raw_v2.csv')
  storeResolutions(csv)
  document.title = "Peacekeeping Mandates Dataset"
})
</script>

<style>
.v-overlay__scrim {
  background: white;
  opacity: 0.65
}
p {
  padding: 5px 0;
}

</style>