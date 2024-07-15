<template>
  <div v-if="desc.length">
    {{ desc.split('.').slice(0,2).join('. ') }}.
  </div>
  <!-- <div v-else>
    {{ mockdata.split('.').slice(0,2).join('. ')}}.
  </div> -->
</template>

<script>
import axios from 'axios'
import { weatherRegions, regionsCity } from '../utils'
export default {
  name: 'DescBlock',
  data() {
    return {
      desc: '',
      mockdata: 'Яку́тия — крупнейший субъект Российской Федерации, а также самая большая административно-территориальная единица в мире. По размеру территории Якутия превосходит Аргентину — восьмое государство в мире по площади.'
    }
  },
  methods: {
    async fetchDesc(regionName) {
      const url = 'https://kgsearch.googleapis.com/v1/entities:search'
      const apiKey = 'AIzaSyAkCAi03tTo310SnRk06NT_NL8nI0Qehe4'
      this.desc = ''

      let weatherRegion = regionName
      const unvalidRegion = Object.keys(weatherRegions).find(
        (key) => weatherRegions[key] === weatherRegion
      )
      if (unvalidRegion) weatherRegion = regionsCity[unvalidRegion]

      try {
        const response = await axios.get(url, {
          params: {
            query: weatherRegion,
            languages: 'ru',
            limit: 1,
            indent: true,
            key: apiKey
          }
        })
        console.log(response)
        if (response.data && response.data.itemListElement[0]) {
          this.desc = response?.data?.itemListElement[0]?.result?.detailedDescription?.articleBody
        }
      } catch (error) {
        this.desc = ''
        console.error('Ошибка при получении описания:', error)
      }
    }
  }
}
</script>

<style scoped>
div {
  margin: 30px;
  font-size: 16px;
  line-height: 1.5;
}
</style>
