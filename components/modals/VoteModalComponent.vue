<template>
  <v-dialog
    :value="value"
    v-bind="$attrs"
    content-class="common-modal__dialog"
    width="450"
    @click:outside="$emit('input', false)"
  >
    <v-card>
      <div class="common-modal__container">
        <div class="common-modal__header">
          <div class="d-flex align-center">
            <span class="text-h4 text-black-color">แบบประเมินความพึงพอใจ</span>
            <v-spacer />
            <v-menu
              bottom
              left
            >
              <template #activator="{ on }">
                <v-btn
                  icon
                  v-on="on"
                  @click="$emit('input', false)"
                >
                  <v-icon>mdi-close</v-icon>
                </v-btn>
              </template>
            </v-menu>
          </div>
        </div>
        <v-divider class="border-gainboro-color" />
        <div class="common-modal__content text-body-1 text-black-color">
          <v-row>
            <v-col cols="12">
              <div class="text-h4 text-black-color">
                ความพึงพอใจของการแก้ไขปัญหา
              </div>
              <v-rating
                v-model="problemScore"
                hover
                length="5"
                size="48"
              />
            </v-col>
            <v-col cols="12">
              <div class="text-h4 text-black-color">
                ความพึงพอใจของระบบ
              </div>
              <v-rating
                v-model="systemScore"
                hover
                length="5"
                size="48"
              />
            </v-col>
          </v-row>
        </div>
        <v-divider class="border-gainboro-color" />
        <div class="common-modal__footer">
          <CommonButton
            rounded
            depressed
            outlined
            :btn-text="$t('components.confirmModal.cancelBtnText')"
            @click="$emit('input', false)"
          />
          <SubmitButton
            :btn-text="$t('components.confirmModal.successBtnText')"
            @click="$emit('success', {
              problemScore,
              systemScore
            })"
          />
        </div>
      </div>
    </v-card>
  </v-dialog>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from '@nuxtjs/composition-api'

export default defineComponent({
  props: {
    value: {
      type: Boolean,
      default: false
    }
  },
  setup (props) {
    const problemScore = ref<number>(0)
    const systemScore = ref<number>(0)

    watch(() => props.value, (value) => {
      if (!value) {
        problemScore.value = 0
        systemScore.value = 0
      }
    })

    return {
      problemScore,
      systemScore
    }
  }
})
</script>
