<template>
  <v-container fluid>
    <v-row>
      <v-col class="v-col-sm-12 v-col-md-12 v-col-lg-12 v-col-xl-8">
        <span style="font-size: 18pt">Visualizing <strong>{{ resolutions.length }} resolutions</strong> in <strong>{{
          mandates.length }} mission<span v-if="mandates.length > 1">s</span></strong></span>
        <p><v-btn size="small" color="#648fff" :variant="activeModalities.includes('_Monitor') ? 'tonal' : 'outlined'"
            @click="updateActiveModalities('_Monitor')"><v-icon icon="mdi-circle" />Monitoring</v-btn> is a modality of
          engagement, whereby peacekeepers are requested to <em>observe</em> compliance or implementation of a task.<br>
          <v-btn size="small" color="#dc267f" :variant="activeModalities.includes('_Assist') ? 'tonal' : 'outlined'"
            @click="updateActiveModalities('_Assist')"><v-icon icon="mdi-circle" />Assisting</v-btn>is a modality of
          engagement, whereby peacekeepers are requested to <em>implement</em> a task or support the government in doing
          so. <br>
          <v-btn size="small" color="#fe6100" :variant="activeModalities.includes('_Security') ? 'tonal' : 'outlined'"
            @click="updateActiveModalities('_Security')"><v-icon icon="mdi-circle" />Securing</v-btn> is a modality of
          engagement, whereby peacekeepers are requested to <em>provide security</em> for a task. <br>
          Sometimes, the mandate does not request but <v-btn size="small" color="#ffb000"
            :variant="activeModalities.includes('_Encouraged') ? 'tonal' : 'outlined'"
            @click="updateActiveModalities('_Encouraged')"><v-icon icon="mdi-circle" />encourages</v-btn> engagement in a
          task. In this case, we code encouraging and do not distinguish between the different modalities of engagement
          (i.e., monitoring, assisting, or securing).
        </p>
      </v-col>
    </v-row>
    <div style="overflow-x: scroll; padding: 20px 0 ">
      <svg :height="dimensions.svgHeight + 'px'" :width="dimensions.svgWidth + 'px'">



        <!-- MANDATES-->
        <g class="mandates">
          <g v-for="(mandate, i) in mandates" :key="mandate.NamePKO"
            :transform="'translate(' + (dimensions.textMargin + (mandate.priorMandateLength * dimensions.colWidth) + (i * 50)) + ',0)'">
            <line y1="0" :y2="(dimensions.rowHeight + 6) * (props.tasks.length + 1)" x1="0" x2="0"
              stroke="rgba(255,255,255,.1)" />
            <text y="15" x="5" font-size="8pt" fill="white" font-weight="bold">{{ mandate.NamePKO }}</text>
            <text y="25" x="5" fill="white" font-size="6pt">{{ mandate.Country }}</text>
            <rect y="0"
              :x="mandate.resolutions.length * (dimensions.colWidth + 5) > 65 ? mandate.resolutions.length * (dimensions.colWidth + 5) : 65"
              width="100" height="40" fill="#121213" />
            <!--RESOLUTIONS-->



            <g v-for="(resolution, l)  in mandate.resolutions"
              :transform="'translate(' + (l * dimensions.colWidth) + ',40)'" :key="resolution.Signature">

              <text font-size="5pt" fill="white" :transform="'rotate(-90) translate(-5  ,10)'"
                @mouseover="setHighlight($event, 'resolution')" @mouseleave="setHighlight($event)">{{ resolution.Year
                }}</text>
              <g v-for="(task, t) in presentTasks" :key="task.key"
                :transform="'translate(' + 0 + ', ' + (t * (dimensions.rowHeight + 5)) + ')'">
                <g v-if="resolution[task.key]" :class="task.key">
                  <!--<rect :width="dimensions.colWidth" :height="dimensions.rowHeight" x="5" y="12" fill="rgba(0,0,0,.1)" />-->
                  <template v-for="(modality, k) in props.modalities">
                    <!--<rect
                    v-if="resolution[task.key+modality.name]"
                    :key="modality.name"
                    x="5"
                    :y="k*dimensions.rowHeight/8+dimensions.rowHeight"
                    :width="dimensions.colWidth"
                    :height="dimensions.rowHeight/8" 
                    :fill="modality.color">
                  </rect>-->
                    <circle v-if="resolution[task.key + modality.name]" :class="task.key + modality.name"
                      :key="modality.name" :cx="10" :cy="k * dimensions.rowHeight / 8 + dimensions.rowHeight" r="7"
                      opacity=".7" @mouseover="showTooltip(resolution, task.key + modality.name, $event)"
                      @mouseout="showTooltip(null, null, $event)" :fill="modality.color">
                    </circle>
                  </template>


                </g>
              </g>
            </g>
          </g>
        </g>

        <!-- TASKS-->
        <g class="tasks" :transform="'translate(0,' + dimensions.topMargin + ')'">
          <g v-for="(task, i) in presentTasks" :key="task.key" @mouseover="setHighlight($event, 'task')"
            @mouseleave="setHighlight($event)" :transform="'translate(0, ' + (i * (dimensions.rowHeight + 5)) + ')'">
            <rect x="0" :width="dimensions.textMargin" y="0" :height="dimensions.rowHeight" fill="#121213" />
            <text :x="dimensions.textMargin - 20" :y="dimensions.rowHeight" fill="white" font-size="10pt"
              text-anchor="end" pointer-events="none">{{ task.text }}</text>
            <!--<text :x="dimensions.textMargin-20" :y="dimensions.rowHeight" font-size="7pt" text-anchor="end" fill="white">{{task.text}}</text>-->
            <line x1="0" :x2="dimensions.svgWidth" y1="5" y2="5" stroke="rgba(255,255,255,.1)" />
          </g>
        </g>

        <g class="highlight" v-if="highlight.display">
          <rect :x="highlight.x" :y="highlight.y" :width="highlight.width" :height="highlight.height"
            fill="rgb(255,255,255,0.5)" />
        </g>

      </svg>
    </div>

    <v-card class="tooltip" v-if="tooltip.display" :style="'top: ' + tooltip.y + 'px; left: ' + tooltip.x + 'px; '">


      <v-card-item>
        <div>
          <div class="text-overline" style="line-height: 1em; margin-bottom: 1em">
            {{ tooltip.mandate }} <span style="font-weight: 300">({{ tooltip.country }})</span>
          </div>
          <div class="text-caption">
            <!--The task <strong  style="font-family: monospace;">{{ tooltip.task }} </strong> in the modality <strong style="font-family: monospace;"> {{ tooltip.modality }} </strong> is mentioned {{ tooltip.count }} time<span v-if="tooltip.count > 1">s</span> in {{ tooltip.year }}.-->
            {{ tooltip.textv3 }}
            <!--<ul>
              <li v-for="(paragraph, i) in tooltip.paragraphs" :key="paragraph.resolution + i">
                <div v-if="paragraph.mention && paragraph.mention.includes('inherited')">
                  Resolution <em>{{ paragraph.resolution }}</em> ({{ paragraph.date }}) does not
                  <span v-if="tooltip.modality == 'Encouraged'">encourage <em>{{ tooltip.task }}</em></span>
                  <span v-else>mention <em>{{ tooltip.task }}</em> in the modality {{ tooltip.modality }}</span>
                  but it is {{ paragraph.mention }}.
                </div>
                <div v-else>
                  Resolution <em>{{ paragraph.resolution }}</em> ({{ paragraph.date }})
                  <span v-if="tooltip.modality == 'Encouraged'">encourages <em>{{ tooltip.task }}</em></span>
                  <span v-else>mentions <em>{{ tooltip.task }}</em> in the modality {{ tooltip.modality }}</span>
                  in §{{ paragraph.mention }}
                </div>
              </li>
            </ul>-->
          </div>
        </div>
      </v-card-item>


    </v-card>

    <div class="wobble" style="position: fixed; top: 80%; right: 10px;"><v-icon color="white"
        icon="mdi-arrow-right-circle-outline" /></div>
  </v-container>
