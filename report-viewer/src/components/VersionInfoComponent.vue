<template>
  <div class="text-left text-sm font-bold">
    <div v-if="!isDemo">
      <div v-if="reportViewerVersion.isInvalid()">Could not load version information</div>
      <div v-else-if="reportViewerVersion.isDevVersion()">
        You are using a development version of the JPlag Report Viewer.
      </div>

      <div v-else>
        JPlag v{{ reportViewerVersion.toString() }}

        <div v-if="!minimalReportVersion.isInvalid()">
          The minimal version of JPlag that is supported by the viewer is v{{
            minimalReportVersion.toString()
          }}.
        </div>
      </div>
    </div>

    <div v-else>
      <div v-if="reportViewerVersion.isInvalid()">Could not load version information</div>
      <div v-else>Demo of JPlag v{{ reportViewerVersion.toString() }}</div>
      <div>Displays the result of JPlag on the Progpedia dataset.</div>
    </div>
    <VersionRepositoryReference :show-version="false" :override-style="false" />
  </div>
</template>

<script setup lang="ts">
import { minimalReportVersion, reportViewerVersion } from '@/model/Version'
import VersionRepositoryReference from './VersionRepositoryReference.vue'

const isDemo = import.meta.env.MODE == 'demo'
</script>
