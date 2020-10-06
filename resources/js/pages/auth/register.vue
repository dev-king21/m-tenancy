<template>
  <div class="row d-flex align-center justify-center" style="height: 100%">
    <v-overlay :value="overlay">
      <v-progress-circular
        :size="70"
        :width="7"
        color="purple"
        indeterminate />
    </v-overlay>
    <v-card class="col-lg-6 px-10 m-auto" elevation="5">
      <v-card-title>
        <h1 class="text-middle">
          Welcome to
        </h1>
        <img src="logo.png" algin="left" style="padding:0px 10px;margin:auto;max-width:300px">
      </v-card-title> 
      <v-card-subtitle>
        <h5>Create your plany account</h5>
      </v-card-subtitle>
      <v-card-text>
        <div v-if="mustVerifyEmail" :title="$t('register')">
          <div class="alert alert-success" role="alert">
            {{ $t('verify_email_address') }}
          </div>
        </div>
        <div v-else :title="$t('register')">
          <form @submit.prevent="register" @keydown="form.onKeydown($event)">
            <validation-provider
              v-slot="{ errors }"
              name="Name"
              rules="required"
            >
              <v-text-field
                v-model="form.company_name"
                :error-messages="errors"
                label="Company Name"
                color="purple darken-2"
                suffix=".plany.io"
                required
              />
            </validation-provider>
            <validation-provider
              v-slot="{ errors }"
              name="Email"
              rules="required|email"
            >
              <v-text-field
                v-model="form.email"
                :error-messages="errors"
                label="Email address"
                color="purple darken-2"
                required
              />
            </validation-provider>
            <validation-provider
              v-slot="{ errors }"
              name="Name"
              rules="required"
            >
              <v-text-field
                v-model="form.password"
                :error-messages="errors"
                type="password"
                label="Password"
                color="purple darken-2"
                required
              />
            </validation-provider>
            <!-- <div class="form-group">
              <v-text-field
                v-model="form.company_name"
                :rules="nameRules"
                :counter="10"
                label="Company Name"
                prepend-icon="plany.io"
                required
              />
              <label for="exampleInputEmail1">Company name</label>
              <input v-model="form.company_name" :class="{ 'is-invalid': form.errors.has('company_name') }" class="form-control" type="text" name="companyname">
              <has-error :form="form" field="company_name" />
              <div class="text-right">
                <input
                  v-if="domain.edit" v-model="form.domain" v-focus
                  class="main tx-tfm-lower"
                  type="text"
                  name="company_name"
                  :class="{ 'is-invalid': form.errors.has('domain') }"
                  @blur="domain.edit = false; $emit('update')"
                  @keyup.enter="domain.edit=false; $emit('update')"
                >
                <div v-else>
                  <a class="main tx-tfm-lower" :href="`https://${form.domain ? form.domain : form.company_name}.plany.io`">{{ form.domain ? form.domain :form.company_name }}.plany.io</a>
                  <fa class="main" :icon="['fas', 'edit']" @click="domain.edit = true;" />
                </div>
                <has-error :form="form" field="company_name" />
              </div>
              <div v-show="domainError" class="text-danger">
                <small>{{ domainErrorMessage }}</small>
              </div>
            </div>
            <div class="form-group">
              <label for="exampleInputEmail1">Email address</label>
              <input v-model="form.email" :class="{ 'is-invalid': form.errors.has('email') }" class="form-control" type="text" name="email">
              <has-error :form="form" field="email" />
            </div>
            <div class="form-group">
              <label for="exampleInputEmail1">Set a password</label>
              <input v-model="form.password" :class="{ 'is-invalid': form.errors.has('password') }" class="form-control" type="password" name="password">
              <has-error :form="form" field="password" />
            </div> -->
            <div class="form-group mt-4">
              <div class="text-center">
                <v-btn
                  class="mr-4"
                  color="primary"
                  type="submit"
                  block
                >
                  {{ $t('signup') }}
                </v-btn>
                <!-- <v-button :loading="form.busy" class="btn btn-block mainbtn">
                  {{ $t('signup') }}
                </v-button> -->
              </div>
            </div>
            <div class="form-group">
              <p class="text-center">
                By sign up your account you agree with plany's <a href="#">Privacy Policy</a> and <a href="#">Terms of use</a>.
              </p>
            </div>
            <div class="">
              <div class="login-or">
                <hr class="hr-or">
                <span class="span-or">or</span>
              </div>
            </div>
            <login-with-google :domain="domainValidated" />
            <div class="form-group">
              <p class="text-left">
                Already have an account? <a id="signup" href="/login">Sign in</a>
              </p>
            </div>
          </form>
        </div>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import Form from 'vform'
import LoginWithGithub from '~/components/LoginWithGithub'
import LoginWithGoogle from '~/components/LoginWithGoogle'

import { library } from '@fortawesome/fontawesome-svg-core'
import { faEdit } from '@fortawesome/free-solid-svg-icons'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'

import * as axios from 'axios'
import { ValidationProvider, extend } from 'vee-validate'
import { required, email } from 'vee-validate/dist/rules'
import vLoading from '~/components/Loading'

// Add the required rule
extend('required', {
  ...required,
  message: 'This field is required'
}) 
// Add the email rule
extend('email', {
  ...email,
  message: 'This field must be a valid email'
})

library.add(faEdit)

export default {
  middleware: 'guest',

  components: {
    LoginWithGithub,
    LoginWithGoogle,
    ValidationProvider,
    vLoading
  },

  metaInfo () {
    return { title: this.$t('register') }
  },
  directives: {
    focus: {
      inserted (el) {
        el.focus()
      }
    }
  },

  data: () => ({
    form: new Form({
      company_name: '',
      domain: '',
      email: '',
      password: ''
    }),
    overlay: false,
    errors: null,
    domain: { edit: false },
    mustVerifyEmail: false,
    domainError: false,
    domainErrorMessage: null
  }),

  computed: {
    domainValidated () {
      return this.domainError ? null : this.form.domain
    }
  },

  watch: {
    'form.domain': {
      handler (newVal, oldVal) {
        if (newVal.length > 1) {
          this.domainError = false
          this.domainErrorMessage = null
        }
      }
    },
    'domain.edit': {
      handler (editing, wasEditing) {
        if (wasEditing && !editing) {
          this.verifyDomain()
        }
      }
    }
  },

  methods: {
    async register () {
      // Register the user.
      this.overlay = true
      const { data } = await this.form.post('/api/register')
      console.log(data)
      // Must verify email fist.
      this.overlay = false
      if (data.status) {
        this.mustVerifyEmail = true
      } else {
        // Log in the user.
        const { data: { token } } = await this.form.post('//' + data.base_url + ':8000/api/login')
        console.log('//' + data.base_url + ':8000/token/' + token)
        window.location.href = '//' + data.base_url + ':8000/token/' + token
      }
    },
    async verifyDomain () {
      let { data } = await axios.post('/api/domain/verify', { domain: this.form.domain })
      if (data.available) {
        this.domainError = false
        this.domainErrorMessage = null
      } else {
        this.domainError = true
        this.domainErrorMessage = 'Domain is already taken.'
      }
    }
  }
}
</script>
