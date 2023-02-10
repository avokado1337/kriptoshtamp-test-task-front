<template>
  <div class="wrapper">
    <div class="panel">
      <div class="search-form">
        <div class="text-search">
          <input
            v-model="searchName"
            class="form-control mr-sm-2"
            type="search"
            placeholder="Search by fullname"
            aria-label="Search"
          />
        </div>
        <div class="datepicker">
          <Datepicker
            v-model="date"
            placeholder="Select date"
            :enable-time-picker="false"
            >Pick a date</Datepicker
          >
        </div>
      </div>
      <div class="buttons">
        <div class="search-btn-group">
          <div class="search-button">
            <button
              v-on:click="Search(searchName, date)"
              class="btn btn-outline-success my-2 my-sm-0"
              type="submit"
            >
              Search
            </button>
          </div>
          <div class="reset-button">
            <button
              v-on:click="getData()"
              class="btn btn-outline-danger my-2 my-sm-0"
              type="submit"
            >
              Reset
            </button>
          </div>
        </div>
        <div class="add-btn-group">
          <button
            @click="addModal()"
            class="btn btn-outline-primary my-2 my-sm-0"
            type="submit"
          >
            Add new user
          </button>
        </div>
      </div>
      <form class="form-inline my-2 my-lg-0"></form>
      <table class="table table-striped">
        <thead>
          <tr>
            <th scope="col">
              #
              <font-awesome-icon
                v-show="orderBy === 'asc'"
                @click="getData(currentPage, 'Id', 'asc')"
                icon="fa-solid fa-sort-up"
              />
              <font-awesome-icon
                v-show="orderBy === 'desc'"
                @click="getData(currentPage, 'Id', 'desc')"
                icon="fa-solid fa-sort-down"
              />
            </th>
            <th scope="col">
              Name
              <font-awesome-icon
                v-show="orderBy === 'asc'"
                @click="getData(currentPage, 'Name', 'asc')"
                icon="fa-solid fa-sort-up"
              />
              <font-awesome-icon
                v-show="orderBy === 'desc'"
                @click="getData(currentPage, 'Name', 'desc')"
                icon="fa-solid fa-sort-down"
              />
            </th>
            <th scope="col">
              Surname
              <font-awesome-icon
                v-show="orderBy === 'asc'"
                @click="getData(currentPage, 'Surname', 'asc')"
                icon="fa-solid fa-sort-up"
              />
              <font-awesome-icon
                v-show="orderBy === 'desc'"
                @click="getData(currentPage, 'Surname', 'desc')"
                icon="fa-solid fa-sort-down"
              />
            </th>
            <th scope="col">
              Middlename
              <font-awesome-icon
                v-show="orderBy === 'asc'"
                @click="getData(currentPage, 'Middlename', 'asc')"
                icon="fa-solid fa-sort-up"
              />
              <font-awesome-icon
                v-show="orderBy === 'desc'"
                @click="getData(currentPage, 'Middlename', 'desc')"
                icon="fa-solid fa-sort-down"
              />
            </th>
            <th scope="col">
              Date of birth
              <font-awesome-icon
                v-show="orderBy === 'asc'"
                @click="getData(currentPage, 'DateOfBirth', 'asc')"
                icon="fa-solid fa-sort-up"
              />
              <font-awesome-icon
                v-show="orderBy === 'desc'"
                @click="getData(currentPage, 'DateOfBirth', 'desc')"
                icon="fa-solid fa-sort-down"
              />
            </th>
            <th scope="col">
              Country
              <font-awesome-icon
                v-show="orderBy === 'asc'"
                @click="getData(currentPage, 'Country', 'asc')"
                icon="fa-solid fa-sort-up"
              />
              <font-awesome-icon
                v-show="orderBy === 'desc'"
                @click="getData(currentPage, 'Country', 'desc')"
                icon="fa-solid fa-sort-down"
              />
            </th>
            <th scope="col">
              City
              <font-awesome-icon
                v-show="orderBy === 'asc'"
                @click="getData(currentPage, 'City', 'asc')"
                icon="fa-solid fa-sort-up"
              />
              <font-awesome-icon
                v-show="orderBy === 'desc'"
                @click="getData(currentPage, 'City', 'desc')"
                icon="fa-solid fa-sort-down"
              />
            </th>
            <th scope="col">
              Gender
              <font-awesome-icon
                v-show="orderBy === 'asc'"
                @click="getData(currentPage, 'Gender', 'asc')"
                icon="fa-solid fa-sort-up"
              />
              <font-awesome-icon
                v-show="orderBy === 'desc'"
                @click="getData(currentPage, 'Gender', 'desc')"
                icon="fa-solid fa-sort-down"
              />
            </th>
            <th scope="col">Update</th>
            <th scope="col">Delete</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="user in userList" :key="user.id">
            <th scope="row">{{ user.id }}</th>
            <td>{{ user.name }}</td>
            <td>{{ user.surname }}</td>
            <td>{{ user.middleName }}</td>
            <td>{{ getFormatDate(new Date(user.dateOfBirth)) }}</td>
            <td>{{ user.country }}</td>
            <td>{{ user.city }}</td>
            <td>{{ user.gender }}</td>
            <td>
              <button
                @click="updateModal(user)"
                type="button"
                class="btn btn-warning btn-sm"
              >
                Update
              </button>
            </td>
            <td>
              <button
                v-on:click="Delete(user.id)"
                type="button"
                class="btn btn-danger btn-sm"
              >
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
      <div class="pages">
        <span
          @click="getData(index)"
          class="page"
          v-for="index in totalPages"
          :key="index"
        >
          {{ index }}
        </span>
      </div>
    </div>
    <ActionModal
      :userObj="userObj"
      :action="action"
      v-show="showModal"
      @close-modal="showModal = false"
      @update-users="getData(currentPage)"
      @add-user="getData(currentPage)"
    />
  </div>
