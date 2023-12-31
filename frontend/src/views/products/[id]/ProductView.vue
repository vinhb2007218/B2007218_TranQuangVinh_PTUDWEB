<template>
  <div class="container py-4">
    <span class="spinner-border" v-if="!product"></span>
    <div class="row" v-else>
      <div class="col col-12 col-md-6">
        <h2>{{ product.ten }}</h2>

        <p>
          {{ product.mota }}
        </p>

        <div class="d-flex gap-1 mb-3">
          <button class="btn btn-success" disabled>
            <i class="fa-solid fa-dollar-sign"></i>
            {{ vndFormat(product.gia) }}
          </button>

          <button class="btn btn-secondary" disabled>Còn lại: {{ product.soluong }}</button>
        </div>

        <label class="form-label">Đặt hàng</label>
        <input
          class="form-control mb-2"
          placeholder="Số lượng"
          type="number"
          v-model="quantity"
          min="1"
          :max="product.soluong"
        />

        <div class="d-flex">
          <button @click="addToCart" class="btn btn-secondary d-block mb-2">
            <i class="fa fa-cart-plus"> </i>
            Thêm vào giỏ hàng
          </button>

          <button class="btn btn-primary d-block" @click="buy">Mua</button>
        </div>

        <div v-if="cartItem">
          Đã có <span style="font-weight: bold">{{ cartItem.soluong }}</span> sản phẩm này trong giỏ
          hàng
        </div>
        <div class="d-flex gap-2 my-2" style="overflow: auto">
          <img
            v-for="(image, index) in product.images"
            :key="image.id"
            :src="fileService.getFileUrl(image.path)"
            :alt="image.ten"
            style="width: 150px; aspect-ratio: 1; cursor: pointer"
            class="product-image"
            :class="{
              'border-primary': imgIndex === index
            }"
            @click="imgIndex = index"
          />
        </div>
      </div>
      <div class="col col-12 col-md-6">
        <img
          v-if="product.images[imgIndex]"
          :src="fileService.getFileUrl(product.images[imgIndex].path)"
          :alt="product.images[imgIndex].ten"
          class="product-image"
        />
      </div>
    </div>

    <h3>Các sản phẩm khác</h3>

    <div class="row">
      <div
        class="col col-12 col-md-4 col-lg-3 col-xl-2 mb-2 p-1"
        v-for="product in otherProducts"
        :key="product.id"
      >
        <ProductCard :product="product" />
      </div>
    </div>
  </div>
</template>

<script>
import ProductCard from '@/components/ProductCard.vue'
import fileService from '@/services/file.service'
import hanghoaService from '@/services/hanghoa.service'
import { useCartStore } from '@/stores/cart'
import { useUserStore } from '@/stores/user'
import vndFormat from '@/utils/vndFormat'
import { ref } from 'vue'

const cartStore = useCartStore()
const userStore = useUserStore()
export default {
  name: 'ProductView',
  data() {
    const product = ref(null)
    const imgIndex = ref(0)
    const quantity = ref(1)
    const cartItem = ref(null)
    const otherProducts = ref([])
    return { product, fileService, imgIndex, quantity, cartItem, otherProducts }
  },
  watch: {
    product(value) {
      if (!value) return
      this.cartItem = cartStore.getItemById(value.id)
    },
    cartStore() {
      if (!this.product) return
      this.cartItem = cartStore.getItemById(this.product.id)
    },
    $route() {
      this.getProductData()
    }
  },
  methods: {
    async getProductData() {
      try {
        const { id } = this.$route.params
        const res = await hanghoaService.getById(id)
        this.product = res
      } catch (error) {
        console.log(error)
      }
    },
    addToCart() {
      if (!userStore.user) {
        this.$router.push('/auth/login')
        return
      }
      cartStore.addToCart({
        id: this.product.id,
        soluong: this.quantity
      })
      this.cartItem = cartStore.getItemById(this.product.id)
      this.$toast.success('Thêm vào giỏ hàng thành công!', { position: 'top-right' })
    },
    vndFormat,
    async getOtherProducts() {
      try {
        this.otherProducts = (await hanghoaService.getAll()).data
      } catch (error) {
        this.$toast.error(error.message)
      }
    },
    buy() {
      this.addToCart()
      this.$router.push('/cart')
    }
  },
  beforeMount() {
    this.getProductData()
    this.getOtherProducts()
  },
  components: { ProductCard }
}
</script>

<style scoped>
.product-image {
  width: 100%;
  aspect-ratio: 1;
  object-fit: cover;
  border: 2px solid #000;
  border-radius: 5px 5px 5px 5px/25px 25px 25px 5px;
}
</style>
