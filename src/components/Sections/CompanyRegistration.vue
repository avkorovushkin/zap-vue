<script lang="ts" setup>
import IconQuestionBlue from "@/components/icons/iconQuestionBlue.vue";
</script>

<template>
  <div class="large bold mb-5">Данные организации</div>

  <div class="row gy-3 organisation-signup">
    <div class="col-12 col-md-6 col-xl-4">
      <div class="mb-3">
        <label for="reg-inn" class="mb-2"
        >ИНН
          <IconQuestionBlue id="reg-inn-icon" data-tooltip-id="tlp-1" />
          <ui-tooltip-anchor>
            <ui-tooltip id="tlp-1">
              При заполнении ИНН остальные данные добавятся из базы ФНС
              автоматически
            </ui-tooltip>
          </ui-tooltip-anchor>
        </label>
        <ui-textfield id="reg-inn" v-model="company_data.inn" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-companyName" class="mb-2">Наименование</label>
        <ui-textfield id="reg-companyName" v-model="company_data.companyName" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-legalAddress" class="mb-2">Юридический адрес</label>
        <ui-textfield id="reg-legalAddress" v-model="company_data.legalAddress" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-actualAddress" class="mb-2">Фактический адрес</label>
        <ui-textfield id="reg-actualAddress" v-model="company_data.actualAddress" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-bankName" class="mb-2">Наименование банка</label>
        <ui-textfield id="reg-bankName" v-model="company_data.bankName" outlined fullwidth />
      </div>
    </div>
    <div class="col-12 col-md-6 col-xl-4">
      <div class="mb-3">
        <label for="reg-kpp" class="mb-2">КПП</label>
        <ui-textfield id="kpp" v-model="company_data.kpp" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-bankAccount" class="mb-2">Р/С</label>
        <ui-textfield id="reg-bankAccount" v-model="company_data.bankAccount" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-corrAccount" class="mb-2">К/С</label>
        <ui-textfield id="reg-corrAccount" v-model="company_data.bankCorrAccount" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-bik" class="mb-2">БИК</label>
        <ui-textfield id="reg-bik" v-model="company_data.bankBik" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-ogrn" class="mb-2">ОГРН</label>
        <ui-textfield id="reg-ogrn" v-model="company_data.ogrn" outlined fullwidth />
      </div>
    </div>
    <div class="col-12 col-md-6 col-xl-4">
      <div class="mb-3">
        <label for="reg-okpo" class="mb-2">ОКПО</label>
        <ui-textfield id="reg-okpo" v-model="company_data.okpo" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-contactPerson" class="mb-2">Контактное лицо</label>
        <ui-textfield id="reg-contactPerson" v-model="company_data.contactPerson" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-email" class="mb-2">E-mail</label>
        <ui-textfield id="reg-email" v-model="company_data.contactEmail" outlined fullwidth />
      </div>
      <div class="mb-3">
        <label for="reg-contactPhone" class="mb-2">Телефон</label>
        <ui-textfield id="reg-contactPhone" v-model="company_data.contactPhone" outlined fullwidth />
      </div>
        <ui-button fullwidth class="mt-4" style="margin-top: 28px!important;" raised v-on:click="registrationCompany()">Зарегистрироваться</ui-button>
    </div>
  </div>
  <ui-dialog
      v-model="showErrMessage"
      maskClosable
      sheet
      class="balance-warning-dialog"
  >
    <NotificationDialog
      :type_message="typeMessage"
      :error_detail_message="errMessage"
      :hide_error_dialog="hideErrorDialog"
    />
  </ui-dialog>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import type CompanyRegistrationData from "@/types/CompanyRegistrationData";
import CustomSelect from "../../components/CustomSelect.vue";
import BalanceBar from "../../components/Profile/BalanceBar.vue";
import LineBreak from "@/components/LineBreak.vue";
import NotificationDialog from "@/components/Dialogs/NotificationDialog.vue";
import UserDataService from "@/services/UserDataService";
import type ResponseData from "@/types/ResponseData";
import router from "@/router";

