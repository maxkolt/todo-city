<template>
  <v-container class="align-center text-center fill-height">

    <v-row justify="center">
      <v-col cols="12" lg="6" md="12" sm="12">
        <v-card>
          <h1 class="my-5 font-weight-thin">Форма ввода</h1>
          <v-col cols="12">
            <v-text-field
              v-model="email"
              :rules="emailRules"
              label="E-mail"
              required
              variant="outlined"
              color="info"
              type="email"
            ></v-text-field>
          </v-col>

          <v-col cols="12">
            <v-autocomplete
              @update:search="findAddress"
              :items="cities"
              v-model="city"
              label="Город"/>
          </v-col>

          <v-col cols="12">
            <v-text-field
              disabled
              v-model="country"
              label="Страна"
            ></v-text-field>
          </v-col>

          <v-col cols="12">
            <v-text-field
              disabled
              v-model="county"
              label="Федеральный округ"
            ></v-text-field>
          </v-col>

          <v-col cols="12">
            <v-text-field
              disabled
              v-model="area"
              label="Область"
            ></v-text-field>
          </v-col>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import {defineComponent} from 'vue'

export default defineComponent({
  data: () => ({

    email: null,
    city: null,
    country: null,
    county: null,
    area: null,

    cities: ['Moscow', 'Belarus', 'Ukraine'],

    emailRules: [
      (value: string) => {
        if (value) return true
        return 'Электронная почта обязательна.'
      },
      (value: string) => {
        if (/.+@.+\..+/.test(value)) return true
        return 'E-mail введён не корректно'
      },
    ],
  })

  
})
</script>