</template>
  
<script setup>
import { defineProps, ref, computed, onMounted } from 'vue';
const props = defineProps(['resolutions', 'tasks', 'filters', 'modalities', 'activeTasks'])

const highlight = ref({
  display: false,
  x: null,
  y: null,
  height: null,
  width: null
})



//group passed (already filtered) into mandates, combine multiple resolutions/year into one, check how long each mandate ran
const mandates = computed(() => {
  let pkos = props.resolutions.map(resolution => resolution.NamePKO)
  let uniquePkos = [...new Set(pkos)]


  let mandates = uniquePkos.map(mandateName => {
    let mandateResolutions = props.resolutions.filter(resolution => resolution.NamePKO == mandateName)
    let mandateResolutionYears = mandateResolutions.map(resolution => resolution.Year)
    let mandateInterval = [Math.min(...mandateResolutionYears), Math.max(...mandateResolutionYears)]
    let combinedResolutions = []

    for (let year = mandateInterval[0]; year <= mandateInterval[1]; year++) {
      let combinedResolution = {} //create empty object to represent this year
      let resolutionsPerYear = mandateResolutions.filter(resolution => resolution.Year == year)
      resolutionsPerYear.forEach(resolution => { //iterate over all resolutions this year
        Object.keys(resolution).forEach(key => { //for all keys (includind all task keys)
          if (resolution[key]) {
            if (props.modalities.some(modality => key.includes(modality.name))) {  //check if current key is modal

              if (activeModalities.value.includes("_" + key.split("_")[1])) {  //check if current modal is active
                if (!combinedResolution[key]) combinedResolution[key] = []
                combinedResolution[key].push({ resolution: resolution.Signature, mention: String(resolution[key]), date: resolution.Date2 })
              }
            } else {
              combinedResolution[key] = resolution[key]
            }
          }
        })
      })

      combinedResolutions.push(combinedResolution)



    }

    return {
      NamePKO: mandateName,
      resolutions: combinedResolutions,
      Country: combinedResolutions[0].Country
    }
  })

  mandates.forEach((mandate, i) => {
    let priorMandateLength = 0
    let priorMandates = i - 1
    if (mandates[priorMandates]) {
      while (mandates[priorMandates]) {
        priorMandateLength = priorMandateLength + mandates[priorMandates].resolutions.length
        priorMandates--
      }
    }
    mandate.priorMandateLength = priorMandateLength
  })
  return mandates
})

