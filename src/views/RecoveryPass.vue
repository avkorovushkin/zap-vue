<template>
  <main class="container-fluid pb-5">
    <div class="row">
      <h1 class="col-auto my-5 large">Восстановление пароля</h1>
      <div class="col-auto ms-auto">
        <BalanceBar class="mt-2 mb-3" />
      </div>
    </div>
    <div class="col-10" style="margin-bottom: 20px">
      <label for="select-all">Введите адрес электронной почты, который был указан при регистрации в ZAP.</label>
    </div>
    <div class="col-4" style="margin-bottom: 20px">
      <ui-textfield
          input-type="email"
          fullwidth
          :required="true"
          v-model="email"
          >
        Email Address
      </ui-textfield>
    </div>
    <div class="row" style="margin-top: 40px!important;">
      <div class="col-md-3">
        <ui-button :disabled="disableButton" v-on:click="recoveryPassAccount" raised>Восстановить пароль</ui-button>
      </div>
      <div class="col-md-1">
        <ui-spinner
            :active="progressSpinner"
        ></ui-spinner>
      </div>
    </div>
  </main>
  <ui-dialog
      v-model="showNotification"
      maskClosable
      sheet
      class="balance-warning-dialog"
  >
    <NotificationDialog
        :type_message="typeNotification"
        :error_detail_message="notificationMessage"
        :hide_error_dialog="hideErrorDialog"
    />
  </ui-dialog>
</template>

<script lang="ts">
import {defineComponent} from "vue";
import {mapGetters} from "vuex";
import NotificationDialog from "@/components/Dialogs/NotificationDialog.vue";
import LineBreak from "@/components/LineBreak.vue";
import AccountService from "@/services/AccountService";
import router from "@/router";


export default defineComponent({
  name: "RecoveryPass",

  created: function () {
  },

  data() {

    return {
      disableButton: true,
      progressSpinner: false,
      email: '',
      showNotification: false,
      notificationMessage: "",
      typeNotification: ""
    };
  },

  watch: {
    email () {
      if (this.validateEmail(this.email) != null) {
        this.disableButton = false
      } else {
        this.disableButton = true
      }

    }
  },

  methods: {
    validateEmail(email: string) {
      return email.match(
          /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
      );
    },

    async recoveryPassAccount() {
      this.progressSpinner = true
      await AccountService.accountPassRecovery(this.email)
          .then((response: any) => {
            this.typeNotification = "УВЕДОМЛЕНИЕ"
            this.notificationMessage = "На ваш электронный адрес " + this.email + " отправлена ссылка для восстановления пароля."
            this.showNotification = true
          })
          .catch((e: Error) => {
            if (e.response.status == 404) {
              this.typeNotification = "ОШИБКА"
              this.notificationMessage = "Аккаунта с данным электронным адресом не найдено."
              this.showNotification = true
            } else {
              this.typeNotification = "ОШИБКА"
              this.notificationMessage = "Попробуйте восстановить пароль позже."
              this.showNotification = true
            }
            console.log(e);
          });
      this.progressSpinner = false
    },

    hideErrorDialog() {
      this.typeNotification = ""
      this.notificationMessage = ""
      this.showNotification = false
    },
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

<style scoped>
div#pass-contents {
  min-height: 60vh;
}
</style>
