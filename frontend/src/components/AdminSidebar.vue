<template>
  <div class="sidebar bg-info" :class="{ open: isOpened }">
    <div class="item-list">
      <AdminSidebarItem path="/admin" icon="fa fa-home" title="Trang chủ" :show-title="isOpened" />

      <AdminSidebarItem
        path="/admin/customers"
        icon="fa fa-users"
        title="Khách hàng"
        :show-title="isOpened"
      />
      <AdminSidebarItem
        path="/admin/products"
        icon="fa fa-box"
        title="Hàng hóa"
        :show-title="isOpened"
      />
      <AdminSidebarItem
        path="/admin/invoices"
        icon="fa fa-file-invoice"
        title="Đơn hàng"
        :show-title="isOpened"
      />
    </div>

    <!-- <div class="back-btn" @click="toggle">
      <i class="fa" :class="{ 'fa-angle-left': isOpened, 'fa-angle-right': !isOpened }"></i>
    </div> -->
  </div>
</template>

<script>
import { ref } from 'vue'
import AdminSidebarItem from './AdminSidebarItem.vue'

export default {
  name: 'AdminSidebar',
  components: { AdminSidebarItem },
  data() {
    const isOpened = ref(false)

    return { isOpened }
  },
  methods: {
    toggle() {
      this.isOpened = !this.isOpened
    }
  }
}
</script>

<style scoped>
.sidebar {
  position: relative;
  top: 25%;
  right: 10px;
  height: fit-content;
  max-height: 350px;
  width: 64px;
  border-right: 1px solid #fff;
  display: flex;
  flex-direction: column;
  transition: 0.6s ease;
  border-radius: 5px 5px 5px 5px/25px 25px 25px 5px;
}

.sidebar.open {
  width: 350px;
}

.item-list {
  flex: 1;
  overflow: auto;
}

.back-btn {
  color: #fff;
  cursor: pointer;
  padding: 10px 20px;
  display: flex;
  justify-content: center;
}

.back-btn:hover {
  background: #000;
}
</style>
