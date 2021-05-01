<template>
  <div>
    <v-row>
      <v-col cols="12">
        <div class="text-h3">
          ปัญหาที่พบเจอ
        </div>
      </v-col>
      <!-- <v-col cols="6" class="text-right">
        <AddButton
          btn-text="เพิ่มปัญหาที่พบเจอใหม่"
          :block="$vuetify.breakpoint.xsOnly"
          @click="problemModalValue = true"
        />
      </v-col> -->
    </v-row>
    <v-row class="spacing-12">
      <!-- <v-col cols="12" md="8">
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
      </v-col> -->
      <v-col v-if="previousProblem.length > 0" cols="12">
        <div class="cursor-pointer">
          <span
            v-for="(problem, index) in previousProblem"
            :key="index"
            @click="onClickPrevious(index)"
          >{{ index > 0 ? ` / ${problem}` : problem }}</span>
        </div>
      </v-col>
      <v-col cols="12">
        <v-expansion-panels>
          <v-expansion-panel
            v-for="(problem, index) in problems"
            :key="index"
          >
            <v-expansion-panel-header class="text-h3">
              {{ problem.title }} ({{ problem.score }}) คะแนน
            </v-expansion-panel-header>
            <v-expansion-panel-content>
              <v-row>
                <v-col cols="12">
                  <div class="text-body-1">
                    {{ problem.detail }}
                  </div>
                </v-col>
                <v-col cols="12" class="text-center">
                  <CommonButton
                    v-if="problem.childs.length > 0"
                    btn-text="ใช่"
                    color="primary"
                    outlined
                    @click="onClickChoice(problem, true)"
                  />
                  <CommonButton
                    v-if="problem.childs.length > 0"
                    btn-text="ไม่ใช่"
                    color="error"
                    outlined
                    @click="onClickChoice(problem, false)"
                  />
                  <CommonButton
                    v-if="problem.childs.length <= 0"
                    btn-text="เสร็จสิ้น"
                    color="success"
                    outlined
                    @click="showSolve(problem.title, problem.description, problem.id, problem.filename)"
                  />
                  <CommonButton
                    v-if="problem.childs.length <= 0"
                    btn-text="ไม่พบปัญหา"
                    color="info"
                    outlined
                    @click="contactValue = true"
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
    <ConfirmModalComponent
      v-model="confirmModalValue"
      :title="confirmModalTitle"
      :text="confirmModalText"
      :link="link"
      @success="onClickSuccess"
    />
    <ConfirmModalComponent
      v-model="contactValue"
      title="แจ้งเตือน"
      text="กรุณาติดต่อเจ้าหน้าที่ห้องทะเบียน"
      @success="contactValue = false"
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
    const confirmModalValue = ref<boolean>(false)
    const confirmModalTitle = ref<string>('')
    const confirmModalText = ref<string>('')
    const sortLists = ref<any>([{
      text: 'เรียงตามคะแนนน้อยไปมาก',
      value: 'asc'
    }, {
      text: 'เรียงตามคะแนนมากไปน้อย',
      value: 'desc'
    }])
    const lastProblemID = ref<number>(0)
    const link = ref<string>('')
    const contactValue = ref<boolean>(false)

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
        const problemRequest = await context.$axios.get(`/problems?problem_category_id=${route.value.params.id}&problem_id=${problemID.value}&title=${search.value}&sortBy=${sort.value}`)
        problems.value = problemRequest.data.data
      } catch (err) {
        context.store.commit('alert/show', { type: 'error', message: err })
      }
    }

    const onClickChoice = (problem: any, value: boolean) => {
      const trueProblem = problem.childs.find((s: any) => s.is_true)
      const falseProblem = problem.childs.find((s: any) => s.is_false)
      if ((value && (!trueProblem && falseProblem)) || (!value && (trueProblem && !falseProblem))) {
        showSolve(problem.title, problem.description, problem.id, problem.filename)
        return
      }
      const currentProblem = problem
      previousProblem.value = [...previousProblem.value, currentProblem.title]
      previousProblemID.value = [...previousProblemID.value, currentProblem.id]
      problemID.value = trueProblem ? trueProblem.child_problem_id : falseProblem.child_problem_id
      search.value = ''
      if (currentProblem.is_head) {
        headID.value = currentProblem.id
      }
      fetchProblems()
    }

    const onClickPrevious = (index: number) => {
      if (index === 0) {
        problemID.value = ''
        previousProblem.value = []
        previousProblemID.value = []
        fetchProblems()
        return
      }
      if (index + 1 === previousProblemID.value.length) {
        return
      }
      problemID.value = previousProblemID.value[index]
      previousProblem.value = previousProblem.value.filter((_: any, i: number) => i <= index)
      previousProblemID.value = previousProblemID.value.filter((_: any, i: number) => i <= index)
      search.value = ''
      fetchProblems()
    }

    const onClickSuccess = async () => {
      try {
        await context.$axios.post(`/problems/${lastProblemID.value}/count-view`)
        problemID.value = ''
        previousProblem.value = []
        previousProblemID.value = []
        problems.value = []
        search.value = ''
        voteModalValue.value = true
        confirmModalValue.value = false
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
        await context.$axios.post(`/problems/${lastProblemID.value}/vote`, {
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

    const showSolve = (title: string, description: string, id: number, filename: string) => {
      confirmModalValue.value = true
      confirmModalTitle.value = title
      confirmModalText.value = description
      lastProblemID.value = id
      link.value = filename
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
      createNewProblem,
      showSolve,
      confirmModalValue,
      confirmModalTitle,
      confirmModalText,
      link,
      contactValue
    }
  }
})
</script>
