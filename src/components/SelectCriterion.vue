a<template>
  <div class="flex flex-col rounded-md shadow-sm w-96" role="group">
    <button
      @click="(selected = 'fiat'), this.updatePredictor(selected)"
      type="button"
      :class="
        selected === 'fiat'
          ? 'inline-flex items-center px-4 py-2 text-sm font-medium border rounded-t-lg bg-gray-600 border-blue-500 text-white hover:text-white hover:bg-gray-600 transition duration-300'
          : 'inline-flex items-center px-4 py-2 text-sm font-medium border rounded-t-lg bg-gray-700 border-gray-600 text-white hover:text-white hover:bg-gray-600 transition duration-300'
      "
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        fill="currentColor"
        class="w-6 h-6 text-emerald-500"
      >
        <path
          d="M10.464 8.746c.227-.18.497-.311.786-.394v2.795a2.252 2.252 0 01-.786-.393c-.394-.313-.546-.681-.546-1.004 0-.323.152-.691.546-1.004zM12.75 15.662v-2.824c.347.085.664.228.921.421.427.32.579.686.579.991 0 .305-.152.671-.579.991a2.534 2.534 0 01-.921.42z"
        />
        <path
          fill-rule="evenodd"
          d="M12 2.25c-5.385 0-9.75 4.365-9.75 9.75s4.365 9.75 9.75 9.75 9.75-4.365 9.75-9.75S17.385 2.25 12 2.25zM12.75 6a.75.75 0 00-1.5 0v.816a3.836 3.836 0 00-1.72.756c-.712.566-1.112 1.35-1.112 2.178 0 .829.4 1.612 1.113 2.178.502.4 1.102.647 1.719.756v2.978a2.536 2.536 0 01-.921-.421l-.879-.66a.75.75 0 00-.9 1.2l.879.66c.533.4 1.169.645 1.821.75V18a.75.75 0 001.5 0v-.81a4.124 4.124 0 001.821-.749c.745-.559 1.179-1.344 1.179-2.191 0-.847-.434-1.632-1.179-2.191a4.122 4.122 0 00-1.821-.75V8.354c.29.082.559.213.786.393l.415.33a.75.75 0 00.933-1.175l-.415-.33a3.836 3.836 0 00-1.719-.755V6z"
          clip-rule="evenodd"
        />
      </svg>

      FIAT-Währung
    </button>
    <button
      type="button"
      @click="(selected = 'crypto'), this.updatePredictor(selected)"
      :class="
        selected === 'crypto'
          ? 'inline-flex items-center px-4 py-2 text-sm font-medium border bg-gray-600 border-blue-500 text-white hover:text-white hover:bg-gray-600 transition duration-300'
          : 'inline-flex items-center px-4 py-2 text-sm font-medium border bg-gray-700 border-gray-600 text-white hover:text-white hover:bg-gray-600 transition duration-300'
      "
    >
      <svg
        class="w-6 h-6"
        viewBox="0.004 0 64 64"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M63.04 39.741c-4.274 17.143-21.638 27.575-38.783 23.301C7.12 58.768-3.313 41.404.962 24.262 5.234 7.117 22.597-3.317 39.737.957c17.144 4.274 27.576 21.64 23.302 38.784z"
          fill="#f7931a"
        />
        <path
          d="M46.11 27.441c.636-4.258-2.606-6.547-7.039-8.074l1.438-5.768-3.512-.875-1.4 5.616c-.922-.23-1.87-.447-2.812-.662l1.41-5.653-3.509-.875-1.439 5.766c-.764-.174-1.514-.346-2.242-.527l.004-.018-4.842-1.209-.934 3.75s2.605.597 2.55.634c1.422.355 1.68 1.296 1.636 2.042l-1.638 6.571c.098.025.225.061.365.117l-.37-.092-2.297 9.205c-.174.432-.615 1.08-1.609.834.035.051-2.552-.637-2.552-.637l-1.743 4.02 4.57 1.139c.85.213 1.683.436 2.502.646l-1.453 5.835 3.507.875 1.44-5.772c.957.26 1.887.5 2.797.726L27.504 50.8l3.511.875 1.453-5.823c5.987 1.133 10.49.676 12.383-4.738 1.527-4.36-.075-6.875-3.225-8.516 2.294-.531 4.022-2.04 4.483-5.157zM38.087 38.69c-1.086 4.36-8.426 2.004-10.807 1.412l1.928-7.729c2.38.594 10.011 1.77 8.88 6.317zm1.085-11.312c-.99 3.966-7.1 1.951-9.083 1.457l1.748-7.01c1.983.494 8.367 1.416 7.335 5.553z"
          fill="#ffffff"
        />
      </svg>
      Krypto-Währung
    </button>
    <button
      @click="(selected = 'kpi'), this.updatePredictor(selected)"
      type="button"
      :class="
        selected === 'kpi'
          ? 'inline-flex items-center px-4 py-2 text-sm font-medium border bg-gray-600 border-blue-500 text-white hover:text-white hover:bg-gray-600 transition duration-300'
          : 'inline-flex items-center px-4 py-2 text-sm font-medium border bg-gray-700 border-gray-600 text-white hover:text-white hover:bg-gray-600 transition duration-300'
      "
    >
      <svg
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 24 24"
        fill="currentColor"
        class="w-6 h-6 text-gray-200"
      >
        <path
          fill-rule="evenodd"
          d="M3 6a3 3 0 013-3h12a3 3 0 013 3v12a3 3 0 01-3 3H6a3 3 0 01-3-3V6zm4.5 7.5a.75.75 0 01.75.75v2.25a.75.75 0 01-1.5 0v-2.25a.75.75 0 01.75-.75zm3.75-1.5a.75.75 0 00-1.5 0v4.5a.75.75 0 001.5 0V12zm2.25-3a.75.75 0 01.75.75v6.75a.75.75 0 01-1.5 0V9.75A.75.75 0 0113.5 9zm3.75-1.5a.75.75 0 00-1.5 0v9a.75.75 0 001.5 0v-9z"
          clip-rule="evenodd"
        />
      </svg>

      Ökonomische KPI's
    </button>
    <button
      @click="(selected = 'commodities'), this.updatePredictor(selected)"
      type="button"
      :class="
        selected === 'commodities'
          ? 'inline-flex items-center px-4 py-2 text-sm font-medium border bg-gray-600 border-blue-500 text-white hover:text-white hover:bg-gray-600 transition duration-300'
          : 'inline-flex items-center px-4 py-2 text-sm font-medium border bg-gray-700 border-gray-600 text-white hover:text-white hover:bg-gray-600 transition duration-300'
      "
    >
      <svg
        fill="#f7931a"
        class="w-6 h-6"
        viewBox="0 0 50 50"
        xmlns="http://www.w3.org/2000/svg"
        xmlns:xlink="http://www.w3.org/1999/xlink"
      >
        <path
          d="M10 3L10 13L20 13L20 3L10 3 z M 33 13L33 27L1 27C0.447 27 0 27.448 0 28L0 34C0 36.206 1.794 38 4 38L8.0898438 38C8.5678437 40.833 11.032 43 14 43C16.968 43 19.431156 40.833 19.910156 38L33 38C33.48 40.832 36.031 43 39 43C41.969 43 44.43025 40.832 44.90625 38L47 38C48.57 38 50 36.57 50 35L50 25.375C50 23.363 48.551 21.3085 48.375 21.0625L44.21875 15.5C43.26575 14.352 41.77 13 40 13L33 13 z M 4 15L4 25L14 25L14 15L4 15 z M 16 15L16 25L26 25L26 15L16 15 z M 38 18L43.59375 18L46.78125 22.25C47.12025 22.73 48 24.203 48 25.375L48 26L38 26C37 26 36 25 36 24L36 20C36 18.895 37 18 38 18 z M 14 33C16.206 33 18 34.794 18 37C18 39.206 16.206 41 14 41C11.794 41 10 39.206 10 37C10 34.794 11.794 33 14 33 z M 39 33C41.207 33 43 34.793 43 37C43 39.207 41.207 41 39 41C36.793 41 35 39.207 35 37C35 34.793 36.793 33 39 33 z"
        />
      </svg>
      Rohstoffe
    </button>

    <button
      @click="(selected = 'aktie'), this.updatePredictor(selected)"
      type="button"
      :class="
        selected === 'aktie'
          ? 'inline-flex items-center px-4 py-2 text-sm font-medium border rounded-b-lg bg-gray-600 border-blue-500 text-white hover:text-white hover:bg-gray-600 transition duration-300'
          : 'inline-flex items-center px-4 py-2 text-sm font-medium border rounded-b-lg bg-gray-700 border-gray-600 text-white hover:text-white hover:bg-gray-600 transition duration-300'
      "
    >
    <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke-width="1.5" stroke="currentColor" class="w-6 h-6">
      <path stroke-linecap="round" stroke-linejoin="round" d="M3.75 3v11.25A2.25 2.25 0 0 0 6 16.5h2.25M3.75 3h-1.5m1.5 0h16.5m0 0h1.5m-1.5 0v11.25A2.25 2.25 0 0 1 18 16.5h-2.25m-7.5 0h7.5m-7.5 0-1 3m8.5-3 1 3m0 0 .5 1.5m-.5-1.5h-9.5m0 0-.5 1.5m.75-9 3-3 2.148 2.148A12.061 12.061 0 0 1 16.5 7.605" />
    </svg>

      Andere Aktie
    </button>
  </div>
</template>

<script>
import { inject } from 'vue';

export default {
  name: 'SelectCriterion',
  inject: ['predict'],
  setup() {
    const currentPredictor = inject('predict');

    const updatePredictor = (newPredictor) => {
      currentPredictor.predictorName = newPredictor;
    };

    return { updatePredictor };
  },
  data: () => ({
    selected: 'fiat',
  }),
};
</script>

<style scoped></style>
