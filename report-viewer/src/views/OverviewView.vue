<!--
  A view displaying the overview file of a JPlag report.
-->
<template>
  <div class="absolute bottom-0 left-0 right-0 top-0 flex flex-col">
    <div class="relative left-0 right-0 top-0 flex space-x-5 p-5 pb-0">
      <Container class="flex-grow">
        <h2>JPlag Report</h2>
        <div
          class="flex flex-row items-center space-x-5 print:flex-col print:items-start print:space-x-0"
        >
          <TextInformation label="Submission Directory" class="flex-auto">{{
            submissionPathValue
          }}</TextInformation>
          <TextInformation label="Result name" class="flex-auto">{{
            store().state.uploadedFileName
          }}</TextInformation>
          <TextInformation label="Total Submissions" class="flex-auto">{{
            store().getSubmissionIds.length
          }}</TextInformation>

          <TextInformation label="Shown/Total Comparisons" class="flex-auto">
            <template #default
              >{{ overview.shownComparisons }} / {{ overview.totalComparisons }}</template
            >
            <template #tooltip>
              <div class="whitespace-pre text-sm">
                <TextInformation label="Shown Comparisons">{{
                  overview.shownComparisons
                }}</TextInformation>
                <TextInformation label="Total Comparisons">{{
                  overview.totalComparisons
                }}</TextInformation>
                <div v-if="overview.missingComparisons > 0">
                  <TextInformation label="Missing Comparisons">{{
                    overview.missingComparisons
                  }}</TextInformation>
                  <p>
                    To include more comparisons in the report modify the number of shown comparisons
                    in the CLI.
                  </p>
                </div>
              </div>
            </template>
          </TextInformation>

          <TextInformation label="Min Token Match" class="flex-auto">
            <template #default>
              {{ overview.matchSensitivity }}
            </template>
            <template #tooltip>
              <div class="whitespace-pre text-sm">
                <p>
                  Tunes the comparison sensitivity by adjusting the minimum token required to be
                  counted as a matching section.
                </p>
                <p>It can be adjusted in the CLI.</p>
              </div>
            </template>
          </TextInformation>

          <ToolTipComponent direction="left" class="flex-grow-0 print:hidden">
            <template #default>
              <Button @click="router.push({ name: 'InfoView' })"> More </Button>
            </template>
            <template #tooltip>
              <p class="whitespace-pre text-sm">More information about the CLI run of JPlag</p>
            </template>
          </ToolTipComponent>
        </div>
      </Container>
    </div>

    <div
      class="relative bottom-0 left-0 right-0 flex flex-grow space-x-5 px-5 pb-7 pt-5 print:flex-col print:space-x-0 print:space-y-5"
    >
      <Container
        class="flex max-h-0 min-h-full flex-1 flex-col print:max-h-none print:min-h-fit print:flex-none"
      >
        <h2>Distribution of Comparisons:</h2>
        <DistributionDiagram :distributions="overview.distribution" class="flex-grow" />
      </Container>

      <Container class="flex max-h-0 min-h-full flex-1 flex-col print:hidden">
        <ComparisonsTable
          :clusters="overview.clusters"
          :top-comparisons="overview.topComparisons"
          class="min-h-0 flex-1 print:min-h-full print:flex-grow"
        >
          <template v-if="overview.topComparisons.length < overview.totalComparisons" #footer>
            <p class="w-full pt-1 text-center font-bold">
              Not all comparisons are shown. To see more, re-run JPlag with a higher maximum number
              argument.
            </p>
          </template>
        </ComparisonsTable>
      </Container>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, type PropType, onErrorCaptured } from 'vue'
import { redirectOnError, router } from '@/router'
import DistributionDiagram from '@/components/distributionDiagram/DistributionDiagram.vue'
import ComparisonsTable from '@/components/ComparisonsTable.vue'
import { store } from '@/stores/store'
import Container from '@/components/ContainerComponent.vue'
import Button from '@/components/ButtonComponent.vue'
import TextInformation from '@/components/TextInformation.vue'
import ToolTipComponent from '@/components/ToolTipComponent.vue'
import { Overview } from '@/model/Overview'

const props = defineProps({
  overview: {
    type: Object as PropType<Overview>,
    required: true
  }
})

document.title = `${store().state.uploadedFileName} - JPlag Report Viewer`

const hasMoreSubmissionPaths = computed(() => props.overview.submissionFolderPath.length > 1)
const submissionPathValue = computed(() =>
  hasMoreSubmissionPaths.value
    ? 'Click More to see all paths'
    : props.overview.submissionFolderPath[0]
)

onErrorCaptured((error) => {
  redirectOnError(error, 'Error displaying overview:\n')
  return false
})
</script>
