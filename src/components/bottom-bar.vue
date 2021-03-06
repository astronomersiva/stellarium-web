// Stellarium Web - Copyright (c) 2018 - Noctua Software Ltd
//
// This program is licensed under the terms of the GNU AGPL v3, or
// alternatively under a commercial licence.
//
// The terms of the AGPL v3 license can be found in the main directory of this
// repository.

<template>
  <div style="position: absolute; display:flex; align-items: flex-end;">
    <div class="tbtcontainer" style="max-width: 300px; display:flex; align-items: flex-end;">
      <v-btn class="tmenubt" color="secondary" @click.stop.native="locationClicked()"><v-icon class="hidden-sm-and-up">location_on</v-icon><span class="hidden-xs-only">{{ $store.state.currentLocation.shortName }}</span></v-btn>
    </div>
    <v-spacer></v-spacer>

    <bottom-button label="Constellations"
                img="/static/images/btn-cst-lines.svg"
                :toggled="$store.state.stel.constellations.lines.visible"
                @clicked="(b) => { $stel.core.constellations.lines.visible = b }">
    </bottom-button>
    <bottom-button label="Atmosphere"
                img="/static/images/btn-atmosphere.svg"
                :toggled="$store.state.stel.atmosphere.visible"
                @clicked="(b) => { $stel.core.atmosphere.visible = b }">
    </bottom-button>
    <bottom-button label="Landscape"
                img="/static/images/btn-landscape.svg"
                :toggled="$store.state.stel.landscape.visible"
                @clicked="(b) => { $stel.core.landscape.visible = b }">
    </bottom-button>
    <bottom-button label="Azimuthal Grid"
                img="/static/images/btn-azimuthal-grid.svg"
                :toggled="$store.state.stel.lines.azimuthal.visible"
                @clicked="(b) => { $stel.core.lines.azimuthal.visible = b }">
    </bottom-button>
    <bottom-button label="Equatorial Grid"
                img="/static/images/btn-equatorial-grid.svg"
                :toggled="$store.state.stel.lines.equatorial.visible"
                @clicked="(b) => { $stel.core.lines.equatorial.visible = b }">
    </bottom-button>
    <bottom-button label="Deep Sky Objects"
                img="/static/images/btn-nebulae.svg"
                class="mr-auto"
                :toggled="$store.state.stel.dsos.visible"
                @clicked="(b) => { $stel.core.dsos.visible = b }">
    </bottom-button>
    <bottom-button label="Fullscreen"
                :img="fullscreenBtnImage"
                class="mr-auto hidden-xs-only"
                :toggled="$store.state.fullscreen"
                @clicked="(b) => { setFullscreen(b) }">
    </bottom-button>

    <v-spacer></v-spacer>

    <v-menu :close-on-content-click="true" transition="v-slide-y-transition" offset-y top left>
      <v-btn class="tmenubt" color="secondary" slot="activator"><v-icon class="hidden-sm-and-up">today</v-icon><span class="hidden-xs-only">{{ date }}</span></v-btn>
      <v-date-picker v-model="date" scrollable dark></v-date-picker>
    </v-menu>

    <v-menu :close-on-content-click="false" transition="v-slide-y-transition" offset-y top left offset-y>
      <v-btn class="tmenubt" color="secondary" slot="activator"><v-icon class="hidden-sm-and-up">access_time</v-icon><span class="hidden-xs-only">{{ time }}</span></v-btn>
      <v-time-picker v-model="time" dark format="24hr"></v-time-picker>
    </v-menu>


  </div>
</template>

<script>

import BottomButton from '@/components/bottom-button.vue'
import Moment from 'moment'

export default {
  components: { BottomButton },
  data: function () {
    return {
    }
  },
  computed: {
    // The MomentJS time in local time
    utc: function () {
      var d = new Date()
      d.setMJD(this.$store.state.stel.observer.utc)
      return Moment(d)
    },
    time: {
      get: function () {
        let utc = this.utc.clone()
        utc.local()
        return utc.format('HH:mm:ss')
      },
      set: function (newValue) {
        let utc = this.utc.clone()
        utc.local()
        let m = Moment('2000-01-01 ' + newValue + ':00')
        utc.hours(m.hours())
        utc.minutes(m.minutes())
        this.$stel.core.observer.utc = utc.toDate().getMJD()
      }
    },
    date: {
      get: function () {
        let utc = this.utc.clone()
        utc.local()
        return utc.format('YYYY-MM-DD')
      },
      set: function (newValue) {
        let utc = this.utc.clone()
        utc.local()
        let m = Moment(newValue)
        utc.year(m.year()).month(m.month()).date(m.date())
        this.$stel.core.observer.utc = utc.toDate().getMJD()
      }
    },
    fullscreenBtnImage: function () {
      return this.$store.state.fullscreen ? '/static/images/svg/ui/fullscreen_exit.svg' : '/static/images/svg/ui/fullscreen.svg'
    }
  },
  methods: {
    locationClicked: function () {
      this.$store.commit('toggleBool', 'showLocationDialog')
    },
    setFullscreen: function (b) {
      this.$fullscreen.toggle(document.body, {
        wrap: false,
        callback: this.onFullscreenChange
      })
    },
    onFullscreenChange: function (b) {
      if (this.$store.state.fullscreen === b) return
      this.$store.commit('toggleBool', 'fullscreen')
    }
  }
}
</script>

<style>

@media all and (max-width: 600px) {
  .tmenubt {
    min-width: 30px;
  }
}
@media all and (min-width: 600px) {
  .tbtcontainer {
    width: 300px;
  }
}
</style>
