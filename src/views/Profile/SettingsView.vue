<template>
  <main class="container-fluid pb-5">
    <BalanceBar class="ms-auto mt-2 mb-3" />
    <h1 class="mb-4 mt-5 col-auto large">Настройки профиля</h1>

    <div class="col-12 col-xl-8">
      <ui-tab-bar v-model="selectedTab">
        <ui-tab min-width content-indicator>Личные данные</ui-tab>
        <!-- <ui-tab min-width content-indicator>Ваши реквизиты</ui-tab> -->
        <!-- <ui-tab min-width content-indicator>Доставка</ui-tab> -->
        <ui-tab min-width content-indicator>Изменить пароль</ui-tab>
        <!-- <ui-tab min-width content-indicator>Уведомления</ui-tab> -->
      </ui-tab-bar>
    </div>

    <div class="mt-1">
      <!-- Personal data -->
      <section v-if="selectedTab === 0">
        <ProfileInfo />
      </section>
      <!-- Payment details -->
      <!-- <section v-if="selectedTab === 1" class="col-12 col-md-10 col-xl-6"> -->
        <!-- <ProfilePaymentDetails /> -->
      <!-- </section> -->
      <!-- Delivery details -->
      <!-- <section v-if="selectedTab === 2"> -->
        <!-- <ProfileDeliveryDetails /> -->
      <!-- </section> -->
      <!-- Password change -->
      <section v-if="selectedTab === 1">
        <ProfilePasswordChange />
      </section>
      <!-- Password change -->
    </div>
  </main>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import CustomSelect from "@/components/CustomSelect.vue";
import BalanceBar from "@/components/Profile/BalanceBar.vue";
import LineBreak from "@/components/LineBreak.vue";
import ProfilePasswordChange from "@/components/Sections/ProfilePasswordChange.vue";
import ProfileInfo from "@/components/Sections/ProfileInfo.vue";

export default defineComponent({
  name: "SettingsView",

  components: {
    CustomSelect: CustomSelect,
    BalanceBar: BalanceBar,
    LineBreak: LineBreak,
    ProfilePasswordChange: ProfilePasswordChange,
    ProfileInfo: ProfileInfo,
  },

  data() {
    return {
      selectedTab: 0,
    };
  },
});
</script>
