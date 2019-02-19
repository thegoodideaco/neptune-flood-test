<template>
  <div class="flood-map">

  </div>
</template>

<script>

import { debounce } from 'lodash'

const { geocode } = new window.google.maps.Geocoder()

export default {
  data() {
    return {
      geocode,
      d:       null,
      map:     null,
      marker:  null,
      loading: false,

      loadF: debounce(() => {

        this.geocode({
          address: this.$props.address
        }, d => {
          this.d = d.length ? d[0] : null
          this.loading = false

          const {
            geometry: {
              location: {
                lat,
                lng
              }
            }
          } = this.d


          this.$emit('update:bounds', {
            lat: lat(),
            lng: lng()
          })
        })
      }, 300)
    }
  },
  props: {
    address: String,

    zoom: {
      type:    Number,
      default: 8
    },
    mapTypeId: String
  },
  computed: {
    mapOptions() {
      return {
        zoom:              this.zoom,
        center:            this.coords,
        streetViewControl: false,
        mapTypeId:         this.mapTypeId || 'hybrid'
      }
    },
    markerOptions() {
      return {
        position: {
          ...this.coords
        }
      }
    },
    coords() {
      if (this.d) {
        const {
          lat,
          lng
        } = this.d.geometry.location

        return {
          lat: lat(),
          lng: lng()
        }
      } else {
        return null
      }
    }
  },
  watch: {
    address: {
      handler(val) {
        if (val) {
          this.loading = true
          this.d = null
          this.loadF()
        }
      },
      immediate: true
    },
    mapOptions: {
      handler(val) {
        if (val) {
          console.log(val)
          this.map.setOptions(val)
        }
      },
      deep:      true,
      immediate: true
    },
    markerOptions: {
      handler(val) {
        if (val) {
          this.marker.setOptions(val)
        }
      },
      deep:      true,
      immediate: true
    }
  },
  mounted() {
    this.map = new window.google.maps.Map(this.$el, this.mapOptions)
    this.marker = new window.google.maps.Marker({
      position:  this.coords,
      draggable: true,
      map:       this.map,
      title:     'Hello World!'
    })

    this.marker.addListener('drag', (e) => {
      // console.log('clicked marker', e)
      this.$emit('update:bounds', {
        lat: e.latLng.lat(),
        lng: e.latLng.lng()
      })
    })


    this.$emit('update:bounds', this.coords)
  }
}
</script>



<style scoped>
.flood-map {
  min-width: 100px;
  min-height: 100px;
}
</style>
