<template>
  <div>
    <div class="text-h3">
      ข่าวสารทั้งหมด
    </div>
    <v-row class="spacing-12">
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
    </v-row>
    <v-row>
      <v-col cols="12">
        <v-pagination
          v-model="page"
          circle
          :length="total"
        />
      </v-col>
    </v-row>
  </div>
</template>

<script lang="ts">
import { defineComponent, useContext, ref, watch } from '@nuxtjs/composition-api'

export default defineComponent({
  setup () {
    const context = useContext()
    const news = ref<any>([])
    const page = ref<any>(1)
    const total = ref<any>(0)

    watch(() => page.value, (value) => {
      page.value = value
      fetchNews()
    })

    const fetchNews = async () => {
      try {
        const newRequest = await context.$axios.get(`/news?perPage=9&page=${page.value}`)
        news.value = newRequest.data.data.data
        page.value = newRequest.data.data.page
        total.value = newRequest.data.data.lastPage
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    }

    fetchNews()

    return {
      news,
      page,
      total
    }
  }
})
</script>
