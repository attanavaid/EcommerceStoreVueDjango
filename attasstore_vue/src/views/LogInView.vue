<template>
    <div class="LogInView">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">
                    Log In
                </h1>

                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label>Username</label>
                        <div class="control">
                            <input type="text" class="input" v-model="username">
                        </div>
                    </div>

                    <div class="field">
                        <label>Password</label>
                        <div class="control">
                            <input type="password" class="input" v-model="password">
                        </div>
                    </div>

                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="error in errors" v-bind:key="error">
                            {{ error }}
                        </p>
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-dark">
                                Log In
                            </button>
                        </div>
                    </div>

                    <hr>

                    <p class="is-light">
                        Don't have an account? <router-link to="/sign-up">Click here</router-link> to sign up!
                    </p>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'

export default {
    name: 'LogInView',
    data() {
        return {
            username: '',
            password: '',
            errors: []
        }
    },
    mounted() {
        document.title = "Log In | Atta's Store"
    },
    methods: {
        async submitForm() {
            axios.defaults.headers.common['Authorization'] = ''
            localStorage.removeItem('token')

            const formData = {
                username: this.username,
                password: this.password
            }
            
            await axios
            .post('/api/v1/token/login/', formData)
            .then(response => {
                const token = response.data.auth_token
                this.$store.commit('setToken', token)
                axios.defaults.headers.common['Authorization'] = 'Token ' + token
                localStorage.setItem('token', token)
                const toPath = this.$route.query.to || '/cart'
                this.$router.push(toPath)

                toast({
                    message: 'Log in successful!',
                    type: 'is-success',
                    dismissible: true,
                    pauseOnHover: true,
                    duration: 2000,
                    position: 'bottom-right'
                })
            })
            .catch(error => {
                if (error.response) {
                    for (const property in error.response.data) {
                        this.errors.push(`${property}: ${error.response.data[property]}`)
                    }

                    console.log(JSON.stringify(error.response.data))
                }
                
                else if (error.message) {
                    this.errors.push('Something went wrong. Please try again.')

                    console.log(JSON.stringify(error))
                }

                toast({
                    message: 'Something went wrong. Please try again.',
                    type: 'is-danger',
                    dismissible: true,
                    pauseOnHover: true,
                    duration: 2000,
                    position: 'bottom-right',
                })
            })
        }
    }
}
</script>