<template>
    <tr>
        <td>
            <router-link :to="item.product.get_absolute_url">
                {{ item.product.name }}
            </router-link>
        </td>
        <td>
            ${{ item.product.price }}
        </td>
        <td>
            <button class="fas fa-arrow-left" @click="decrementQuantity(item)"></button>
            &nbsp;
            {{ item.quantity }}
            &nbsp;
            <button class="fas fa-arrow-right" @click="incrementQuantity(item)"></button>
        </td>
        <td>
            ${{ getItemTotal(item).toFixed(2) }}
        </td>
        <td>
            <button
                class="delete"
                @click="removeFromCart(item)"
            >
            </button>
        </td>
    </tr>
</template>

<script>
export default {
    name: 'CartItem',
    props: {
        initialItem: Object
    },
    data() {
        return {
            item: this.initialItem
        }
    },
    methods: {
        getItemTotal(item) {
            return item.quantity * item.product.price
        },
        updateCart() {
            localStorage.setItem('cart', JSON.stringify(this.$store.state.cart))
        },
        removeFromCart(item) {
            this.$emit('removeFromCart', item)

            this.updateCart()
        },
        decrementQuantity(item) {
            item.quantity -= 1

            if (item.quantity === 0) {
                this.$emit('removeFromCart', item)
            }
            
            this.updateCart()
        },
        incrementQuantity(item) {
            item.quantity += 1

            this.updateCart()
        }
    }
}
</script>