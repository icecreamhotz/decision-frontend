<template>
  <div>
    <!-- <v-row>
      <v-col cols="12">
        <InputWithValidate
          v-model="search"
          label="ค้นหาปัญหาที่พบเจอ"
          name="ค้นหาปัญหาที่พบเจอใช้ระบบ"
          @enter="$router.push(`/problems?search=${search}`)"
        />
      </v-col>
    </v-row> -->
    <div class="text-h3 spacing-24">
      ข่าวสาร
    </div>
    <v-row>
      <v-col v-for="(data, index) in news" :key="index" cols="12" md="4">
        <v-card
          max-width="374"
          height="100%"
        >
          <v-carousel height="300" :show-arrows="false">
            <v-carousel-item
              v-for="(img, index2) in data.new_images"
              :key="`${index2}-image-carousel`"
              :src="img.url"
              reverse-transition="fade-transition"
              transition="fade-transition"
            />
          </v-carousel>
          <v-card-title>
            <nuxt-link :to="`/news/${data.id}`" class="text-h1 text-decoration-none black--text">
              {{ data.name }}
            </nuxt-link>
          </v-card-title>
          <v-card-text>
            <div class="text-truncate-5">
              {{ data.description }}
            </div>
            <nuxt-link :to="`/news/${data.id}`" class="text-link d-flex justify-end text-right spacing-16">
              อ่านต่อ
            </nuxt-link>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" class="text-right">
        <nuxt-link to="/news" class="text-link">
          ดูเพิ่มเติม
        </nuxt-link>
      </v-col>
    </v-row>
  </div>
</template>

<script lang="ts">
import { defineComponent, useContext, useFetch, ref } from '@nuxtjs/composition-api'

export default defineComponent({
  setup () {
    const context = useContext()
    const news = ref<any>([])
    const search = ref<string>('')

    useFetch(async () => {
      try {
        const newRequest = await context.$axios.get('/news?perPage=9')
        news.value = newRequest.data.data.data
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    })

    return {
      news,
      search
    }
  }
})
</script>