</template>

<script>
import moment from "moment";
import Datepicker from "@vuepic/vue-datepicker";
import "@vuepic/vue-datepicker/dist/main.css";
import ActionModal from "./ActionModal.vue";
export default {
  components: { Datepicker, ActionModal },
  name: "ListPanel",
  data() {
    return {
      userObj: {
        id: 0,
        name: "",
        surname: "",
        middleName: "",
        dateOfBirth: "",
        country: "",
        city: "",
        gender: "",
      },
      userList: [],
      date: null,
      wasDeleted: false,
      searchName: "",
      showModal: false,
      action: "",
      totalPages: 0,
      currentPage: 0,
      param: "",
      orderBy: "asc",
    };
  },
  methods: {
    async getData(page, param, orderBy) {
      if (orderBy === "asc") this.orderBy = "desc";
      else this.orderBy = "asc";
      const request = await fetch(
        `https://localhost:44357/users/${page ?? 1}/${param ?? "Id"}/${
          orderBy ?? "asc"
        }`,
        {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
          },
        }
      );
      const response = await request.json();
      this.totalPages = response.pages;
      this.currentPage = response.currentPage;
      this.userList = response.users;
      this.date = undefined;
      this.searchName = undefined;
    },
    getFormatDate: function (date) {
      return moment(date).format("DD/MM/YYYY");
    },
    getFormatForInput: function (date) {
      return moment(date).format("YYYY-MM-DD");
    },
    async Delete(id) {
      const request = await fetch(`https://localhost:44357/user/${id}`, {
        method: "DELETE",
        headers: {
          "Content-Type": "application/json",
        },
      });
      const response = await request.json();
      this.wasDeleted = response;
      if (this.wasDeleted === true) {
        var index = this.userList.findIndex((x) => x.id === id);
        this.userList.splice(index, 1);
        await this.getData(this.currentPage);
      } else alert("Couldn't delete user");
    },
    async Search(name, date) {
      const request = await fetch(
        `https://localhost:44357/User/users/search/${name}/${moment(
          date
        ).format("YYYY-MM-DD")}`,
        {
          method: "GET",
          headers: {
            "Content-Type": "application/json",
          },
        }
      );
      const response = await request.json();
      this.userList = response;
    },
    async updateModal(user) {
      this.userObj.id = user.id;
      this.userObj.name = user.name;
      this.userObj.surname = user.surname;
      this.userObj.middleName = user.middleName;
      this.userObj.dateOfBirth = user.dateOfBirth;
      this.userObj.country = user.country;
      this.userObj.city = user.city;
      this.userObj.gender = user.gender;
      this.action = "update";
      this.showModal = true;
    },
    async addModal() {
      this.userObj.id = undefined;
      this.userObj.name = "";
      this.userObj.surname = "";
      this.userObj.middleName = "";
      this.userObj.dateOfBirth = "";
      this.userObj.country = "";
      this.userObj.city = "";
      this.userObj.gender = "";

      this.action = "add";
      this.showModal = true;
    },
    async getUpdatedUser(user) {
      var index = this.userList.findIndex((x) => x.id === user.id);
      this.userList[index] = user;
    },
  },
  mounted() {
    this.getData();
  },
};
</script>

<style scoped>
.wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.panel {
  width: 130vh;
  height: auto;
}

.search-form {
  width: 63vh;
  display: flex;
  justify-content: space-between;
}

.search-form > * {
  margin-top: 10px;
  margin-right: 20px;
}

.buttons {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.buttons > * {
  margin-right: 20px;
  margin-top: 10px;
  margin-bottom: 10px;
  margin-left: 0;
  width: auto;
}

.search-btn-group {
  display: flex;
  flex-direction: row;
  width: auto;
}
.search-btn-group > * {
  margin-right: 20px;
}

.add-btn-group {
  width: auto;
}

.pages {
  display: flex;
  justify-content: center;
  align-items: center;
}

span {
  cursor: pointer;
  color: blue;
  text-decoration: underline;
  margin: 5px;
}
</style>