const presentTasks = computed(() => {
  let tasks = props.tasks
  tasks = tasks.filter(task => props.activeTasks.includes(task.key))
  return tasks

  /*tasks.forEach(task => {
    task.isPresent = false
    props.resolutions.forEach(resolution => {
      if(resolution[task.key]) task.isPresent = true
    })
  })
  return tasks.filter(task => task.isPresent)*/
})

const dimensions = computed(() => {
  let dimensions = {}
  dimensions.rowHeight = 25
  dimensions.topMargin = 50
  dimensions.textMargin = 250
  dimensions.svgHeight = presentTasks.value.length * (dimensions.rowHeight + 5) + dimensions.topMargin
  dimensions.svgWidth = 6000 //mandates.value.length * 
  dimensions.colWidth = 10

  return dimensions
})




const tooltip = ref({
  display: false,
  x: null,
  y: null,
  task: "",
  modality: "",
  year: "",
  country: "",
  mandate: "",
  count: null,
})

function showTooltip(resolution, task, event) {
  if (resolution && task) {
    event.target.setAttribute("stroke", "white")

    tooltip.value.textv3 = resolution[task][0].mention

    tooltip.value.display = true
    tooltip.value.x = event.pageX + 10
    tooltip.value.y = event.pageY - 5

    let splitTask = task.split("_")
    tooltip.value.task = props.tasks.find(task => task.key == splitTask[0]).text
    tooltip.value.modality = splitTask[1]

    tooltip.value.paragraphs = resolution[task]

    tooltip.value.year = resolution.Year
    tooltip.value.country = resolution.Country
    tooltip.value.mandate = resolution.NamePKO
  } else {
    tooltip.value.display = false

    event.target.setAttribute("stroke", null)
  }
}

function updateActiveModalities(name) {
  let index = activeModalities.value.indexOf(name)
  if (index >= 0) {
    activeModalities.value.splice(index, 1)
  } else {
    activeModalities.value.push(name)
  }
}



const setHighlight = ((event, whattoHighlight) => {
  if (event.type == "mouseover") {
    if (whattoHighlight == 'resolution') {
      highlight.value.display = true
      highlight.value.x = event.target.getCTM().e - 5
      highlight.value.y = dimensions.value.topMargin - 25
      highlight.value.width = dimensions.value.colWidth
      highlight.value.height = dimensions.value.svgHeight
    } else if (whattoHighlight == "task") {
      highlight.value.display = true
      highlight.value.x = 0
      highlight.value.y = event.target.getCTM().f + 4
      highlight.value.width = dimensions.value.svgWidth
      highlight.value.height = dimensions.value.rowHeight
    }
  } else {
    highlight.value.display = false
  }
})

const activeModalities = ref([])
onMounted(() => {
  activeModalities.value = props.modalities.map(modality => modality.name)
})


</script>
<style scoped>
.active {
  font-weight: bold
}

.modalityLegend {
  cursor: pointer;
}

.tooltip {
  display: block;
  position: absolute;
  color: black;
  padding: 10px;

  background: white;
}


.tooltip ul {
  list-style-type: disc;
  list-style-position: outside;
}

.tooltip li {
  border-bottom: 1px solid black;
}

em {
  font-family: monospace;
}

ul.legend {
  list-style-type: none;
  list-style-position: outside;
}

circle {
  mix-blend-mode: lighten;
}

.wobble {
  animation: wobble 2s ease infinite;
}

@keyframes wobble {
  0% {
    transform: translateX(0%);
  }

  15% {
    transform: translateX(-25%) rotate(-5deg);
  }

  30% {
    transform: translateX(20%) rotate(3deg);
  }

  45% {
    transform: translateX(-15%) rotate(-3deg);
  }

  60% {
    transform: translateX(10%) rotate(2deg);
  }

  75% {
    transform: translateX(-5%) rotate(-1deg);
  }

  100% {
    transform: translateX(0%);
  }
}
</style>
  