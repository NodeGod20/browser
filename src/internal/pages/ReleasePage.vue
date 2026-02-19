<template>
  <div class="flex flex-column w-full h-full padding-100">
    <div
      class="bg-white border-radius-20px box-shadow-default padding-100 max-w-800 w-full margin-bottom-50"
    >
      <h2 class="txt-md txt-weight-strong margin-bottom-25">Release</h2>

      <p class="txt-xs color-gray-blue margin-bottom-25">
        Internal page <code>lumen://release</code>.
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
          <RouteLink :route="key" />

          <ul
            v-if="childrenMap[key]"
            class="list-style-none margin-top-25 margin-left-25"
          >
            <li
              v-for="child in childrenMap[key]"
              :key="child"
              class="margin-bottom-5"
            >
              <RouteLink :route="child" />
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

/* =====================================================
   Route Config
===================================================== */

const childrenMap: Record<string, string[]> = {
  home: ['drive', 'wallet', 'domain'],
  network: ['explorer', 'dao', 'release'],
};

const topRouteKeys = computed(() =>
  INTERNAL_ROUTE_KEYS.filter(
    (k) => !Object.values(childrenMap).flat().includes(k)
  )
);

/* =====================================================
   Navigation
===================================================== */

const openInNewTab = inject<(url: string) => void>('openInNewTab');

function openRoute(key: string) {
  openInNewTab?.(`lumen://${key}`);
}
</script>

<!-- Reusable inline component -->
<script lang="ts">
import { defineComponent } from 'vue';

export const RouteLink = defineComponent({
  props: {
    route: {
      type: String,
      required: true,
    },
  },
  inject: ['openInNewTab'],
  methods: {
    open() {
      this.openInNewTab?.(`lumen://${this.route}`);
    },
  },
  template: `
    <button
      class="border-none bg-transparent padding-0 cursor-pointer color-blue hover-underline"
      @click="open"
    >
      <code>lumen://{{ route }}</code>
    </button>
  `,
});
</script>
