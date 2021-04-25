<template>
  <div>
    <v-row>
      <v-col cols="12">
        <div class="text-h3">
          เพิ่มปัญหา
        </div>
      </v-col>
    </v-row>
    <v-row class="spacing-12">
      <v-col cols="12">
        <ValidationObserver ref="observer" v-slot="{ invalid, validate }">
          <form @submit.prevent="createNewProblem(validate)">
            <InputWithValidate
              v-model="name"
              name="ปัญหาใหม่"
              label="ปัญหาใหม่"
              outlined
              dense
              flat
              rules="required"
            />
            <div class="d-flex justify-center spacing-24">
              <SubmitButton
                :disabled="invalid"
                :btn-text="$t('components.categoryFormModal.submitBtnText')"
              />
            </div>
          </form>
        </ValidationObserver>
      </v-col>
    </v-row>
  </div>
</template>

<script lang="ts">
import { defineComponent, useContext, ref } from '@nuxtjs/composition-api'

export default defineComponent({
  setup (_, {
    root
  }) {
    const context = useContext()
    const name = ref<string>('')
    const observer = ref<any>(null)

    const createNewProblem = (validate: () => Promise<any>) => {
      validate().then(async (success: boolean) => {
        if (success) {
          try {
            await context.$axios.post('/problem-drafts', {
              title: name.value
            })
            name.value = ''
            root.$nextTick(() => observer.value?.reset())
            context.store.commit('alert/show', { type: 'success', message: 'ทำรายการสำเร็จ' })
          } catch (err) {
            context.store.commit('alert/show', { type: 'error', message: err })
          }
        }
      })
    }

    return {
      createNewProblem,
      name,
      observer
    }
  }
})
</script>
