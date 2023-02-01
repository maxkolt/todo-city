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
              type="email"/>
          </v-col>

          <v-col cols="12">
            <v-autocomplete
              @update:search="findCity"
              :items="cities"
              v-model="city"
              label="Введите город"/>
          </v-col>

          <v-col cols="12">
            <v-autocomplete
              disabled
              v-model="country"
              label="Страна"/>
          </v-col>

          <v-col cols="12">
            <v-autocomplete
              disabled
              v-model="county"
              label="Федеральный округ"/>
          </v-col>

          <v-col cols="12">
            <v-autocomplete
              disabled
              v-model="area"
              label="Область"/>
          </v-col>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script lang="ts">
import {defineComponent} from 'vue'

interface City {
  title: string
  regionFiasId: string
}

export default defineComponent({
  data: () => ({
    email: null,
    city: null,
    country: null,
    county: null,
    area: null,
    cities: [] as City [],

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
  }),
  methods: {
    findCity(chars: string) {
      const url = "https://suggestions.dadata.ru/suggestions/api/4_1/rs/suggest/address";
      const token = "a5fa9500908363e432ad3ca6cd33c280927b32d5";

      const options: RequestInit = {
        method: "POST",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
          "Accept": "application/json",
          "Authorization": "Token " + token
        },
        body: JSON.stringify({
          query: chars,
          from_bound: {value: "city"},
          to_bound: {value: "city"}
        })
      }

      fetch(url, options)
        .then(response => response.json())
        .then(result => {
          const suggestions: any[] = result.suggestions
          const findData: any[] = suggestions.map((s: any) => s.data)

          const citiesFound = findData.map(d => {
            const city: City = {
              title: d.city,
              regionFiasId: d.region_fias_id
            }
            return city
          })
          this.cities = citiesFound
        })
        .catch(error => console.log("error", error));
    }
  }
})
</script>
