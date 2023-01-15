<script setup lang="ts">
import { ref } from "vue";
import ProductSearchFilters from "../components/Search/ProductSearchFilters.vue";

// const similarSearchDataBody = [
//   { field: "brand" },
//   { field: "dealer" },
//   { field: "count" },
//   { field: "deadline" },
//   { field: "price" },
//   { field: "date" },
//   { slot: "cart" },
//   { slot: "delivery" },
// ];

const searchDataBody = [
  { field: "make_name"},
  { field: "supplier_name" },
  { field: "count",
    fn: (data: { count: number; }) => {
    if (data.count == 10000){
      return "Под заказ "
    }
      return data.count;
    }
  },
  { field: "time_delivery" },
  { field: "price",
    fn: (data: { price: number; }) => {
      return data.price.toFixed(2);
    }
  },
  // { field: "time_delivery_direction" },
  { slot: "quantity",},
  { slot: "cart" },
  // { slot: "delivery" },
];

const searchDataHead = [
  { value: "Бренд"},
  { value: "Наличие"},
  { value: "Количество"},
  { value: "Срок доставки (дн.)" },
  { value: "Цена ₽" },
  // { value: "Отправка поставщику" },
  { value: "Количество" },
  { value: "" },
  // { value: "" }
];
// const similarSearchDataHead = [
//   { value: "Бренд" },
//   { value: "Поставщик" },
//   { value: "Количество" },
//   { value: "Срок" },
//   { value: "Цена" },
//   { value: "Отправка поставщику" },
//   { value: "" },
//   { value: "" },
// ];
const isSearchFiltersOpen = ref(false);
// const isSimilarShow = ref(false);

// const onFilterClick = () => (isSimilarShow.value = true);

// const similarSearchData = [
//   {
//     brand: "NOKIAN",
//     dealer: "Название поставщика ",
//     count: "4 шт.",
//     deadline: "3 дня",
//     price: "5841 ₽",
//     date: "Сегодня в 14:30",
//   },
//   {
//     brand: "NOKIAN",
//     dealer: "Название поставщика ",
//     count: "4 шт.",
//     deadline: "3 дня",
//     price: "5841 ₽",
//     date: "Сегодня в 14:30",
//   },
// ];

</script>

<template>
  <main class="pb-5 container-fluid">
    <h3 class="large mb-5" style="margin-bottom: 20px!important;">
<!--      <ui-icon-button class="d-xl-none" v-model="isSearchFiltersOpen"-->
<!--        >menu_open</ui-icon-button-->
<!--      >Артикул {{ productId }}-->
      <span :class="$tt('body1')" class="ms-3 hint" style="margin-left: 0px!important;">Найдено предложений: {{ productCount }}</span>
    </h3>
    <div
        class="col-12 col-lg-5"
        style="margin-bottom: 10px"
    >
      <div class="row">
        <div class="col-md-10">
          <SearchFormWithIcon
              :articleProp="productId"
              placeholder="Поиск по артикулу"
              @updateSearchPage="searchDetailInfoByItemNo"
          />
        </div>
        <div class="col-md-2">
          <ui-spinner
              :active="progress"
          ></ui-spinner>
        </div>
      </div>
    </div>
    <div class="row">
      <div>
        <div style="margin-bottom: 40px">
          <ui-table
            fullwidth
            :data="priceInfo"
            :thead="searchDataHead"
            :tbody="searchDataBody"
          >
<!--            <template #brand="{ data }">-->
<!--              <router-link-->
<!--                  :to="{ name: 'product', params: { categoryId: 'wheel', productId: data.price_id}}"-->
<!--              >{{ data.make_name }}</router-link>-->
<!--            </template>-->
            <template #cart="{ data }">
              <ui-icon-button
                :id="data.price_list_id + data.price_id + 'cart'"
                @click="addDetailToCart(data)"
              >
                <ui-icon
                  class="hint"
                  outlined>
                  shopping_cart
                </ui-icon>
              </ui-icon-button>
            </template>
<!--            <template #delivery>-->
<!--              <ui-icon class="hint" outlined > directions_car </ui-icon>-->
<!--            </template>-->
            <template #quantity="{ data }">
              <ui-textfield
                @change="event => changeQuantityPrice(event.target.value, data.price_id, data.price_list_id, data.min_part, data.count)"
                @click="setCursorStart(data.price_id, data.price_list_id)"
                :modelValue="map_carts.get(data.price_id + data.price_list_id)"
                :placeholder="0"
                :min="0"
                :max="data.count"
                inputType="number"
                :id="data.price_id"
                :inputId="data.price_list_id + data.price_id + 'quantity'"
                class="small"
                style="width: 70px; height: 50px; padding: 0 8px"
              ></ui-textfield>
            </template>
          </ui-table>
        </div>
