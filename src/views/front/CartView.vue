<template>
  <h1>這是購物車頁面</h1>
  <table class="table align-middle">
    <thead>
      <tr>
        <th></th>
        <th>品名</th>
        <th style="width: 150px">數量/單位</th>
        <th>單價</th>
      </tr>
    </thead>
    <tbody>
      <template v-if="cart.carts">
        <tr v-for="item in cart.carts" :key="item.id">
          <td>
            <button type="button" class="btn btn-outline-danger btn-sm" @click="deleteCartItem(item)" :disabled="lodingItem === item.id">
              <i class="fas fa-spinner fa-pulse" v-if="lodingItem === item.id"></i>
              x
            </button>
          </td>
          <td>
            {{ item.product.title }}
          </td>
          <td>
            <div class="input-group input-group-sm">
              <select name="" id="" class="form-select" v-model="item.qty" @change="updateCart(item)" :disabled="lodingItem === item.id">
                <option :value="i" v-for="i in 20" :key="i+'45621'">{{i}}</option>
              </select>
            </div>
          </td>
          <td class="text-end">
            {{ item.total }}
          </td>
        </tr>
      </template>
    </tbody>
    <tfoot>
      <tr>
        <td colspan="3" class="text-end">總計</td>
        <td class="text-end">{{ cart.total }}</td>
      </tr>
    </tfoot>
  </table>
</template>

<script>
const { VITE_APP_URL, VIPE_APP_PATH } = import.meta.env
export default {
  data () {
    return {
      text: 'hello',
      products: [],
      productId: '',
      cart: {},
      lodingItem: '',
      lodingItema: '',
      isLoading: false,
      tempProduct: {},
      data: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: ''
        },
        message: ''
      }

    }
  },
  methods: {
    // loading
    doAjax () {
      this.isLoading = true
      // simulate AJAX
      setTimeout(() => {
        this.isLoading = false
      }, 1000)
    },
    // 取得購物車
    getCart () {
      this.$http.get(`${VITE_APP_URL}/v2/api/${VIPE_APP_PATH}/cart`)
        .then(res => {
          this.cart = res.data.data
          console.log(this.cart)
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    // 更新購物車
    updateCart (item) { // 購物車id 產品id
      this.lodingItem = item.id
      const data = {
        product_id: item.product.id,
        qty: item.qty
      }
      console.log(data)
      this.$http.put(`${VITE_APP_URL}/v2/api/${VIPE_APP_PATH}/cart/${item.id}`, { data })
        .then(res => {
          alert('更新成功')
          this.getCart()
          this.lodingItem = ''
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
    },
    // 刪除單一品項購物車
    deleteCartItem (item) {
      this.lodingItem = item.id
      this.$http.delete(`${VITE_APP_URL}/v2/api/${VIPE_APP_PATH}/cart/${item.id}`)
        .then(res => {
          alert('刪除成功')
          this.lodingItem = ''
          this.getCart()
        })
        .catch((err) => {
          alert(err.data.message)
          this.lodingItem = ''
        })
    },
    // 刪除全部品項購物車
    deleteCartAll () {
      this.lodingItem = '123'
      this.$http.delete(`${VITE_APP_URL}/v2/api/${VIPE_APP_PATH}/carts`)
        .then(res => {
          console.log(res.data)
          alert('刪除成功')
          this.lodingItem = ''
          this.getCart()
        })
        .catch((err) => {
          alert(err.data.message)
          this.lodingItem = ''
        })
    },
    // 結帳
    order () {
      console.log(this.data)
      const orderData = {
        data: this.data
      }

      this.$http.post(`${VITE_APP_URL}/v2/api/${VIPE_APP_PATH}/order`, orderData)
        .then(res => {
          console.log(res.data)
          this.$refs.form.resetForm()
        })
        .catch((err) => {
          alert(err.response.data.message)
        })
      this.cart = ''
      this.$refs.form.resetForm()
    },
    // reset表單
    resetForm () {
      console.log(this.$refs.form)
      this.$refs.form.resetForm()
    }
  },
  mounted () {
    this.doAjax()
    this.getCart()
  }
}
</script>
