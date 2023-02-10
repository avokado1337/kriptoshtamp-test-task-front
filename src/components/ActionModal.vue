<template>
  <form class="wrapper-form">
    <div class="forms">
      <div class="form-group">
        <input
          type="text"
          class="form-control"
          placeholder="Name"
          :value="userObj.name ?? ''"
          @input="
            (event) => (newUserObj.name = event.target.value ?? userObj.name)
          "
        />
        <input
          type="text"
          class="form-control"
          placeholder="Surname"
          :value="userObj.surname ?? ''"
          @input="
            (event) =>
              (newUserObj.surname = event.target.value ?? userObj.surname)
          "
        />
        <input
          type="text"
          class="form-control"
          placeholder="Middlename"
          :value="userObj.middleName ?? ''"
          @input="
            (event) =>
              (newUserObj.middleName = event.target.value ?? userObj.middleName)
          "
        />
        <input
          type="date"
          class="form-control"
          placeholder="Date of birth"
          :value="getFormatForInput(userObj.dateOfBirth) ?? undefined"
          @input="
            (event) =>
              (newUserObj.dateOfBirth =
                event.target.value ?? userObj.dateOfBirth)
          "
        />
        <input
          type="text"
          class="form-control"
          placeholder="Country"
          :value="userObj.country ?? ''"
          @input="
            (event) =>
              (newUserObj.country = event.target.value ?? userObj.country)
          "
        />
        <input
          type="text"
          class="form-control"
          placeholder="City"
          :value="userObj.city ?? ''"
          @input="
            (event) => (newUserObj.city = event.target.value ?? userObj.city)
          "
        />
        <input
          type="text"
          class="form-control"
          placeholder="Gender"
          :value="userObj.gender ?? ''"
          @input="
            (event) =>
              (newUserObj.gender = event.target.value ?? userObj.gender)
          "
        />
      </div>
      <div class="buttons">
        <button
          v-if="action === 'update'"
          @click="Update(newUserObj)"
          type="button"
          class="btn btn-primary update"
        >
          Update
        </button>
        <button
          v-else
          @click="Add(newUserObj)"
          type="button"
          class="btn btn-primary update"
        >
          Add
        </button>
        <button
          @click="$emit('close-modal')"
          type="button"
          class="btn btn-danger close"
        >
          Close
        </button>
      </div>
    </div>
  </form>
</template>
<script>
import moment from "moment";
export default {
  props: {
    userObj: Object,
    action: String,
  },
  data() {
    return {
      newUserObj: this.userObj,
    };
  },
  methods: {
    getFormatForInput: function (date) {
      if (date) return moment(date).format("YYYY-MM-DD");
    },
    async Update(user) {
      console.log(JSON.stringify(user));
      const request = await fetch("https://localhost:44357/user", {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(user),
      });
      const response = await request.json();
      if (response === true) {
        this.$emit("close-modal");
        this.$emit("update-users");
      } else alert("fail");
    },
    async Add(user) {
      console.log(JSON.stringify(user));
      const request = await fetch("https://localhost:44357/user", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify(user),
      });
      await request.json();
      this.$emit("close-modal");
      this.$emit("add-user");
      //   if (response) {
      //   } else {
      //     alert("Failed to add user");
      //   }
    },
  },
  update(key, value) {
    this.$emit("input", { ...this.value, [key]: value });
  },
};
</script>
<style scoped>
.buttons {
  display: flex;
  flex-direction: row;
  justify-content: center;
}

.buttons > * {
  margin: 20px;
}
.wrapper-form {
  position: fixed;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  display: flex;
  justify-content: center;
  background-color: #000000da;
}

.forms {
  text-align: center;
  background-color: white;
  height: 500px;
  width: 500px;
  margin-top: 10%;
  padding: 30px 0;
  border-radius: 20px;
}

.form-group {
  margin: 20px;
  padding-top: 0;
}
.form-group > * {
  margin-bottom: 10px;
}

.confirm-modal {
  display: flex;
  justify-content: center;
  align-items: center;
}
.confirm-modal-content {
  display: flex;
  flex-direction: column;
  padding: 1rem;
  background: #fff;
  border-radius: 0.5rem;
}
.confirm-modal-content > * + * {
  margin: 0.5rem 0;
}
.confirm-modal-content h1 {
  font-size: 1.375rem;
}
.confirm-modal-content button {
  margin: 0.25rem 0 0 auto;
  padding: 0 8px;
  border: 1px solid;
  border-radius: 0.5rem;
}
.dark .confirm-modal-content {
  background: #000;
}
</style>
