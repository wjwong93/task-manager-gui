<template>
<v-app app>
  <v-app-bar
    color="light-blue white--text"
    app
  >
    <v-toolbar-title>Welcome, {{ userName }}</v-toolbar-title>
    <v-spacer></v-spacer>
  
    <v-menu offset-y>
      <template v-slot:activator="{ on, attrs }">
        <v-btn tile depressed color="primary" v-bind="attrs" v-on="on">
          <v-icon>mdi-cog</v-icon>
          <span>User Settings</span>
        </v-btn>
      </template>

      <v-list>
        <v-list-item link key="1">
          <v-list-item-title @click="showDialog.ChangeUsername = true">Change Username</v-list-item-title>
        </v-list-item>
        <v-list-item link key="2">
          <v-list-item-title @click="showDialog.ChangePassword = true">Change Password</v-list-item-title>
        </v-list-item>
        <v-list-item link key="3">
          <v-list-item-title @click="showDialog.DeleteUser = true">Delete Account</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-menu>

    <v-btn tile depressed color="primary" @click="logout">
      <v-icon>mdi-logout</v-icon>
      <span>Logout</span>
    </v-btn>
  
  </v-app-bar>

  <v-main class="pt-4">
    <!-- Change Username Dialog -->
    <v-dialog
      v-model="showDialog.ChangeUsername"
      persistent
      width="500px"
    >
      <v-card>
        <v-card-title>Change Username</v-card-title>
        <v-card-text>
          <v-form>
            <v-text-field
              required
              clearable
              v-model="changeUsernameForm.newUsername"
              variant="underlined"
              label="Enter New Username"
            ></v-text-field>
            <v-text-field
              required
              clearable
              v-model="changeUsernameForm.password"
              label="Enter Password"
              :rules="[ v => !!v || 'Enter Password' ]"
              :type="changeUsernameForm.showpw ? 'text' : 'password'"
              :append-icon="changeUsernameForm.showpw ? 'mdi-eye' : 'mdi-eye-off'"
              @click:append="changeUsernameForm.showpw = !changeUsernameForm.showpw"
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text tile color="primary" @click="changeUsername">Confirm</v-btn>
          <v-btn text tile color="primary" @click="showDialog.ChangeUsername = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Change Password Dialog -->
    <v-dialog
      v-model="showDialog.ChangePassword"
      persistent
      width="500px"
    >
      <v-card>
        <v-card-title>Change Password</v-card-title>
        <v-card-text>
          <v-form>
            <v-text-field
              required
              clearable
              v-model="changePwForm.currPw"
              label="Enter Current Password"
              :rules="[ v => !!v || 'Enter Current Password' ]"
              :type="changePwForm.showCurrPw ? 'text' : 'password'"
              :append-icon="changePwForm.showCurrPw ? 'mdi-eye' : 'mdi-eye-off'"
              @click:append="changePwForm.showCurrPw = !changePwForm.showCurrPw"
            ></v-text-field>
            <v-text-field
              required
              clearable
              v-model="changePwForm.newPw"
              label="Enter New Password"
              :rules="[ v => !!v || 'Enter New Password' ]"
              :type="changePwForm.showNewPw ? 'text' : 'password'"
              :append-icon="changePwForm.showNewPw ? 'mdi-eye' : 'mdi-eye-off'"
              @click:append="changePwForm.showNewPw = !changePwForm.showNewPw"
            ></v-text-field>
            <v-text-field
              required
              clearable
              v-model="changePwForm.newPw2"
              label="Re-enter New Password"
              :rules="[ v => !!v || 'Re-enter New Password' ]"
              :type="changePwForm.showNewPw2 ? 'text' : 'password'"
              :append-icon="changePwForm.showNewPw2 ? 'mdi-eye' : 'mdi-eye-off'"
              @click:append="changePwForm.showNewPw2 = !changePwForm.showNewPw2"
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text tile color="primary" @click="changePassword">Confirm</v-btn>
          <v-btn text tile color="primary" @click="showDialog.ChangePassword = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Delete User Dialog -->
    <v-dialog
      v-model="showDialog.DeleteUser"
      persistent
      width="500px"
    >
      <v-card>
        <v-card-title>Delete Account</v-card-title>
        <v-card-text>
          <v-alert
            type="error"
            border="left"
            text
            dense
          >
            <span>Are you sure you want to delete your account?<br>This action cannot be undone.</span>
          </v-alert>
          <v-form>
            <v-text-field
              required
              clearable
              v-model="deleteUserForm.password"
              label="Enter Password"
              :rules="[ v => !!v || 'Enter Password' ]"
              :type="deleteUserForm.showpw ? 'text' : 'password'"
              :append-icon="deleteUserForm.showpw ? 'mdi-eye' : 'mdi-eye-off'"
              @click:append="deletUserForm.showpw = !deleteUserForm.showpw"
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text tile color="primary" @click="deleteUser()">Confirm</v-btn>
          <v-btn text tile color="primary" @click="showDialog.DeleteUser = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Create Task Dialog -->
    <v-dialog
      v-model="showDialog.CreateTask"
      persistent
      width="500px"
    >
      <v-card>
        <v-card-title>Create New Task</v-card-title>
        <v-card-text>
          <v-form>
            <v-text-field
              label="Title"
              prepend-icon="mdi-clipboard"
              v-model="newTask.Title"
              clearable
              required
            ></v-text-field>
            <v-menu
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field 
                  prepend-icon="mdi-calendar"
                  readonly
                  clearable 
                  v-bind="attrs" 
                  v-on="on"
                  v-model="newTask.DueDate"
                  label="Due Date"
                  required
                  ></v-text-field>
              </template>

              <v-date-picker
                no-title
                scrollable
                v-model="newTask.DueDate"
              ></v-date-picker>
            </v-menu>
            <v-icon>mdi-timer-outline</v-icon>  Priority
            <v-chip-group v-model="newTask.Priority" mandatory >
              <v-chip 
                v-for="(priority, i) in priorityItems" 
                :key="i"
                :value="priority" 
                :color="priority === newTask.Priority ? getPriorityColor(priority) : 'grey lighten'"
                class="white--text"
                >
                  {{ priority }}
                </v-chip>
            </v-chip-group>
            <v-textarea
              label="Description"
              prepend-icon="mdi-text-long"
              auto-grow
              v-model="newTask.Description"
              clearable
            ></v-textarea>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text tile color="primary" @click="createTask()">Create</v-btn>
          <v-btn text tile color="primary" @click="showDialog.CreateTask = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Edit Task Dialog -->
    <v-dialog
      v-model="showDialog.EditTask"
      width="500px"
      persistent
    >
      <v-card>
        <v-card-title>Edit Task</v-card-title>
        <v-card-text>
          <v-form>
            <v-text-field
              label="Title"
              prepend-icon="mdi-clipboard"
              v-model="selectedTask.Title"
              required
            ></v-text-field>
            <v-menu
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field 
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs" 
                  v-on="on"
                  v-model="selectedTask.DueDate"
                  label="Due Date"
                  required
                  ></v-text-field>
              </template>

              <v-date-picker
                no-title
                scrollable
                v-model="selectedTask.DueDate"
              ></v-date-picker>
            </v-menu>
            <v-icon>mdi-timer-outline</v-icon>  Priority
            <v-chip-group v-model="selectedTask.Priority" mandatory >
              <v-chip 
                v-for="(priority, i) in priorityItems" 
                :key="i"
                :value="priority" 
                :color="priority === selectedTask.Priority ? getPriorityColor(priority) : 'grey lighten'"
                class="white--text"
                >
                  {{ priority }}
                </v-chip>
            </v-chip-group>

            <v-radio-group row required v-model="selectedTask.IsDone" label="Status" prepend-icon="mdi-checkbox-marked-outline">
              <v-radio label="Completed" :value="true"></v-radio>
              <v-radio label="Incomplete" :value="false"></v-radio>
            </v-radio-group>
            <v-textarea
              label="Description"
              prepend-icon="mdi-text-long"
              v-model="selectedTask.Description"
              auto-grow
            ></v-textarea>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text tile color="primary" @click="updateTask()">Update</v-btn>
          <v-btn text tile color="primary" @click="showDialog.EditTask = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Delete Task Dialog -->
    <v-dialog
      v-model="showDialog.DeleteTask"
      width="500px"
    >
      <v-card>
        <v-card-title>Delete Task</v-card-title>
        <v-card-text>
          <v-alert
            type="error"
            border="left"
            text
            dense
          >
            This action cannot be undone.
          </v-alert>
          <v-form>
            <v-text-field
              label="Title"
              prepend-icon="mdi-clipboard"
              readonly
              :value="selectedTask.Title"
            ></v-text-field>
            <v-text-field 
              prepend-icon="mdi-calendar"
              readonly
              label="Due Date"
              :value="selectedTask.DueDate"
            ></v-text-field>

            <!-- <v-select
              label="Priority"
              chips
              :items="priorityItems"
              prepend-icon="mdi-timer-outline"
              readonly
              :value="selectedTask.priority"
            ></v-select> -->
            <v-icon>mdi-timer-outline</v-icon>  Priority
            <v-chip-group v-model="selectedTask.Priority" mandatory >
              <v-chip 
                v-for="(priority, i) in priorityItems" 
                :key="i"
                :value="priority" 
                :color="priority === selectedTask.Priority ? getPriorityColor(priority) : 'grey'"
                class="white--text"
                :disabled="priority === selectedTask.Priority ? false : true" 
                >
                  {{ priority }}
                </v-chip>
            </v-chip-group>

            <v-radio-group row readonly v-model="selectedTask.IsDone" label="Status" prepend-icon="mdi-checkbox-marked-outline">
              <v-radio
              label="Completed"
              :value="true"
              readonly
              ></v-radio>
              <v-radio
              label="Incomplete"
              :value="false"
              readonly
              ></v-radio>
            </v-radio-group>
            <v-textarea
              label="Description"
              prepend-icon="mdi-text-long"
              readonly
              :value="selectedTask.Description"
              auto-grow
            ></v-textarea>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn text tile color="primary" @click="deleteTask()">Delete</v-btn>
          <v-btn text tile color="primary" @click="showDialog.DeleteTask = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Main -->
    <v-container class="white">
      <v-row>
        <h1>Task Manager</h1>
        <v-spacer></v-spacer>
        <v-btn text tile color="primary" @click="showDialog.CreateTask = true">
          <v-icon>mdi-plus</v-icon>
          Create New Task
        </v-btn>
      </v-row>
      <v-row>
        <v-col cols="5">
          <v-text-field
          label="Title"
          v-model="filters.keyword"
          outlined
          clearable
          placeholder="Search Title"
          persistent-placeholder
          ></v-text-field>
        </v-col>
        <v-col cols="2">
          <v-select
            v-model="filters.priority"
            outlined
            multiple
            :items="priorityItems"
            label="Priority"
            placeholder="Select Priority"
            persistent-placeholder
          >
            <template v-slot:selection="{ index }">
              <template v-if="index === 0">
                <v-chip v-for="p in filters.priority" :key="p" :color="getPriorityColor(p)" small></v-chip>
              </template>
            </template>
          </v-select>
        </v-col>
        <v-col cols="2">
          <v-menu
            min-width="auto"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-text-field 
                v-model="filters.dueDate"
                prepend-inner-icon="mdi-calendar"
                readonly
                clearable 
                v-bind="attrs" 
                v-on="on"
                outlined
                label="Due Date"
                placeholder="Select Due Date"
                persistent-placeholder
                ></v-text-field>
            </template>

            <v-date-picker
              v-model="filters.dueDate"
              no-title
              scrollable
            ></v-date-picker>
          </v-menu>
        </v-col>
        <v-col cols="2">
          <v-select
            label="Status"
            v-model="filters.status"
            outlined
            :items="statusItems"
          ></v-select>
        </v-col>
        <v-col cols="1">
          <v-btn fab tile text @click="getTasks()">
            <v-icon>mdi-magnify</v-icon>
          </v-btn> 
        </v-col>
      </v-row>
      <v-divider></v-divider>
      <v-row>
        <v-col>
          <v-card tile>
          <v-data-table
            :headers="taskHeaders"
            :items="tasks"
            item-key="ID"
            :items-per-page="10"
            no-data-text="No Tasks Found"
            show-expand
          >
            <!-- <template v-slot:item="{ item }">
              <tr>
                <td></td>
                <td>{{ item.title }}</td>
                <td>{{ item.dueDate }}</td>
                <td>{{ item.status ? 'Completed' : 'Incomplete' }}</td>
                <td>
                  <v-tooltip bottom>
                    <template v-slot:activator="{ on, attrs }">
                      <v-btn v-bind="attrs" v-on="on" icon><v-icon>mdi-pencil</v-icon></v-btn>
                    </template>
                    <span>Edit Task</span>
                  </v-tooltip>
                  <v-tooltip bottom>
                    <template v-slot:activator="{ on, attrs }">
                      <v-btn 
                        v-bind="attrs" 
                        v-on="on" 
                        icon 
                        @click="selectTask(item.id); showDialog.DeleteTask = true"
                      >
                        <v-icon>mdi-delete</v-icon>
                      </v-btn>
                    </template>
                    <span>Delete Task</span>
                  </v-tooltip>
                </td>
              </tr>
            </template> -->

            <template v-slot:[`item.Priority`]="{ item }">
              <v-chip dark :color="getPriorityColor(item.Priority)">{{ item.Priority }}</v-chip>
            </template>
            <template v-slot:[`item.RemainingDays`]="{ item }">
              {{ item.IsDone ? '-' : item.RemainingDays }}
            </template>
            <template v-slot:[`item.IsDone`]="{ item }">
              {{ item.IsDone ? 'Completed' : 'Incomplete' }}
            </template>
            <template v-slot:[`item.Actions`]="{ item }">
              <v-tooltip bottom>
                <template v-slot:activator="{ on, attrs }">
                  <v-btn 
                    v-bind="attrs" 
                    v-on="on" 
                    icon
                    @click="selectTask(item.ID); showDialog.EditTask = true"
                  >
                    <v-icon>mdi-pencil</v-icon>
                  </v-btn>
                </template>
                <span>Edit Task</span>
              </v-tooltip>
              <v-tooltip bottom>
                <template v-slot:activator="{ on, attrs }">
                  <v-btn 
                    v-bind="attrs" 
                    v-on="on" 
                    icon 
                    @click="selectTask(item.ID); showDialog.DeleteTask = true"
                  >
                    <v-icon>mdi-delete</v-icon>
                  </v-btn>
                </template>
                <span>Delete Task</span>
              </v-tooltip>
            </template>
            <template v-slot:expanded-item="{ headers, item }">
              <td class="grey lighten-4"></td>
              <td>Description</td>
              <td :colspan="headers.length-2"><span>{{ item.Description || 'No Description.' }}</span></td>
            </template>
          </v-data-table>
          </v-card>

        </v-col>
      </v-row>
    </v-container>
  </v-main>

  <!-- Snackbar -->
  <v-snackbar v-model="snackbar.show" timeout="5000" dark :color="snackbar.color">
    {{ snackbar.text }}
    <template v-slot:action="{ attrs }">
      <v-btn icon v-bind="attrs" @click="snackbar.show = false">
        <v-icon>mdi-close</v-icon>
      </v-btn>
    </template>
  </v-snackbar>
