<!--<script setup lang="ts">-->
<!--defineProps<{-->
<!--  hideAddedProduct: Function;-->
<!--  detail_name: string;-->
<!--  itm_no: string;-->
<!--  make_name: string;-->
<!--  quantity: number;-->
<!--  price: number;-->
<!--  min_part: number;-->
<!--  max_quantity: number;-->
<!--}>();-->
<!--</script>-->

<template>
  <!-- leave title for close action -->
  <ui-dialog-title>
    <div :class="$tt('body2')" class="cart-dialog__title large x-bold mb-3">
      Добавление в корзину
    </div>
  </ui-dialog-title>
  <ui-dialog-content
    style="padding-left: 30px; padding-right: 30px; padding-bottom: 30px"
  >
    <div class="row cart-dialog__content">
<!--      <div class="col-auto">-->
<!--        <img-->
<!--          style="border: 1px solid #d9d9de"-->
<!--          src="@/assets/catalog/wheel.png"-->
<!--          alt=""-->
<!--          width="104"-->
<!--        />-->
<!--      </div>-->
      <div class="col">
        <div :class="$tt('body1')" class="bold mb-2">
          {{  }}
        </div>
        <div :class="$tt('body1')" class="small">
          <span v-if="data.detail_name !== '' && data.detail_name !== null" class="hint">Название детали:</span> {{ data.detail_name }} <br />
          <span class="hint">Бренд:</span> {{ data.make_name }} <br />
          <span class="hint">Артикул:</span> {{ itm_no_cart }} <br />
          <span class="hint">Цена за ед.:</span> {{ price_cart }} ₽ <br />
          <span class="hint">В наличии ед.:</span> {{ max_quantity }}
        </div>
        <div class="row align-items-end mt-3">
<!--          <div class="col-auto" :class="$tt('body1')">В корзине {{ quantity }} шт</div>-->
          <div class="row col-auto" :class="$tt('body1')">
            <ui-textfield
                @change="event => changeQuantityPrice(event.target.value)"
                @click="setCursorStart()"
                :modelValue="quantityCart"
                :placeholder="0"
                :min="0"
                :max="data.count"
                inputType="number"
                class="small"
                style="width: 70px; height: 50px; padding-left: 0px!important; margin-left: 10px"
            ></ui-textfield>
            <div class="col-auto" style="display: flex; align-items: flex-end;">
              шт
            </div>
            <div class="col-md-2">
              <ui-spinner
                  :active="progress"
              ></ui-spinner>
            </div>
          </div>

          <div class="col-auto ms-auto" :class="$tt('headline3')">{{ totalPrice }} ₽</div>
        </div>
      </div>
    </div>

    <div class="col-auto ms-auto" style="margin-top: 40px;">
      <div style="margin-bottom: 5px" >
        <span :class="$tt('body1')" class="ms-3 hint" style="margin-left: 0!important;">
          Оставить комментарий к заказу
        </span>
      </div>
      <ui-textfield
        outlined
        fullwidth
        v-model="comment"
        input-type="textarea"
        rows="8" cols="40"
      >Комментарий
      </ui-textfield>
    </div>
    <div class="col-12 col-md-12" style="margin-top: 10px">
      <ui-button id="add-to-cart"
          :disabled="!buttonAddCartEnable"
          style="color: #0069c8"
          class="w-100"
          outlined
          @click="addDetailToCart"
      >Добавить в корзину</ui-button
      >
    </div>
    <div class="row g-3 mt-4">
      <div class="col-12 col-md-6">
        <ui-button
            style="color: #0069c8"
            class="w-100"
            outlined
            @click="hideAddedProduct"
        >Продолжить&nbsp;покупки</ui-button
        >
      </div>
      <div class="col-12 col-md-6">
        <RouterLink to="/cart">
          <ui-button id="go-to-cart" class="w-100" raised
          >Перейти&nbsp;в&nbsp;корзину</ui-button
          >
        </RouterLink>
      </div>
    </div>
  </ui-dialog-content>
</template>

<script lang="ts">
import {defineComponent} from "vue";
import {mapGetters} from "vuex";
import {store} from "@/store";
import OrderService from "@/services/OrderService";
import type ResponseData from "@/types/ResponseData";
import {GET_NUMBER_CONFIRM_ORDERS} from "@/store/actions_type";


export default defineComponent({
  name: "CartAddDialog",

  created: function () {

  },

  watch: {
    'myState': function() {
      if (!this.myState) {
        this.comment = ''
        this.quantityCart = 0
        this.totalPrice = 0.00
        this.buttonAddCartEnable = false
      }
    },
    'quantity': function(value) {
      this.quantityCart = value;
      this.totalPrice = (value * Number(this.$props.price_cart)).toFixed(2)
      this.comment = ''
    },
    'quantityCart': function () {
      this.quantityCart == 0 || this.quantityCart == null ? this.buttonAddCartEnable = false : this.buttonAddCartEnable = true
    }
  },

  data() {

    return {
      quantityCart: 0,
      totalPrice: 0.00,
      comment: "",
      progress: false,
      buttonAddCartEnable: false
    };
  },

  props: {
    hideAddedProduct: Function,
    data: {
    },
    quantity: {
      type: Number
    },
    itm_no_cart: {
      type: String
    },
    price_cart: {
      type: Number
    },
    max_quantity: {
      type: String
    },
  },

  computed: {
    ...mapGetters({myState: "currentStateOrderData"})
  },

  methods: {

    async addDetailToCart() {

        let quantityItem: number
        quantityItem = this.quantityCart

        if (quantityItem > 0) {
            this.progress = true
            await OrderService.addDetailToCart(
                this.$props.data.price_id,
                this.quantityCart,
                this.$props.data.price_list_id,
                this.$props.price_cart,
                this.comment,
                this.$props.data.time_delivery,
            )
                .then((response: ResponseData) => {
                  store.dispatch(GET_NUMBER_CONFIRM_ORDERS)
                  this.$emit('hideCartAddDialog')
                })
                .catch((e: Error) => {
                  this.$emit('errorHideCartAddDialog')
                  console.log(e);
                })
            this.progress = false
        }

        this.comment = ''
        this.quantityCart = 0
        this.totalPrice = 0.00
        this.buttonAddCartEnable = false
    },

    multiple(quantity: number, minPart: number): number {

      if ((quantity % minPart) != 0){
        let quotient = Math.floor(quantity/minPart) + 1
        return Number(quantity) + (quotient * minPart - quantity)
      }
      return Number(quantity)
    },

    async changeQuantityPrice(quantity: number) {
      let maxQuantityAvailable = this.$props.data.count
      let minPart = this.$props.data.min_part

      if (quantity != 0) {
        if (quantity < maxQuantityAvailable) {
          if (quantity > this.quantityCart) {
            this.quantityCart = this.multiple(quantity, minPart)
          } else {
            if (this.quantityCart - quantity > minPart){
              this.quantityCart = this.multiple(quantity, minPart)
            } else {
              this.quantityCart = this.quantityCart - minPart
            }
          }
        } else {
          await this.wait()
          this.quantityCart = maxQuantityAvailable
        }
      }
      else {
        this.quantityCart = 0
      }
      this.totalPrice = (Number(this.$props.price_cart) * this.quantityCart).toFixed(2)
    },

    async setCursorStart() {
      if (this.quantityCart == 0 || this.quantityCart == null) {
        await this.wait()
        this.quantityCart = null
      }
    },

    async wait() {
      this.quantityCart = 0
    }
  },

});
</script>