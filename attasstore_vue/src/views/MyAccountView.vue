<template>
    <div class="MyAccountView">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">
                    My Account
                </h1>
            </div>

            <div class="column is-12">
                <button class="button is-danger" @click="logout()">
                    Log Out
                </button>
            </div>

            <hr>

            <div class="column is-12">
                <h2 class="subtitle">
                    My Orders
                </h2>

                <OrderSummary
                    v-for="order in orders"
                    v-bind:key="order.id"
                    v-bind:order="order"
                />
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import OrderSummary from '@/components/OrderSummary'

export default {
    name: 'MyAccountView',
    data() {
        return {
            orders: []
        }
    },
    mounted() {
        document.title = "My Account | Atta's Store"

        this.getMyOrders()
    },
    components: {
        OrderSummary
    },
    methods: {
        logout() {
            axios.defaults.headers.common['Authorization'] = ''

            localStorage.removeItem('token')
            localStorage.removeItem('username')
            localStorage.removeItem('userid')

            this.$store.commit('removeToken')
            this.$route.push('/')
        },
        async getMyOrders() {
            this.$store.commit('setIsLoading', true)

            await axios
            .get('/api/v1/orders/')
            .then(response => {
                this.orders = response.data
            })
            .catch(error => {
                console.log(error)
            })
                
            this.$store.commit('setIsLoading', false)
        }
    }
}
</script>