</v-app>
</template>

<script>
import axios from 'axios'
import router from '../router/index'

export default {
  data() {
    return {
      api: 'http://localhost:8000',
      userName: 'User',
      changeUsernameForm: {
        showpw: false,
        newUsername: '',
        password: ''
      },
      changePwForm: {
        showCurrPw: false,
        showNewPw: false,
        showNewPw2: false,
        currPw: '',
        newPw: '',
        newPw2: ''
      },
      deleteUserForm: {
        showpw: false,
        password: ''
      },
      snackbar: {
        show: false,
        color: '',
        text: ''
      },
      showDialog: {
        ChangeUsername: false,
        ChangePassword: false,
        DeleteUser: false,
        CreateTask: false,
        EditTask: false,
        DeleteTask: false,
      },
      filters: {
        keyword: '',
        priority: [],
        dueDate: '',
        status: 'All'
      },
      statusItems: ['All', 'Completed', 'Incomplete'],
      priorityItems: ['high', 'medium', 'low'],
      taskHeaders: [
        {
          text: 'Priority',
          value: 'Priority',
          width: '10%',
          sort: (a, b) => {
            a = this.priorityItems.indexOf(a)
            b = this.priorityItems.indexOf(b)
            return a - b 
          }
        },
        {
          text: 'Title',
          value: 'Title',
          width: '35%'
        },
        {
          text: 'Created At',
          value: 'CreatedAt',
          align: 'center',
          width: '10%'
        },
        {
          text: 'Due Date',
          value: 'DueDate',
          align: 'center', 
          width: '10%'
        },
        {
          text: 'Remaining Days',
          value: 'RemainingDays',
          align: 'center',
          width: '15%'
        },
        {
          text: 'Status',
          value: 'IsDone',
          align: 'center', 
          width: '10%'
        },
        {
          text: 'Actions',
          value: 'Actions',
          sortable: false,
          align: 'center', 
          width: '10%'
        }
      ],
      tasks: [],
      newTask: {
        Title: '',
        Priority: '',
        DueDate: '',
        Description: ''
      },
      selectedTask: {}
    }
  },
  mounted() {
    const loggedIn = this.$cookies.isKey('user-session')
    if (!loggedIn) {
      console.log('Not Logged In')
      this.logout()
    }
    
    this.getUsername()
    this.getTasks()
  },  
  computed: {
  },
  methods: {
    getUsername() {
      axios.get(`${this.api}/user/name`, { withCredentials: true }).then(res => {
        if (res.status === 200) {
          this.userName = res.data.name
        }
      }).catch(err => {
        console.error(err)
        if (err.response)
          this.enableSnackbar('error', 'Could not get username')
      })
    },
    logout() {
      this.$cookies.remove('user-session')
      axios.get(`${this.api}/logout`).then(() => {
        router.replace({ path: '/login' })
      }).catch(err => {
        if (err.response) {
          console.error(err.response)
        }
      })
    },
    changeUsername() {
      let formData = new FormData()
      formData.append('new_username', this.changeUsernameForm.newUsername)
      formData.append('password', this.changeUsernameForm.password)

      axios.put(`${this.api}/user/name`, formData, { withCredentials: true }).then(res => {
        if (res.status === 200) {
          this.showDialog.ChangeUsername = false
          this.getUsername()
          this.enableSnackbar('success', 'Username Changed')
        }
      }).catch(err => {
        console.error(err)
        if (err.response)
          this.enableSnackbar('error', err.response.data.message)
      })
    },
    changePassword() {
      let formData = new FormData()
      formData.append('curr_password', this.changePwForm.currPw)
      formData.append('new_password', this.changePwForm.newPw)
      formData.append('new_password2', this.changePwForm.newPw2)

      axios.put(`${this.api}/user/password`, formData, { withCredentials: true }).then(res => {
        if (res.status === 200) {
          this.showDialog.ChangePassword = false
          this.enableSnackbar('success', 'Password Changed')
        }
      }).catch(err => {
        console.error(err)
        if (err.response)
          this.enableSnackbar('error', err.response.data.message)
      })
    },
    deleteUser() {
      let formData = new FormData()
      formData.append('password', this.deleteUserForm.password)

      axios.post(`${this.api}/user/delete`, formData, { withCredentials: true }).then(res => {
        if (res.status === 200)
          this.logout()
      }).catch(err => {
        console.error(err)
        if (err.response)
          this.enableSnackbar('error', err.response.data.message)
      })
    },
    getTasks() {
      const daysLeft = (date) => {
        const today = new Date()
        const dueDate = new Date(date)
        const diffInDays = Math.round((dueDate.getTime() - today.getTime()) / (1000 * 60 * 60* 24))
        return diffInDays
      }

      const searchParams = () => {
        let paramsObj =  {}
        if (this.filters.keyword) paramsObj['kw'] = this.filters.keyword
        if (this.filters.dueDate) paramsObj['due'] = this.filters.dueDate
        switch (this.filters.status) {
          case 'Completed': paramsObj['completed'] = 't'; break
          case 'Incomplete': paramsObj['completed'] = 'f'; break
        }
        if (this.filters.priority) paramsObj['priority'] = this.filters.priority
        return paramsObj
      }

      axios.get(`${this.api}/list`, { params: searchParams(), withCredentials: true }).then(res => {
        this.tasks = res.data.map(task => {
          task.CreatedAt = task.CreatedAt.split('T')[0]
          task.DueDate = task.DueDate.split('T')[0]
          const dLeft = daysLeft(task.DueDate)
          task['RemainingDays'] = dLeft >= 0 ? dLeft + ' days' : 'Overdue'
          return task
          })
      }).catch(err => {
        console.error(err)
        if (err.response)
          this.enableSnackbar('error', err.response.data.message)
      })
    },
    selectTask(taskId) {
      this.selectedTask = Object.assign({}, this.tasks.find(task => task.ID === taskId))
    },
    createTask() {
      let newTask = new FormData()
      newTask.append('title', this.newTask.Title)
      newTask.append('priority', this.newTask.Priority)
      newTask.append('due_date', this.newTask.DueDate)
      newTask.append('description', this.newTask.Description)
      axios.post(`${this.api}/task/new`, newTask, { withCredentials: true }).then(res => {
        if (res.status === 201) {
          this.showDialog.CreateTask = false
          this.newTask.Title = ''
          this.newTask.DueDate = ''
          this.newTask.Priority = ''
          this.newTask.Description = ''
          this.enableSnackbar('success', 'Task Created')
          this.getTasks()
        }
      }).catch(err => {
        console.error(err)
        if (err.response)
          this.enableSnackbar('error', err.response.data.message)
      })
    },
    updateTask() {
      let updatedTask = new FormData()
      updatedTask.append('title', this.selectedTask.Title)
      updatedTask.append('priority', this.selectedTask.Priority)
      updatedTask.append('due_date', this.selectedTask.DueDate)
      updatedTask.append('is_done', this.selectedTask.IsDone)
      updatedTask.append('description', this.selectedTask.Description)
      axios.put(`${this.api}/task/${this.selectedTask.ID}`, updatedTask, { withCredentials: true }).then(res => {
        if (res.status === 200) {
          this.showDialog.EditTask = false
          this.selectedTask = {}
          this.enableSnackbar('success', 'Task Updated')
          this.getTasks()
        }
      }).catch(err => {
        console.error(err)
        if (err.response)
          this.enableSnackbar('error', err.response.data.message)
      })
    },
    deleteTask() {
      axios.delete(`${this.api}/task/${this.selectedTask.ID}`, { withCredentials: true }).then(res => {
        if (res.status === 200) {
          this.showDialog.DeleteTask = false
          this.selectedTask = {}
          this.enableSnackbar('success', 'Task Deleted')
          this.getTasks()
        }
      }).catch(err => {
        console.error(err)
        if (err.response)
          this.enableSnackbar('error', err.response.data.message)
      })
    },
    enableSnackbar(type, msg) {
      if (type === 'success') {
        this.snackbar.color = 'green accent-3'
      } else if (type === 'error') {
        this.snackbar.color = 'red'
      }

      this.snackbar.text = msg
      this.snackbar.show = true
    },
    getPriorityColor(priority) {
      if (priority === 'high') return 'red darken-4'
      else if (priority === 'medium') return 'amber'
      else if (priority === 'low') return 'green'
      else return 'grey lighten-2'
    }
  }
}
</script>

<style>
html { overflow-y: auto }

.v-data-table__expanded.v-data-table__expanded__content {
  box-shadow: none !important;
}
</style>