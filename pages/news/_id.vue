<template>
  <div>
    <div class="text-h3">
      {{ name }}
    </div>
    <v-row class="spacing-12">
      <v-col cols="12">
        <v-carousel height="auto" :show-arrows="false">
          <v-carousel-item
            v-for="(img, index2) in images"
            :key="`${index2}-image-carousel`"
            :src="img.url"
            reverse-transition="fade-transition"
            transition="fade-transition"
          />
        </v-carousel>
      </v-col>
      <v-col cols="12">
        {{ description }}
      </v-col>
    </v-row>
  </div>
</template>

<script lang="ts">
import { defineComponent, useContext, useFetch, ref, useRoute, useMeta } from '@nuxtjs/composition-api'

export default defineComponent({
  setup () {
    const context = useContext()
    const route = useRoute()
    const name = ref<string>('')
    const description = ref<string>('')
    const images = ref<any>([])

    useFetch(async () => {
      try {
        const newRequest = await context.$axios.get(`/news/${route.value.params.id}`)
        name.value = newRequest.data.data.name
        description.value = newRequest.data.data.description
        images.value = newRequest.data.data.new_images
        // news.value = newRequest.data.data.data
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    })

    useMeta(() => ({ title: name.value }))

    return {
      name,
      description,
      images
    }
  },
  head: {}
})
</script>
