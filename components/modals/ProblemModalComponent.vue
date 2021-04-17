<template>
  <v-dialog
    :value="value"
    v-bind="$attrs"
    content-class="common-modal__dialog"
    width="450"
    @click:outside="$emit('input', false)"
  >
    <v-card>
      <ValidationObserver ref="observer" v-slot="{ invalid, validate }">
        <form @submit.prevent="submit(validate)">
          <div class="common-modal__container">
            <div class="common-modal__header">
              <div class="d-flex align-center">
                <span class="text-h3 text-black-color">เพิ่มข้อมูลปัญหาใหม่</span>
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
            <div class="common-modal__content">
              <InputWithValidate
                v-model="name"
                name="ปัญหาใหม่"
                label="ปัญหาใหม่"
                outlined
                dense
                flat
                rules="required"
              />
            </div>
            <v-divider class="border-gainboro-color" />
            <div class="common-modal__footer">
              <SubmitButton
                :disabled="invalid"
                :btn-text="$t('components.categoryFormModal.submitBtnText')"
              />
            </div>
          </div>
        </form>
      </ValidationObserver>
    </v-card>
  </v-dialog>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from '@nuxtjs/composition-api'

export default defineComponent({
  props: {
    pId: {
      type: Number,
      default: 0
    },
    value: {
      type: Boolean,
      default: false
    },
    isEdit: {
      type: Boolean,
      default: false
    }
  },
  setup (props, {
    root,
    emit
  }) {
    const name = ref<string>('')
    const observer = ref<any>(null)

    watch(() => props.value, (value) => {
      if (!value) {
        name.value = ''
        root.$nextTick(() => observer.value?.reset())
      }
    })

    const submit = (validate: () => Promise<any>) => {
      validate().then((success: boolean) => {
        if (success) {
          emit('submit', name.value)
        }
      })
    }

    return {
      name,
      submit,
      observer
    }
  }
})
</script>
