<template>
  <!-- leave title for close action -->
  <ui-dialog-title>
    <mdc-icon-button
        style="float: right"
        class="mdc-dialog__close"
        data-mdc-dialog-action="close"
    >
      <ui-icon
          style="cursor: pointer;"
      >close</ui-icon>
    </mdc-icon-button>
    <div :class="$tt('body1')" class="large login-dialog__title">
      Личный кабинет
    </div>
  </ui-dialog-title>

  <ui-dialog-content>
    <div class="mt-4">
      <div class="mb-2"><label class="hint" for="login-name">Логин</label></div>
      <ui-textfield
        v-model="user_data_auth.login"
        input-id="login-name"
        outlined
        fullwidth
      />
    </div>
    <div class="mt-3">
      <div class="mb-2">
        <label class="hint" for="login-password">Пароль</label>
      </div>
      <ui-textfield
        v-model="user_data_auth.password"
        input-id="login-password"
        outlined
        fullwidth
        input-type="password"
      />
    </div>

<!--    <div class="row mt-2 align-items-center">-->
<!--      <div class="col-auto">-->
<!--        <ui-form-field>-->
<!--          <ui-checkbox input-id="login-remember" />-->
<!--          <label for="login-remember">Запомнить меня</label>-->
<!--        </ui-form-field>-->
<!--      </div>-->
      <div class="col-auto mt-3 row">
        <RouterLink to="/recover" v-on:click="closeLoginDialog()">
          <div :class="$tt('body1')" class="hint thin">Забыли пароль?</div>
        </RouterLink>
      </div>
<!--    </div>-->

    <ui-button
        id="auth-button"
        v-on:click="authUser" 
        raised
        class="col-12 mt-3"
      >Войти</ui-button
    >
    <div v-if="showErrMessage" class="mb-2; text-center" style="margin-top: 12px">
      <label class="hint" for="error-label" style="color: #F08080">{{ errMessage }}</label>
    </div>

    <div class="row" v-bind:style="style">

      <!-- <div :class="$tt('body1')">Войти через сайт или соцсеть</div>

      <div class="mt-2 row g-2 justify-content-xl-between">
        <div class="col-auto">
          <img src="@/assets/social/vk-color.png" alt="vk" />
        </div>
        <div class="col-auto">
          <img src="@/assets/social/ok-color.png" alt="ok" />
        </div>
        <div class="col-auto">
          <img src="@/assets/social/google-color.png" alt="google" />
        </div>
        <div class="col-auto">
          <img src="@/assets/social/ya-color.png" alt="ya" />
        </div>
        <div class="col-auto">
          <img src="@/assets/social/mail-color.png" alt="mail" />
        </div>
        <div class="col-auto">
          <img src="@/assets/social/facebook-color.png" alt="facebook" />
        </div>
        <div class="col-auto">
          <img src="@/assets/social/insta-color.png" alt="insta" />
        </div>
      </div> -->

      <LineBreak class="my-3" />

      <div class="text-center">
        <RouterLink @click="closeLoginDialog" to="/register">
          <ui-button>ЗАРЕГИСТРИРОВАТЬСЯ</ui-button>
        </RouterLink>
      </div>
    </div>
  </ui-dialog-content>
</template>

<script lang="ts">
import LineBreak from "../LineBreak.vue";
import {defineComponent} from "vue";
import type UserDataAuth from "@/types/UserDataAuth";
import type ResponseData from "@/types/ResponseData";
import {store} from "@/store";
import {AUTH, GET_NUMBER_CONFIRM_ORDERS, USER_ME} from "@/store/actions_type";
import {mapGetters} from "vuex";
import type UserDataInfo from "@/types/UserDataInfo";
import NotificationDialog from "@/components/Dialogs/NotificationDialog.vue";


export default defineComponent({
  name: "LoginDialog",

  props: {
    authKeyEnter: {
      type: Boolean,
      default: false
    }
  },

  watch: {
      authKeyEnter() {
        this.authUser()
      }
  },

  mounted() {
  },

  created: function () {

  },


  data() {

    return {
      user_data_auth: {} as UserDataAuth,
      user_data_info: {} as UserDataInfo,
      showErrMessage: false,
      errMessage: '',
      style: 'margin-top: 32px'
    };
  },

  methods: {

    hideErrorDialog() {
      this.errMessage = ""
      this.showErrMessage = false
    },

    closeLoginDialog() {
      this.$emit('closeDialog')
    },

   authUser() {

     this.showErrMessage = false
     this.errMessage = ""
     this.style='margin-top: 32px'

      this.user_data_auth = {
        login: this.user_data_auth.login,
        password: this.user_data_auth.password
      }

       store.dispatch(AUTH, this.user_data_auth)
          .then((data: ResponseData) => {
            this.$emit("isAuthorisedUser")
            this.$emit('isLoginOpen')
            this.$emit('closeDialog')
            store.dispatch(USER_ME)
                .then((response: ResponseData) => {
                  this.closeLoginDialog()
                })
                .catch((e: Error) => {
                  console.log(e);
                })
            store.dispatch(GET_NUMBER_CONFIRM_ORDERS)
                .then((data: ResponseData) => {
                })
                .catch((e: Error) => {
                  console.log(e);
                });
            this.$emit('updatePage')
          })
          .catch((e: Error) => {
            if (e.data.code == 401) {
              this.showErrMessage = true
              this.errMessage = "Неверный логин или пароль"
              this.style=''
            } else {
              this.showErrMessage = true
              this.errMessage = "Попробуйте позже"
              this.style=''
            }
            console.log(e);
          });
    }
  },
  components: {
    NotificationDialog: NotificationDialog,
    LineBreak: LineBreak
  },

  computed: {
    ...mapGetters(["isAuthenticated"])
  },
});
</script>

<style lang="scss" scoped>
.login-dialog__title {
  text-align: center;
  text-transform: uppercase;
  font-weight: 700;
}
</style>
