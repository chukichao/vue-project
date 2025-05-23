<template>
  <h1>Registration Page</h1>

  <form class="form" @submit.prevent="registerAccount" ref="form">
    <div class="form-main">
      <label
        >Name:
        <input-ui
          v-model.trim="user.username"
          name="username"
          autocomplete="username"
          :class="{ required }"
        ></input-ui>
      </label>

      <label
        >Age:
        <input-ui
          v-model.trim.number="user.age"
          type="number"
          name="age"
          :class="{ required }"
        ></input-ui>
      </label>

      <label
        >Password:
        <input-ui
          v-model.trim="user.password"
          type="password"
          name="password"
          autocomplete="new-password"
          :class="{ required, confirm }"
        ></input-ui>
      </label>

      <label
        >Confirm Password:
        <input-ui
          v-model.trim="user.confirmPassword"
          type="password"
          name="confirmPassword"
          autocomplete="new-password"
          :class="{ confirm }"
        ></input-ui>
      </label>

      <small class="error-feedback" v-if="errorFeedback">{{
        errorFeedback
      }}</small>
    </div>

    <hr style="border-color: bisque" />

    <div class="form-country">
      <label for="country">Country:</label>
      <div>
        <select name="country" id="country" v-model="user.country">
          <option value=""></option>
          <option value="russia">Russia</option>
          <option value="belarus">Belarus</option>
          <option value="ukraine">Ukraine</option>
        </select>
      </div>
    </div>

    <label
      >Date of birth:
      <input-ui v-model="user.birthday" type="date" name="birthday"></input-ui>
    </label>

    <div class="form-gender">
      Gender:
      <div>
        <label>
          <input
            v-model="user.gender"
            type="radio"
            name="gender"
            value="male"
            checked
          />
          Male</label
        >
      </div>

      <div>
        <label
          ><input
            v-model="user.gender"
            type="radio"
            name="gender"
            value="female"
          />
          Female</label
        >
      </div>
    </div>

    <div class="form-skills">
      Skills:
      <div>
        <label
          ><input
            v-model="user.skills"
            type="checkbox"
            name="skills"
            value="html"
          />
          HTML</label
        >
      </div>

      <div>
        <label
          ><input
            v-model="user.skills"
            type="checkbox"
            name="skills"
            value="css"
          />
          CSS</label
        >
      </div>

      <div>
        <label
          ><input
            v-model="user.skills"
            type="checkbox"
            name="skills"
            value="js"
          />
          JS</label
        >
      </div>
    </div>

    <div class="form-rules">
      <label
        ><input v-model="user.agree" type="checkbox" name="agree" /> Я
        ознакомлен с правилами конфиденциальности (обязательно)</label
      >
    </div>

    <button-ui>Sign up</button-ui>
  </form>
</template>

<script>
import { mapStores } from 'pinia';
import { useUserStore } from '../store/UserStore.js';

export default {
  data() {
    return {
      user: {
        username: '',
        age: '',
        birthday: '',
        password: '',
        confirmPassword: '',
        country: '',
        gender: '',
        skills: [],
        agree: false,
      },
      errors: {
        requiredError: false,
        confirmError: false,
      },
      errorFeedback: '',
    };
  },

  methods: {
    registerAccount() {
      if (this.validate()) {
        this.userStore.login(this.user.username, this.user);
        this.$router.push('/');
      }
    },
    validate() {
      if (!this.user.username || !this.user.password) {
        this.errors.requiredError = true;
        this.errorFeedback = 'required field';
        return false;
      }
      this.errors.requiredError = false;

      if (this.user.password !== this.user.confirmPassword) {
        this.errors.confirmError = true;
        this.errorFeedback = 'passwords must match';
        return false;
      }
      this.errors.confirmError = false;

      this.errorFeedback = '';

      if (user.agree) {
        return true;
      }
    },
  },

  computed: {
    ...mapStores(useUserStore),
    required() {
      return this.errors.requiredError ? 'required' : '';
    },
    confirm() {
      return this.errors.confirmError ? 'confirm' : '';
    },
  },
};
</script>

<style scoped lang="scss">
.form {
  input,
  button {
    width: 100%;
    padding: 0.8rem;
    cursor: pointer;
  }

  input,
  select {
    &:focus {
      outline: 1px solid #333;
    }
  }

  label {
    cursor: pointer;
  }

  button {
    margin-top: 1rem;

    &:hover {
      background: bisque;
    }
  }

  &-main {
    margin: 4rem auto;
  }

  &-country {
    margin: 2rem auto;

    select {
      width: 100%;
      padding: 0.5rem;
    }
  }

  &-gender,
  &-skills,
  &-rules {
    margin: 2rem auto;

    input {
      width: auto;
    }
  }
}

.required,
.confirm {
  outline: 1px solid red;
}

.error-feedback {
  color: red;
}
</style>
