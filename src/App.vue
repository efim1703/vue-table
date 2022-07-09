<template>
  <div id="app">
    <v-app>
      <v-container>
        <v-data-table
          :headers="headersTable"
          :items="users"
          :expanded.sync="expanded"
          item-key="name"
          show-expand
          hide-default-footer
        >
          <template v-slot:top>
            <div class="d-flex justify-center mb-4">
              <v-btn
                color="#77e14b"
                class="white--text"
                @click="showModalAddUser = true"
              >
                Добавить
              </v-btn>
            </div>
          </template>
          <template v-slot:expanded-item="{ item }">
            <td v-if="item.childrens" :colspan="headersTable.length">
              <v-data-table
                :headers="headersTable"
                :items="item.childrens"
                item-key="name"
                class="full-width grey lighten-5"
                :expanded.sync="expandedChild"
                show-expand
                hide-default-footer
              >
                <template v-slot:expanded-item="{ item }">
                  <td v-if="item.childrens" :colspan="headersTable.length">
                    <v-data-table
                      :headers="headersTable"
                      :items="item.childrens"
                      item-key="name"
                      class="full-width grey lighten-3"
                      hide-default-footer
                    >
                    </v-data-table>
                  </td>
                </template>
              </v-data-table>
            </td>
          </template>
        </v-data-table>
        <v-dialog v-model="showModalAddUser" persistent max-width="400">
          <div class="modal-window d-flex flex-column align-center pa-6 grey lighten-3">
            <div class="full-width d-flex justify-space-between mb-4">
              <h3>Добавление пользователя</h3>
              <v-icon class="cursor-pointer" @click="showModalAddUser = false">mdi-close</v-icon>
            </div>
            <v-text-field
              class="full-width"
              :rules="[() => !!formAddNewUser.name || 'This field is required']"
              required
              v-model="formAddNewUser.name"
              placeholder="Имя"
            />
            <v-text-field
              class="full-width"
              :rules="[() => !!formAddNewUser.number || 'This field is required']"
              required
              v-model="formAddNewUser.number"
              placeholder="Телефон"
            />
            <v-select
              class="full-width"
              placeholder="Начальник"
              :items="directors"
              v-model="formAddNewUser.director"
            />
            <v-select
              class="full-width"
              placeholder="Родитель"
              :items="usersNames"
              v-model="formAddNewUser.parent"
            />
            <v-btn 
              color="green" 
              @click="saveUser()" 
              class="white--text"
            >
              Сохранить
            </v-btn>
          </div>
        </v-dialog>
      </v-container>
    </v-app>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  mounted() {
    if (localStorage.getItem('users')) {
      try {
        this.users = JSON.parse(localStorage.getItem('users'));
      } catch(e) {
        console.log(e);
      }
    }
  },
  data() {
    return {
      headersTable: [
        { text: 'Name', align: 'start', value: 'name' },
        { text: 'Number', value: 'number' },
        { text: '', value: 'data-table-expand' }
      ],
      users: [
        { name: 'Саша', number: '+7 985 092 02 02' },
        { name: 'Паша', number: '+7 985 092 02 03', childrens: [ { name: 'Толя', number: '+7 985 092 02 04', childrens: [ { name: 'Толя', number: '+7 985 092 02 04' }, { name: 'Коля', number: '+7 185 092 02 05' }, ] }, { name: 'Коля', number: '+7 185 092 02 05' }, ] },
        { name: 'Алексей', number: '+7 985 092 02 04' },
        { name: 'Андрей', number: '+7 185 092 02 05', childrens: [ { name: 'Настя', number: '+7 985 092 02 04' }, { name: 'Оля', number: '+7 185 092 02 05' }, ] },
        { name: 'Анатолий', number: '+7 985 092 02 01' }
      ],
      expanded: [],
      expandedChild: [],
      showModalAddUser: false,
      formAddNewUser: {
        name: null,
        number: null,
        director: null,
        parent: null
      },
      directors: [
        'Сергей Александрович', 'Михаил Сафроновчи', 'Алексей Павлович'
      ]
    }
  },
  computed: {
    usersNames() {
      const names = []
      this.users.forEach(el => names.push(el.name))
      return names 
    },
    
  },
  methods: {
    saveUser() {
      if (this.formAddNewUser.name && this.formAddNewUser.number) {
        if (this.formAddNewUser.parent === null) {
          this.users.push(this.formAddNewUser)
          this.formAddNewUser = {
              name: null,
              number: null,
              director: null,
              parent: null
          }
        } else {
          this.users.forEach(el => {
            if (el.name === this.formAddNewUser.parent) {
              const childrens = el['childrens'] === undefined ? [] :  el.childrens
              childrens.push(this.formAddNewUser)
              el.childrens = childrens
            }
          })
        }
        this.saveUsersToLocalStorage()
      }
      this.showModalAddUser = false
    },
    childrens( user ) {
      const names = []
      user.childrens.forEach(el => names.push(el.name))
      console.log(names);

      return names 
    },
    saveUsersToLocalStorage() {
      let users = JSON.stringify(this.users)
      localStorage.setItem("users", users)
    }
  }
}
</script>

<style lang="scss" scoped>
@import '@/assets/global.scss';
  
</style>
