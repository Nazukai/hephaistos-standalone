<template>
  <v-container fluid>
    <h1 align="center" justify="center"> Creating Exercise </h1>
    <div align="center">
      <v-col cols="12" sm="6" md="3">
        <v-text-field
          v-model="title"
          label="Title"
          required
        ></v-text-field>
      </v-col>
      <v-col cols="12" sm="6" md="3">
        <v-text-field
          v-model="lang"
          label="Langage"
          required
        ></v-text-field>
      </v-col>
    </div>
    <v-row style="align-items:center">
      <v-col cols="12" sm="6" md="3">
        <v-textarea
          v-model="instructions"
          solo
          name="exercise"
          label="Description about the exercise"
          required
        ></v-textarea>
      </v-col>
      <v-col cols="12" sm="6" md="3">
        <v-textarea
          v-model="tests"
          solo
          name="UnitTests"
          label="Enter some Unit Test"
          required
        ></v-textarea>
      </v-col>
      <v-col cols="12" sm="6" md="3">
        <v-textarea
          v-model="solution"
          solo
          name="template"
          label="template solution"
          required
        ></v-textarea>
      </v-col>
      <v-col>
        <div class="exercise-editor-ace-editor" id="editor">
        </div>
        <div>
          <v-btn block color="secondary" dark @click="sandbox()">Run sandbox Code</v-btn>
        </div>
      </v-col>
    </v-row>
    <div align="center">
      <v-col cols="12" sm="6" md="3">
        <v-textarea
          v-model="testResult"
          solo
          name="testResult"
          label="test Result"
          required
        ></v-textarea>
      </v-col>
    </div>
    <div align="center">
      <v-col cols="12" sm="6" md="3">
        <v-btn block color="secondary" dark @click="create()">Create</v-btn>
      </v-col>
    </div>
  </v-container>
</template>

<script>
import ace from 'ace-builds/src-noconflict/ace'
import 'ace-builds/src-noconflict/theme-monokai'
import 'ace-builds/src-noconflict/mode-python'
import 'ace-builds/webpack-resolver'

export default {
  data: () => ({
    instructions: '',
    lang: '',
    title: '',
    tests: '',
    solution: '',
    template_regions: ['template'],
    template_regions_rw: [0],
    difficulty: 0,
    score: 0,
    creation_date: new Date(),
    editor: null,
    testResult: ''
  }),

  mounted () {
    this.editor = ace.edit('editor')
    this.editor.setTheme('ace/theme/monokai')
    this.editor.session.setMode(`ace/mode/${this.lang}`)
  },

  methods: {
    async create () {
      // eslint-disable-next-line camelcase
      const { instructions, lang, title, tests, solution, template_regions, template_regions_rw, difficulty, score, creation_date } = this
      try {
        await this.axios.post('http://localhost:3000/api/v1/exercise', {
          instructions, lang, title, tests, solution, template_regions, template_regions_rw, difficulty, score, creation_date
        })
      } catch (err) {
        console.log(err)
      }
    },
    async sandbox () {
      const { lang, editor, tests } = this
      const solution = editor.getValue()
      try {
        const result = await this.axios.post('http://localhost:3000/api/v1/exercise/sandbox', {
          lang, tests, solution
        })
        this.testResult = JSON.stringify(result.data)
      } catch (err) {
        console.log(err)
      }
    }
  }
}
</script>

<style scoped>
.exercise-editor-ace-editor {
  position: relative;
  min-height: 20rem;
}
</style>
