<template>
  <nav class="navbar" role="navigation" aria-label="main navigation">
    <div class="navbar-brand">
      <div class="navbar-item mono">
        <img :src="logo" title="Pokémon" alt="Pokémon" class="logo">
        <span>{{ gameName }}</span>
      </div>
      <div class="navbar-item">
        <Timer class="is-pulled-right" v-if="isMobile" />
      </div>
    </div>
    <div class="navbar-menu" v-if="!isMobile">
      <div class="navbar-end">
        <div class="navbar-item">
          <Timer />
        </div>
      </div>
    </div>
  </nav>
</template>

<script>
import { sample } from 'lodash-es'
import { mapState } from 'vuex'
import Timer from '../Timer.vue'
import Visibility from 'visibilityjs'

export default {
  name: 'ShellHeader',
  components: { Timer },
  data () {
    return {
      ids: [],
      pokemon: 150
    }
  },
  computed: {
    ...mapState(['isMobile']),
    logo () {
      return 'img/abstract.png'
    },
    gameName () {
      return process.env.VUE_APP_NAME
    }
  },
  watch: {
    logo (href) {
      this.$headIcons
        .forEach(el => {
          el.href = href
        })
    }
  },
  methods: {
    loadIds () {
      return fetch('/logos.json')
        .then(response => response.json())
        .then(ids => {
          this.ids = ids
        })
    },
    changeLogo () {
      this.pokemon = sample(this.ids)
    }
  },
  created () {
    this.$headIcons = document.querySelectorAll('link[rel=icon')
  },
  mounted () {
    this.loadIds()
      .then(() => {
        this.$interval = Visibility.every(10000, () => { // 10s
          this.changeLogo()
        })
      })
  },
  beforeDestroy () {
    Visibility.stop(this.$interval)
  }
}
</script>

<style>
  .logo {
    max-height: 3rem !important;
  }
  .mono {
    font-family: monospace;
    font-weight: bold;
  }
</style>
