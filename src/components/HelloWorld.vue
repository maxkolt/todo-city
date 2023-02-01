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
              @update:search="findCity"
              no-data-text="Начните ввод"
              :items="cities"
              v-model="city"
              density="comfortable"
              label="Город"
            ></v-autocomplete>
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
import {Address} from "@/models/address";
import {City} from "@/models/city";

const DATA_BASE_URL = "https://suggestions.dadata.ru/suggestions/api/4_1/rs";
const TOKEN = "a5fa9500908363e432ad3ca6cd33c280927b32d5";

export default defineComponent({
  data: () => ({
    email: '',
    city: '',
    country: '',
    county: '',
    area: '',

    cities: [] as City[],

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
  watch: {
    city(newCity: string, oldCity: string) {
      const city: City = this.cities.find(c => c.title === newCity)!
      this.findAddress(city.regionFiasId)
    }
  },
  methods: {
    findAddress(regionFiasId: string) {
      const url = DATA_BASE_URL + "/findById/address";

      const options: RequestInit = {
        method: "POST",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
          "Accept": "application/json",
          "Authorization": "Token " + TOKEN,
        },
        body: JSON.stringify({query: regionFiasId})
      }

      fetch(url, options)
        .then(response => response.json())
        .then(result => {
          const resultAddress = result.suggestions[0].data
          console.log(resultAddress)

          const address: Address = {
            federalDistrict: resultAddress.federal_district,
            regionWithType: resultAddress.region_with_type,
            country: resultAddress.country
          }

          this.county = address.federalDistrict
          this.country = address.country
          this.area = address.regionWithType
          return address
        })
        .catch(error => console.log("error", error));

    },


    findCity(chars: string) {
      if (chars.length < 3) {
        console.log(`Недостаточно букв: ${chars.length}`)
        return
      }
      const url = DATA_BASE_URL + "/suggest/address";

      const options: RequestInit = {
        method: "POST",
        mode: "cors",
        headers: {
          "Content-Type": "application/json",
          "Accept": "application/json",
          "Authorization": "Token " + TOKEN
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
