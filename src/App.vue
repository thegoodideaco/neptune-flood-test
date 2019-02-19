<template>
  <v-app :class="$vuetify.breakpoint">

    <v-toolbar dark>
      <v-toolbar-title>
        <img :src="require('@/assets/logo.svg')"> Neptune Flood
      </v-toolbar-title>
    </v-toolbar>

    <v-content>

      <v-layout row
                justify-center>

        <v-container :align-center="true"
                     :fluid="true">

          <v-card :flat="true"
                  class="d-inline-block">

            <!-- Add Button -->
            <v-btn dark
                   @click="dialog = true">
              <v-icon small>add_location</v-icon>
              Add Address
            </v-btn>

            <!-- List of added locations -->
            <div>
              <div class="list-item"
                   v-for="(item, index) in boundCollection"
                   :key="index">

                <h2>Item #{{index+1}}</h2>
                <div>
                  <span>Latitude: <em>{{item.lat}}</em></span>
                  <span>Longitude: <em>{{item.lng}}</em></span>
                </div>

              </div>
            </div>

            <!-- Dialog Window -->
            <v-dialog transition="slide-x-transition"
                      v-model="dialog"
                      scrollable
                      persistent>

              <v-card v-if="dialog">
                <v-toolbar color="pink"
                           dark>
                  <v-toolbar-title>
                    <v-icon>add_location</v-icon>
                    Add Address
                  </v-toolbar-title>
                </v-toolbar>
                <v-card-text>

                  <div>
                    <address-form v-model="address" />

                    <v-container class="headline">
                      <h2>Instructions</h2>
                      <p>
                        Please use the map to position the marker
                        directly above the property.
                      </p>
                    </v-container>
                  </div>

                  <flood-map :address="address"
                             :map-type-id="mapType"
                             :bounds.sync="bounds"
                             :zoom="zoom" />

                </v-card-text>

                <v-divider></v-divider>

                <v-card-actions>
                  <v-spacer></v-spacer>

                  <v-btn flat
                         @click="dialog = false">
                    Cancel
                  </v-btn>
                  <v-btn color="primary"
                         flat
                         :disabled="!bounds"
                         @click="addBounds">
                    Add Location
                  </v-btn>
                </v-card-actions>
              </v-card>

            </v-dialog>

          </v-card>

        </v-container>

      </v-layout>
    </v-content>

  </v-app>
</template>

<script>
import FloodMapVue from '@/components/FloodMap.vue'
import AddressFormVue from './components/AddressForm.vue'

export default {
  name:       'App',
  components: {
    FloodMap:    FloodMapVue,
    AddressForm: AddressFormVue
  },
  data() {
    return {
      address:         null,
      dialog:          false,
      mapType:         'hybrid',
      zoom:            16,
      bounds:          null,
      boundCollection: []
    }
  },
  methods: {
    addBounds() {
      this.boundCollection.push(this.bounds)
      this.dialog = false
    }
  }
}
</script>

<style lang="scss" scoped>
/deep/ .v-dialog {
  width: auto;
  max-width: 1200px;

  .v-card__text {
    display: grid;
    grid: 50vh / 1fr 2fr;

    .xsOnly & {
      grid: auto 500px / 100%;
    }
  }
}

/deep/ .v-toolbar__title {
  position: relative;
  height: 100%;
  display: grid;
  align-items: center;
  grid-auto-flow: column;
  column-gap: 10px;
}
img {
  height: 100%;
  width: auto;
}

.list-item {
  margin-bottom: 15px;

  &:last-child {
    margin-bottom: 0;
  }

  &:nth-child(even) {
    color: #0985c2;
  }

  > div {
    display: grid;

    span {
      font-weight: bold;

      em {
        font-weight: normal;
      }
    }
  }
}
</style>