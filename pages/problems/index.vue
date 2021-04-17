<template>
  <div>
    <v-row>
      <v-col cols="6">
        <div class="text-h3">
          ปัญหาที่พบเจอ
        </div>
      </v-col>
      <v-col cols="6" class="text-right">
        <AddButton
          btn-text="เพิ่มปัญหาที่พบเจอใหม่"
          :block="$vuetify.breakpoint.xsOnly"
          @click="problemModalValue = true"
        />
      </v-col>
    </v-row>
    <v-row class="spacing-12">
      <v-col cols="12" md="8">
        <InputWithValidate
          v-model="search"
          label="ค้นหาปัญหาที่พบเจอ"
          name="ค้นหาปัญหาที่พบเจอใช้ระบบ"
          @enter="searchByText"
        />
      </v-col>
      <v-col cols="12" md="4">
        <SelectWithValidate
          v-model="sort"
          label="ตัวกรอง"
          outlined
          dense
          flat
          text-field="text"
          value-field="value"
          :items="sortLists"
        />
      </v-col>
      <v-col v-if="previousProblem.length > 0" cols="12">
        <div class="cursor-pointer" @click="onClickPrevious">
          <v-icon>mdi-arrow-left</v-icon> <span>{{ previousProblem[previousProblem.length - 1] }}</span>
        </div>
      </v-col>
      <v-col cols="12">
        <v-expansion-panels>
          <v-expansion-panel
            v-for="(problem, index) in problems"
            :key="index"
          >
            <v-expansion-panel-header>
              {{ problem.title }} ({{ problem.score }}) คะแนน
            </v-expansion-panel-header>
            <v-expansion-panel-content>
              <v-row>
                <v-col cols="12">
                  {{ problem.description }}
                </v-col>
                <template v-if="problem.childs.length > 0">
                  <v-col cols="6">
                    <CommonButton
                      btn-text="ใช่"
                      color="primary"
                      block
                      depressed
                      :disabled="!problem.childs.some(s => s.is_true)"
                      @click="onClickChoice(problem.childs.find(s => s.is_true), problem)"
                    />
                  </v-col>
                  <v-col cols="6">
                    <CommonButton
                      text
                      btn-text="ไม่ใช่"
                      block
                      color="error"
                      :disabled="!problem.childs.some(s => s.is_false)"
                      @click="onClickChoice(problem.childs.find(s => s.is_false), problem)"
                    />
                  </v-col>
                </template>
                <v-col v-else cols="12">
                  <CommonButton
                    btn-text="เสร็จสิ้น"
                    color="success"
                    block
                    depressed
                    @click="onClickSuccess(problem.id)"
                  />
                </v-col>
              </v-row>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-expansion-panels>
      </v-col>
    </v-row>
    <VoteModalComponent
      v-model="voteModalValue"
      @success="vote"
    />
    <ProblemModalComponent
      v-model="problemModalValue"
      @submit="createNewProblem"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, useContext, ref, watch, useRoute, onMounted } from '@nuxtjs/composition-api'

export default defineComponent({
  setup () {
    const context = useContext()
    const route = useRoute()
    const problems = ref<any>([])
    const problemID = ref<string>('')
    const headID = ref<string>('')
    const previousProblem = ref<any>([])
    const previousProblemID = ref<any>([])
    const search = ref<string>('')
    const voteModalValue = ref<boolean>(false)
    const problemModalValue = ref<boolean>(false)
    const sort = ref<string>('asc')
    const sortLists = ref<any>([{
      text: 'เรียงตามคะแนนน้อยไปมาก',
      value: 'asc'
    }, {
      text: 'เรียงตามคะแนนมากไปน้อย',
      value: 'desc'
    }])

    watch(() => sort.value, () => {
      fetchProblems()
    })

    onMounted(() => {
      if (route.value.query.search) {
        search.value = route.value.query.search as string
      }
      fetchProblems()
    })

    const fetchProblems = async () => {
      try {
        const problemRequest = await context.$axios.get(`/problems?problem_id=${problemID.value}&title=${search.value}&sortBy=${sort.value}`)
        problems.value = problemRequest.data.data
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    }

    const onClickChoice = (nextProblem: any, currentProblem: any) => {
      previousProblem.value = [...previousProblem.value, currentProblem.title]
      previousProblemID.value = [...previousProblemID.value, currentProblem.id]
      problemID.value = nextProblem.child_problem_id
      search.value = ''
      if (currentProblem.is_head) {
        headID.value = currentProblem.id
      }
      fetchProblems()
    }

    const onClickPrevious = () => {
      problemID.value = previousProblemID.value.length === 1 ? '' : previousProblemID.value[previousProblemID.value.length - 1]
      previousProblem.value.pop()
      previousProblemID.value.pop()
      search.value = ''
      fetchProblems()
    }

    const onClickSuccess = async (id: number) => {
      try {
        await context.$axios.post(`/problems/${id}/count-view`)
        problemID.value = ''
        previousProblem.value = []
        previousProblemID.value = []
        problems.value = []
        search.value = ''
        voteModalValue.value = true
        fetchProblems()
        context.store.commit('alert/show', { type: 'success', message: 'ทำรายการสำเร็จ' })
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    }

    const searchByText = () => {
      problemID.value = ''
      previousProblem.value = []
      previousProblemID.value = []
      problems.value = []
      fetchProblems()
    }

    const vote = async (value: any) => {
      try {
        await context.$axios.post(`/problems/${headID.value}/vote`, {
          score_problem: value.problemScore,
          score_system: value.systemScore
        })
        voteModalValue.value = false
        context.store.commit('alert/show', { type: 'success', message: 'ทำรายการสำเร็จ' })
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    }

    const createNewProblem = async (name: string) => {
      try {
        await context.$axios.post('/problem-drafts', {
          title: name
        })
        problemModalValue.value = false
        context.store.commit('alert/show', { type: 'success', message: 'ทำรายการสำเร็จ' })
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    }

    return {
      problems,
      search,
      sort,
      sortLists,
      onClickChoice,
      previousProblem,
      onClickPrevious,
      onClickSuccess,
      voteModalValue,
      problemModalValue,
      searchByText,
      vote,
      createNewProblem
    }
  }
})
</script>
