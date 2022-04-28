<template>
  <div id="test" :class="{'disable-overflow': ifeditUser}" v-cloak>
  <div class="page">
  <div class="main-operation">
         <el-button type="info" @click="addUser">Add</el-button>
    <input type="text" v-model="userSearched" placeholder="search id, name, email...">
  </div>
  <table class="users">
    <thead>
      <tr>
        <th v-for="(item, index) in thead" :key="index" @click="switchOrder(item)">{{item}}</th>
      </tr>
    </thead>
    <tbody v-if="!users.length">
      <tr>
        <td colspan="8" align="center">没有数据</td>
      </tr>
    </tbody>
    <tbody v-esle>
      <tr class="user" v-for="(user, index) in usersFiltered" :key="user">
        <td>{{user.id}}</td>
        <td>{{user.name}}</td>
        <td>{{user.username}}</td>
        <td>{{user.email}}</td>
        <td>{{user.address.city}}</td>
        <td>{{user.website}}</td>
        <td>{{user.company.name}}</td>
        <td>{{index}}</td>
        <td>
          <el-button  @click="editUser(user)" >Edit</el-button>
          <el-button  size="mini"
          type="danger" @click="open(user)">delete</el-button>
        </td>
      </tr>
    </tbody>
  </table>
  <section v-if="ifeditUser" class="user-modal" @click.prevent="handleCancel">
    <form v-if="userEdited" @click.stop="">
      <h3 v-if="userOperation === 'add'" class="modal-title">Add User</h3>
      <h3 v-if="userOperation === 'edit'" class="modal-title">Edit User</h3>
      <div class="form-group" v-for="(value, prop, index) in userEdited" :key="index" >
        <label :for="prop">{{prop}}:</label>
        <input :id="prop" :readonly="prop === 'id'" type="text" v-if="prop === 'address'" v-model="userEdited[prop]['city']">
        <input :id="prop" :readonly="prop === 'id'" type="text" v-else-if="prop === 'company'" v-model="userEdited[prop]['name']">
        <input :id="prop" :readonly="prop === 'id'" type="text" v-else v-model="userEdited[prop]">
      </div>
      <div class="form-group">
        <button class="submit" type="button" @click="handleSubmit">Submit</button>
        <button class="cancel" type="button" @click="handleCancel">Cancel</button>
      </div>
    </form>
  </section>
  </div>
</div>

</template>

<script>
import axios from 'axios'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  
  data() {
    return {
       ifOpenUserModal: false,
    ifeditUser: false,
    userEditedIndex: undefined,
    userEdited: {},
    userSearched: 'dd',
    userOperation: '',
    users: JSON.parse(window.sessionStorage.getItem('users')) || [],
    thead: ['id', 'name', 'username', 'email', 'address', 'website', 'company', 'operation','phone'],
    }
  },
   created() {
    this.initUsers()
    this.usersFiltered = this.users
  },
    computed: {
    newUser() {
      return {
        id: Math.max(...(this.users.map((user) => user.id))) + 1,
        name: '',
        username: '',
        email: '',
        address: {
          city: ''
        },
        zipcode:'',
        website: '',
        company: {
          name: ''
        }
      }
    },
    usersFiltered() {
      return this.users.filter(userItem => {
        let sourceMatched = JSON.stringify(userItem).toLowerCase()
        return Boolean(sourceMatched.includes(this.userSearched.toLowerCase()))
      })
    }
  },
    methods: {
    // not work for now
    switchOrder(theadItem) {
      this.usersFiltered.sort((prevUser, nextUser) => {
        if (prevUser[theadItem] > nextUser[theadItem]) {
          return 1;
        }
        if (prevUser[theadItem] < nextUser[theadItem]) {
          return -1;
        } 
        return 0;
      })
    },
    initUsers() {
      if (this.users) {
        this.fetchUsers()
      }
    },
    fetchUsers() {
      axios.get('https://jsonplaceholder.typicode.com/users')
        .then(res => {
        if (res && res.status === 200) {
          window.sessionStorage.setItem('users', JSON.stringify(res.data))
          this.users = res.data
        }
      })
    },
    addUser() {
      this.ifeditUser = true
      this.userOperation = 'add'
      this.userEdited = (this.newUser)
      this.userEditedIndex = this.users.length
    },
    editUser(user) {
      console.log(user);
      this.ifeditUser = true
      this.userOperation = 'edit'
      this.userEdited = (user)
      this.userEditedIndex = this.users.findIndex(userItem => userItem === user)
    },
    deleteUser(user) {
      this.users.sort()
      let userIndex = this.users.findIndex(userItem => userItem === user)
      this.users.splice(userIndex, 1)
      window.sessionStorage.setItem('users', JSON.stringify(this.users))
    },
        open(user) {
       
        this.$alert('مطمَن هستید که این ایتم پاک شود؟', 'نکته', {
          confirmButtonText: 'OK',
          callback: action => {
              this.users.sort()
      let userIndex = this.users.findIndex(userItem => userItem === user)
      this.users.splice(userIndex, 1)
      window.sessionStorage.setItem('users', JSON.stringify(this.users))
            this.$message({
              type: 'info',
              message: `action: ${ action }`
            });
          }
        });
      },
    handleSubmit() {
      this.$set(this.users, this.userEditedIndex, this.userEdited)
      window.sessionStorage.setItem('users', JSON.stringify(this.users))
      this.ifeditUser = false
    },
    handleCancel() {
      this.ifeditUser = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

[v-cloak] {
	display: none;
}

/*打开Modal, 禁止滚动条滚动*/
.disable-overflow {
	height: 100%;
	overflow: hidden;
}

.title {
	text-align: center;
	padding: 20px;
}

.main-operation {
    display: flex;
    justify-content: center;
    margin: 20px 0;
    margin-left: 68%;
}

.main-operation button {
	background-color: rgba(0, 148, 255, .6);
	border: 0;
	outline: none;
}

.main-operation input {
	padding-left: 5px;
	font-size: 14px;
}

table {
	border-collapse: collapse;
	width: 80%;
	margin: 0 auto;
}

thead {
	background: white;
	font-size: 20px;
}

thead tr th {
	padding: 10px 20px;
	width: 150px;
}

thead th:last-child {
    min-width: 200px;
}

tbody tr {
	cursor: pointer;
}

tbody tr:hover {
	background: #eee;
}

tr td,
tr th {
	border: 1px solid #ccc;
	padding: 10px;
	text-align: center;
}

.user-modal {
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	background: rgba(0,0,0, .8);
	z-index: 2;
	display: flex;
	justify-content: center;
	align-items: center;
	cursor: pointer;
}

.modal-title {
    border-bottom: 1px solid #ccc;
    padding-bottom: 10px;
    min-width: 200px;
    display: inline-block;
}

form {
  width: 500px;
  border: 1px solid red;
  padding: 20px;
  margin-top: 20px;
  /*z-index: 4;*/
  background: #fff;
  cursor: initial;
}

.form-group {
	height: 30px;
	line-height: 30px;
	margin: 20px 0;
}

form > .form-group:last-child {
	display: flex;
	justify-content: center;
	margin-top: 30px;
	min-width: 200px;
}

label {
	display: inline-block;
	min-width: 100px;
	font-weight: 600;
}

input {
	padding-left: 5px;
	min-width: 300px;
}

button {
	margin: 0 15px;
	padding: 5px 10px;
	font-size: 18px;
	line-height: 1;
	cursor: pointer;
}

.submit {
	background-color: rgba(0, 111, 255, 0.6);
}

.cancel {
	background-color: orangered;
}


</style>
