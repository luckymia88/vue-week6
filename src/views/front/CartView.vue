<template>
    這是購物車頁面
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
                <tr v-for="item in cart.carts" :key="item.id" >
                  <td>
                    <button type="button" class="btn btn-outline-danger btn-sm" @click="deleteItem(item)" :disabled="item.id === loadingItem">
                      <i class="fas fa-spinner fa-pulse"></i>
                      x
                    </button>
                  </td>
                  <td>
                    {{ item.product.title }}
                  </td>
                  <td>
                    <div class="input-group input-group-sm">
                      <select name="" id="" class="form-control" v-model="item.qty" :disabled="item.id === loadingItem"
                      @change="updateCarts(item)" >
                        <option :value="i" v-for="i in 20" :key="`${i}12345`">{{i}}</option>
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
              <tr>
                <td colspan="3" class="text-end text-success">折扣價</td>
                <td class="text-end text-success">{{ cart.final_total }}</td>
              </tr>
            </tfoot>
          </table>
</template>

<script>

const { VITE_APP_URL, VITE_APP_PATH } = import.meta.env

export default {
  data () {
    return {
      products: [],
      productId: '',
      cart: {},
      loadingItem: '',
      form: {
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
    // 取得購物車
    getCarts () {
      this.$http.get(`${VITE_APP_URL}api/${VITE_APP_PATH}/cart`)
        .then((res) => {
          this.cart = res.data.data
        })
        .catch((err) => {
          alert(err.data.message)
        })
    },
    // 更新購物車
    updateCarts (item) { // 會需要2種id : 購物車&產品
      const data = {
        product_id: item.product.id, // 產品id
        qty: item.qty
      }
      this.loadingItem = item.id
      this.$http.put(`${VITE_APP_URL}api/${VITE_APP_PATH}/cart/${item.id}`, { data }) // 購物車id
        .then((res) => {
          this.getCarts()
          this.loadingItem = ''
        })
        .catch((err) => {
          alert(err.data.message)
        })
    },
    // 刪除單一品項
    deleteItem (item) {
      this.loadingItem = item.id
      this.$http.delete(`${VITE_APP_URL}api/${VITE_APP_PATH}/cart/${item.id}`)
        .then((res) => {
          this.getCarts()
          this.loadingItem = ''
        })
        .catch((err) => {
          alert(err.data.message)
        })
    },
    // 清空購物車
    deleteCarts (item) {
      this.$http.delete(`${VITE_APP_URL}api/${VITE_APP_PATH}/carts`)
        .then((res) => {
          this.getCarts()
        })
        .catch((err) => {
          alert(err.data.message)
        })
    },
    // 建立訂單
    createOrder () {
      const order = this.form
      this.$http.post(`${VITE_APP_URL}api/${VITE_APP_PATH}/order`, { data: order })
        .then((res) => {
          alert(res.data.message)
          // $refs可抓到，內層元件內的 ref="form"。(resetFrom()為js內建方法)
          this.$refs.form.resetForm()
          this.getCarts()
        })
        .catch((err) => {
          alert(err.message)
        })
    }
  },
  mounted () {
    this.getCarts()
  }
}
</script>
