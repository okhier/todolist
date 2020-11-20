<template>
  <v-row justify="center" class="todo-container">
    <v-col cols="20">
      <v-card class="todo-wrapper">
        <v-toolbar color="orange" class="todo-toolbar">
          <v-app-bar-nav-icon></v-app-bar-nav-icon>
          <v-toolbar-title class="todo-title">
            <v-text-field
              v-model="todoinput"
              class="todo-textfield"
              value="今週やること"
              @keydown="handleKeydownInput($event)"
            ></v-text-field>
            <v-select
              v-model="todoDays"
              class="todo-daysSelect"
              :items="days"
              color="pink"
              label="曜日"
              required
            ></v-select
          ></v-toolbar-title>
          <v-spacer></v-spacer>
          <v-btn class="todo-inputButton" icon @click="handleTodoInput()">
            <v-icon>mdi-download</v-icon>
          </v-btn>
        </v-toolbar>
        <v-list>
          <template v-for="(todo, index) in todolists">
            <v-subheader v-if="todo.days" :key="todo.days" inset>
              {{ todo.days }}
            </v-subheader>
            <v-divider v-if="todo.divider" :key="index" inset> </v-divider>
            <v-list-item
              v-if="todo.work"
              :key="index"
              class="todo-list"
              @click="toggleCheck(index)"
            >
              <v-list-item-avatar v-if="todo.checked === true">
                <v-icon>mdi-check-bold</v-icon>
              </v-list-item-avatar>
              <v-list-item-content>
                <v-list-item-title
                  size="13"
                  class="todo-work"
                  v-html="todo.work"
                ></v-list-item-title>
              </v-list-item-content>
              <v-list-item-action>
                <v-icon @click="handleDelete($event, index)"
                  >mdi-window-close</v-icon
                >
              </v-list-item-action>
            </v-list-item>
          </template>
        </v-list>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data() {
    return {
      todoinput: '',
      todolists: [
        { days: 'Sun', divider: true },
        { work: '犬とお散歩', selectedDays: 'Sun', checked: false },
        { days: 'Mon', divider: true },
        { work: 'マック食べに行く事。', selectedDays: 'Mon', checked: true },
        { work: 'お台場に遊びに行く事。', selectedDays: 'Mon', checked: false },
      ],
      days: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sta'],
      todoDays: '',
    }
  },
  methods: {
    handleTodoInput() {
      if (!this.todoDays) {
        alert('曜日を選択してください。')
        return
      }
      const selectedDays = this.todoDays
      const daysIndex = this.todolists.findIndex(
        (todo) => todo.days === selectedDays
      )
      if (daysIndex >= 0) {
        this.todolists.splice(daysIndex + 1, 0, {
          work: this.todoinput,
          selectedDays: this.todoDays,
          checked: false,
        })
      } else {
        const daysNumber = this.days.indexOf(selectedDays)

        const newDaysIndex = this.todolists.findIndex(
          (todo) => this.days.indexOf(todo.days) > daysNumber
        )
        if (newDaysIndex >= 0) {
          this.todolists.splice(newDaysIndex, 0, {
            days: selectedDays,
            divider: true,
          })
        } else {
          this.todolists.push({ days: selectedDays, divider: true })
        }

        const newDaysTitleIndex = this.todolists.findIndex(
          (todo) => todo.days === selectedDays
        )
        this.todolists.splice(newDaysTitleIndex + 1, 0, {
          work: this.todoinput,
          selectedDays: this.todoDays,
          checked: false,
        })
      }

      this.todoinput = ''
    },
    handleKeydownInput(event) {
      if (event.key === 'Enter') {
        this.handleTodoInput()
      }
    },
    toggleCheck(index) {
      this.todolists[index].checked = !this.todolists[index].checked
    },
    handleDelete(e, selectedIndex) {
      e.stopPropagation()
      const deletedTodo = this.todolists[selectedIndex]
      const deleteTodoDays = deletedTodo.selectedDays
      this.todolists.splice(selectedIndex, 1)
      const deletedTodoIndex = this.todolists.findIndex(
        (todo) => todo.selectedDays === deleteTodoDays
      )
      const deletedTodoHeader = this.todolists.findIndex(
        (todo) => todo.days === deleteTodoDays
      )
      if (deletedTodoIndex === -1) {
        this.todolists.splice(deletedTodoHeader, 1)
      }
    },
  },
}
</script>