<!--        <div v-if="isSimilarShow">-->
<!--          <h2 style="font-size: 30px" class="large mb-5">-->
<!--            Аналоги других производителей-->
<!--          </h2>-->
<!--          <ui-tab-bar class="mb-4">-->
<!--            <ui-tab min-width content-indicator>По цене</ui-tab>-->
<!--            <ui-tab min-width content-indicator>По производителю</ui-tab>-->
<!--          </ui-tab-bar>-->
<!--          <ui-table-->
<!--            fullwidth-->
<!--            :data="similarSearchData"-->
<!--            :thead="similarSearchDataHead"-->
<!--            :tbody="similarSearchDataBody"-->
<!--          >-->
<!--            <template #cart>-->
<!--              <ui-icon class="hint" outlined> shopping_cart </ui-icon>-->
<!--            </template>-->
<!--            <template #delivery>-->
<!--              <ui-icon class="hint" outlined > directions_car </ui-icon>-->
<!--            </template>-->
<!--          </ui-table>-->
<!--        </div>-->
      </div>

<!--      <div class="d-none d-xl-block offset-1 col-3">-->
<!--        <ProductSearchFilters :onFilterClick="onFilterClick" />-->
<!--      </div>-->
    </div>
  </main>

<!--  <ui-drawer type="modal" v-model="isSearchFiltersOpen">-->
<!--    <ui-drawer-content class="p-4">-->
<!--      &lt;!&ndash; drawer needs at lease one focusable element &ndash;&gt;-->
<!--      <div tabindex="1" />-->
<!--      <ProductSearchFilters :onFilterClick="onFilterClick" />-->
<!--    </ui-drawer-content>-->
<!--  </ui-drawer>-->
  <ui-dialog
      v-model="isShowAddedProduct"
      sheet
      maskClosable
      class="cart-dialog"
  >
    <CartAddDialog
      @hideCartAddDialog="addOrderNotification"
      @errorHideCartAddDialog="errorAddOrderNotification"
      :hide-added-product="hideAddedProduct"
      :data="dataPriceForOrder"
      :quantity="quantityCart"
      :itm_no_cart="itmNoCart"
      :price_cart="priceCart"
      :max_quantity="maxQuantity"
    />
  </ui-dialog>

  <ui-dialog
      @keyup.enter.native="authorisedUserKeyEnter"
      v-model="isLoginOpen"
      :sheet="false"
      :maskClosable="true"
      class="login-dialog"
  >
    <LoginDialog
      v-model:authKeyEnter=authKeyEnterShow
      @isLoginOpen="loginOpen"
      @updatePage="updatePage"
    />
  </ui-dialog>

  <ui-dialog
      v-model="showNotification"
      maskClosable
      sheet
      class="balance-warning-dialog"
  >
    <NotificationDialog
        :type_message="'ВНИМАНИЕ!'"
        :error_detail_message="notificationDesc"
        :hide_error_dialog="hideErrorDialog"
    />
  </ui-dialog>
</template>

<script lang="ts">
import {defineComponent} from "vue";
import SearchService from "@/services/SearchService";
import type ResponseData from "@/types/ResponseData";
import type ArticleDetailData from "@/types/ArticleDetailData";
import type ArticlePriceData from "@/types/ArticlePriceData";
import CartAddDialog from "@/components/Dialogs/CartAddDialog.vue";
import {store} from "@/store";
import {mapGetters} from "vuex";
import LoginDialog from "@/components/Dialogs/LoginDialog.vue";
import NotificationDialog from "@/components/Dialogs/NotificationDialog.vue";
import ProfileDialog from "@/components/Dialogs/ProfileDialog.vue";
import router from "@/router";
import SearchFormWithIcon from "@/components/Search/SearchFormWithIcon.vue";
import {CLEAR_DATA_ORDER_FOR_CART, SET_DATA_ORDER_FOR_CART} from "@/store/actions_type";

