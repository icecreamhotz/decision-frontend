<template>
  <div>
    <v-row>
      <v-col cols="12">
        <div class="text-h3">
          หมวดหมู่วิเคราะห์ปัญหา
        </div>
      </v-col>
    </v-row>
    <v-row class="spacing-12">
      <v-col v-for="(cat, index) in category" :key="index" cols="12">
        <v-card width="100%">
          <v-card-text class="d-flex align-center justify-space-between">
            <div class="text-h4 black--text">
              {{ cat.name }}
            </div>
            <v-btn text color="primary" @click="$router.push(`/problems/category/${cat.id}`)">
              ดูรายละเอียด
            </v-btn>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script lang="ts">
import { defineComponent, useContext, onMounted, ref } from '@nuxtjs/composition-api'

export default defineComponent({
  setup () {
    const context = useContext()
    const category = ref<any>([])

    onMounted(() => {
      fetchPrblemCategories()
    })

    const fetchPrblemCategories = async () => {
      try {
        const problemCategoryRequest = await context.$axios.get('/problem-categories')
        category.value = problemCategoryRequest.data.data
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    }

    return {
      category
    }
  }
})
</script>
