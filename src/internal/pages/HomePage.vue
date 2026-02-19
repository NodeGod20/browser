<template>
  <div class="flex flex-column w-full h-full padding-100">
    <div
      class="bg-white border-radius-20px box-shadow-default padding-100 max-w-800 w-full margin-bottom-50"
    >
      <h2 class="txt-md txt-weight-strong margin-bottom-25">Home</h2>

      <p v-if="activeProfileDisplay" class="txt-xs margin-bottom-25">
        Active profile:
        <span class="txt-weight-strong">
          {{ activeProfileDisplay }}
        </span>
      </p>

      <p class="txt-xs color-gray-blue margin-bottom-25">
        Internal page <code>lumen://home</code>.
      </p>

      <p class="txt-xs txt-weight-strong margin-bottom-10">
        Available internal routes
      </p>

      <ul class="txt-xs">
        <li
          v-for="key in topRouteKeys"
          :key="key"
          class="margin-bottom-10"
        >
          <button
            class="border-none bg-transparent padding-0 cursor-pointer color-blue hover-underline"
            @click="openRoute(key)"
          >
            <code>lumen://{{ key }}</code>
          </button>

          <ul
            v-if="childrenMap[key]"
            class="list-style-none margin-top-25 margin-left-25"
          >
            <li
              v-for="child in childrenMap[key]"
              :key="child"
              class="margin-bottom-5"
            >
              <button
                class="border-none bg-transparent padding-0 cursor-pointer color-blue hover-underline"
                @click="openRoute(child)"
              >
                <code>lumen://{{ child }}</code>
              </button>
            </li>
          </ul>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { inject, computed } from 'vue';
import { INTERNAL_ROUTE_KEYS } from '../routes';
import { profilesState, activeProfileId } from '../profilesStore';

/* =====================================================
   Route Structure
===================================================== */

const childrenMap: Record<string, string[]> = {
  home: ['drive', 'wallet', 'domain'],
  network: ['explorer', 'dao', 'release'],
};

const allChildren = Object.values(childrenMap).flat();

const topRouteKeys = computed(() =>
  INTERNAL_ROUTE_KEYS.filter((k) => !allChildren.includes(k))
);

/* =====================================================
   Profile State
===================================================== */

const activeProfile = computed(() =>
  profilesState.value.find((p) => p.id === activeProfileId.value) || null
);

const activeProfileDisplay = computed(
  () => activeProfile.value?.name || activeProfile.value?.id || ''
);

/* =====================================================
   Navigation
===================================================== */

const openInNewTab = inject<(url: string) => void>('openInNewTab');

function openRoute(key: string) {
  openInNewTab?.(`lumen://${key}`);
}
</script>