export default defineComponent({
  name: "ProductSearch",

  components: {
    LoginDialog: LoginDialog,
    ProfileDialog: ProfileDialog,
    CartAddDialog: CartAddDialog,
    SearchFormWithIcon: SearchFormWithIcon,
    NotificationDialog: NotificationDialog,
  },

  computed: {
    ...mapGetters(["currentStateCart", "isAuthenticated", "currentUser", "currentStateOrderData"])
  },

  watch: {
    isShowAddedProduct() {
      if (!this.isShowAddedProduct) {
        this.dataPriceForOrder = {}
        this.quantityCart = 0
        this.itmNoCart = ""
        this.priceCart = 0
        this.maxQuantity = ""
        store.dispatch(CLEAR_DATA_ORDER_FOR_CART)
      }
    }
  },

  data() {
    return {
      map_carts: new Map<string, number>(),
      priceInfo: [] as ArticlePriceData[],
      productId: '',
      productCount: 0,
      isShowAddedProduct: false,
      dataPriceForOrder: {},
      quantityCart: 0,
      itmNoCart: "",
      priceCart: 0,
      maxQuantity: "",
      isLoginOpen: false,
      showNotification: false,
      notificationDesc: "",
      progress: false,
      authKeyEnterShow: false
    };


  },

  mounted: async function () {
    if (store.getters.isAuthenticated) {
      this.progress = true
      await this.getDetailInfoByItemNo();
      this.progress = false
    } else {
      this.isLoginOpen = true
    }
  },

  methods: {

    authorisedUserKeyEnter() {
      if (this.authKeyEnterShow){
        this.authKeyEnterShow = false
      } else {
        this.authKeyEnterShow = true
      }
    },

    setCursorStart(priceId: number, priceListId: string) {
      if (this.map_carts.get(priceId + priceListId) == 0 ){
        this.map_carts.set(priceId + priceListId, null)
      }
    },

    multiple(quantity: number, minPart: number): number {
      if ((quantity % minPart) != 0){
        let quotient = Math.floor(quantity/minPart) + 1
        return Number(quantity) + (quotient * minPart - quantity)
      }
      return quantity
    },

    async changeQuantityPrice(quantity : number, priceId : number, priceListId: string, minPart: number, availableCount: number) {
      if (quantity <= availableCount) {
        let quantityCurrent = Number(this.map_carts.get(priceId + priceListId))
        if (quantity > quantityCurrent) {
          if ((quantity - quantityCurrent) > minPart) {
            this.map_carts.set(priceId + priceListId, this.multiple(quantity, minPart))
          } else {
            let newQuantity = quantityCurrent + minPart;
            this.map_carts.set(priceId + priceListId, newQuantity)
          }
        } else {
          if (quantity < 0){
            await this.map_carts.set(priceId + priceListId, 1)
            this.map_carts.set(priceId + priceListId, 0)
          } else {
            if (quantity != 0 && (quantityCurrent - quantity) < minPart ) {
              this.map_carts.set(priceId + priceListId, quantityCurrent - minPart)
            } else {
              this.map_carts.set(priceId + priceListId, this.multiple(quantity, minPart))
            }
          }
        }
      } else {
        await this.map_carts.set(priceId + priceListId, 0)
        this.map_carts.set(priceId + priceListId, availableCount)
      }
    },

    hideErrorDialog() {
      this.showNotification = false
      this.notificationDesc = ""
    },

    async serviceGetProductInfo(productId : string) {
      this.priceInfo.length = 0
      await SearchService.prices(productId)
          .then((response: ResponseData) => {
            for (let index = 0, len = response.data.length; index < len; index++) {
              const article_details: ArticleDetailData = {};
              article_details.id = response.data[index].id
              article_details.detail_name = response.data[index].detail_name
              article_details.prices = response.data[index].prices

              for (let index_price = 0, len_price = response.data[index].prices.length; index_price < len_price; index_price++) {
                let price = response.data[index].prices[index_price]
                if (price.count == 0) {
                  price.count = 10000
                }
                this.priceInfo.push(price)
                this.map_carts.set(price.price_id + price.price_list_id, 0)
                this.productCount++
              }
            }
          })
          .catch((e: Error) => {
            console.log(e);
            this.progress = false
          });
    },

    async searchDetailInfoByItemNo(article: string) {
      if (store.getters.isAuthenticated) {
        this.progress = true
        this.priceInfo.length = 0
        this.productCount = 0
        this.productId = article
        await this.serviceGetProductInfo(article)
        this.progress = false
      } else {
        this.isLoginOpen = true
      }
    },

    async getDetailInfoByItemNo() {
      this.priceInfo.length = 0
      this.productCount = 0
      this.productId = this.$route.params.productId
      await this.serviceGetProductInfo(this.productId)
    },

    updatePage() {
      router.go(0)
    },

    async addDetailToCart(data : any) {
      if (store.getters.isAuthenticated) {
        this.isShowAddedProduct = true
        this.dataPriceForOrder = data
        this.quantityCart = Number(this.map_carts.get(data.price_id + data.price_list_id))
        this.itmNoCart = this.productId
        this.priceCart = data.price.toFixed(2)
        this.maxQuantity = data.count == 10000 ? "Под заказ" : String(data.count)
        await store.dispatch(SET_DATA_ORDER_FOR_CART)
      } else {
        this.isLoginOpen = true
      }
      this.notificationDesc = ""
    },

    hideAddedProduct() {
      this.isShowAddedProduct = false
    },

    addOrderNotification() {
      this.notificationDesc = ''
      this.isShowAddedProduct = false
      this.showNotification = true
      this.notificationDesc = "Товар успешно добавлен в корзину"
    },

    errorAddOrderNotification() {
      this.notificationDesc = ''
      this.isShowAddedProduct = false
      this.showNotification = true
      this.notificationDesc = "Произошла ошибка, попробуйте добавить товар в корзну позже."
    },

    loginOpen() {
      this.isLoginOpen = false
    },
  },

});
</script>

<style scoped lang="scss">
.mdc-drawer--modal {
  position: fixed;
  left: 0;
  top: 0;
}
aspect-container  {
  cursor: pointer;
}
</style>
