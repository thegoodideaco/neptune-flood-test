<template>
  <v-form v-model="valid">
    <v-container>
      <v-layout>
        <v-flex class="form">

          <v-text-field @input="$emit('input', $event)"
                        autofocus
                        :value="value"
                        :rules="addressRules"
                        :counter="10"
                        label="Address"
                        required />

          <v-text-field v-model="city"
                        :rules="addressRules"
                        :counter="10"
                        label="City"
                        required />

          <v-select v-model="state"
                    :items="states"
                    :rules="stateRules"
                    label="State"
                    placeholder="State"
                    required />

          <v-text-field v-model.number="zip"
                        maxlength="5"
                        :rules="zipRules"
                        label="Zip"
                        required />
        </v-flex>
      </v-layout>
    </v-container>
  </v-form>
</template>

<script>
import stateList from '@/utils/states'
export default {
  props: ['value'],
  model: {
    prop:  'value',
    event: 'input'
  },
  data() {
    return {
      valid:        false,
      address:      '',
      city:         '',
      addressRules: [
        v => !!v || 'Address is required'
      ],
      state:      '',
      stateRules: [
        v => !!v || 'State is required'
      ],
      zip:      null,
      zipRules: [
        v => String(v).search(/^\d/g) === 0 || 'Zip must only contain numbers',
        v => !!v || 'Zip is required'
      ]
    }
  },
  computed: {
    states() {
      return stateList
    }
  }
}
</script>

<style scoped lang="scss">
.form {
  display: grid;
  column-gap: 10px;

  grid:
    [row1-start] 'addr addr addr addr' auto [row1-end]
    [row2-start] 'city city city state' auto [row2-end]
    [row3-start] 'zip zip zip zip' auto [row3-end]
    / auto;

  > * {
    &:nth-child(1) {
      grid-area: addr;
    }
    &:nth-child(2) {
      grid-area: city;
    }
    &:nth-child(3) {
      grid-area: state;
    }
    &:nth-child(4) {
      grid-area: zip;
    }
  }
}
</style>