export default defineComponent({
  name: "CompanyRegistration",

  components: {
    NotificationDialog: NotificationDialog,
    CustomSelect: CustomSelect,
    BalanceBar: BalanceBar,
    LineBreak: LineBreak,
  },

  data() {
    return {
      company_data: {
        inn: "",
        companyName: "",
        legalAddress: "",
        actualAddress: "",
        bankName: "",
        kpp: "",
        bankAccount: "",
        bankCorrAccount: "",
        bankBik: "",
        ogrn: "",
        okpo: "",
        contactPerson: "",
        contactEmail: "",
        contactPhone: "",
        } as CompanyRegistrationData,
      errMessage: "",
      showErrMessage: false,
      typeMessage: ''
    };
  },

  created: function () {

  },

  methods: {

    hideErrorDialog() {
      this.errMessage = ""
      this.showErrMessage = false
      if (this.typeMessage == "УВЕДОМЛЕНИЕ!"){
        this.typeMessage = ""
        router.push({path: "/"});
      }
    },

    registrationCompany() {
      this.showErrMessage = false;
      this.typeMessage =''

      if (this.company_data.inn == '') {
        this.typeMessage = "ВНИМАНИЕ!"
        this.errMessage = ('Поле "ИНН" обязательно к заполнению');
        this.showErrMessage = true;
        return;
      }
      if (this.company_data.companyName == '') {
        this.typeMessage = "ВНИМАНИЕ!"
        this.errMessage = ('Поле "Наименовании" обязательно к заполнению');
        this.showErrMessage = true;
        return;
      }
      if (this.company_data.contactEmail == '') {
        this.typeMessage = "ВНИМАНИЕ!"
        this.errMessage = ('Поле "E-mail" обязательно к заполнению');
        this.showErrMessage = true;
        return;
      }
      if (this.company_data.contactPhone == '') {
        this.typeMessage = "ВНИМАНИЕ!"
        this.errMessage = ('Поле "Телефон" обязательно к заполнению');
        this.showErrMessage = true;
        return;
      }

      let company_data_reg: any
      company_data_reg = {
        inn: this.company_data.inn,
        companyName: this.company_data.companyName,
        legalAddress: this.company_data.legalAddress,
        actualAddress: this.company_data.actualAddress,
        bankName: this.company_data.bankName,
        kpp: this.company_data.kpp,
        bankAccount: this.company_data.bankAccount,
        bankCorrAccount: this.company_data.bankCorrAccount,
        bankBik: this.company_data.bankBik,
        ogrn: this.company_data.ogrn,
        okpo: this.company_data.okpo,
        contactPerson: this.company_data.contactPerson,
        contactEmail: this.company_data.contactEmail,
        contactPhone: this.company_data.contactPhone
      }

      if (this.company_data.inn != '' &&
          this.company_data.companyName != '' &&
          this.company_data.contactEmail != '' &&
          this.company_data.contactPhone!= '') {

        UserDataService.registrationCompany(company_data_reg)

          .then((response: ResponseData) => {
            if (response.data.code != 409) {
              this.typeMessage = ""
              this.showErrMessage = true;
              this.errMessage = "Для вас создан пользователь " + response.data.login +
                  "." +
                  " \nС вами свяжется менеджер ZAP. "
            } else {
              this.typeMessage = "ВНИМАНИЕ!"
              this.showErrMessage = true;
              this.errMessage = ('Пользователь с таким ИНН уже сущесвует');
            }
          })
          .catch((e: Error) => {
            this.typeMessage = "ВНИМАНИЕ!"
            this.showErrMessage = true;
            this.errMessage = ('Попробуйте пройти регистрацию позже');
          });

      }
    },
  },
});
</script>

<style lang="scss" scoped>
.organisation-signup label {
  display: inline-block;
  position: relative;
}
#reg-inn-icon {
  position: absolute;
  top: -3px;
  right: -30px;
}
</style>
