<template>
  <!-- <ui-drawer type="modal" v-model="isNavMenuOpen">
    <ui-drawer-header>
      <ui-drawer-title
      ><img
          style="vertical-align: middle"
          alt="Logo"
          src="@/assets/logo-blue.svg"
          width="54"
          height="32"
      /></ui-drawer-title>
    </ui-drawer-header>
    <ui-drawer-content>
      <ui-nav>
        <router-link v-slot="{ href, isActive }" to="/">
          <ui-nav-item :href="href" :active="isActive">Главная</ui-nav-item>
        </router-link>
        <router-link v-slot="{ href, isActive }" to="/catalog">
          <ui-nav-item :href="href" :active="isActive">Каталог</ui-nav-item>
        </router-link>
        <router-link v-slot="{ href, isActive }" to="/about">
          <ui-nav-item :href="href" :active="isActive"
          >О&nbsp;компании</ui-nav-item
          >
        </router-link>
        <router-link v-slot="{ href, isActive }" to="/work">
          <ui-nav-item :href="href" :active="isActive"
          >Сотрудничество</ui-nav-item
          >
        </router-link>
        <router-link v-slot="{ href, isActive }" to="/help">
          <ui-nav-item :href="href" :active="isActive">Помощь</ui-nav-item>
        </router-link>
        <router-link v-slot="{ href, isActive }" to="/news">
          <ui-nav-item :href="href" :active="isActive">Новости</ui-nav-item>
        </router-link>
        <router-link v-slot="{ href, isActive }" to="/contacts">
          <ui-nav-item :href="href" :active="isActive">Контакты</ui-nav-item>
        </router-link>
      </ui-nav>
    </ui-drawer-content>
  </ui-drawer> -->
  <header id="app-header" class="container-fluid">
    <div class="row align-items-center">
      <div class="d-xl-none col-auto">
        <ui-icon-button v-model="isNavMenuOpen">menu</ui-icon-button>
      </div>
      <div class="col-auto">
        <img class="logo" alt="Logo" src="@/assets/logo-blue.svg" />
      </div>
      <nav class="col-auto d-none d-lg-block">
        <div class="row">
          <div class="col-auto">
            <RouterLink to="/">Главная</RouterLink>
          </div>
          <div class="col-auto">
            <RouterLink to="/search-brand">Каталог</RouterLink>
          </div>
          <div class="col-auto">
            <RouterLink to="/about">О&nbsp;компании</RouterLink>
          </div>
          <!-- <div class="col-auto">
            <RouterLink to="/work">Сотрудничество</RouterLink>
          </div> -->
          <!-- <div class="col-auto">
            <RouterLink to="/help">Помощь</RouterLink>
          </div>
          <div class="col-auto">
            <RouterLink to="/news">Новости</RouterLink>
          </div>
          <div class="col-auto">
            <RouterLink to="/contacts">Контакты</RouterLink>
          </div> -->
        </div>
      </nav>
      <div class="ms-auto col-auto">
        <div class="row row-cols-auto g-0">
          <div v-if="$store.getters.isAuthenticated" class="header-item" id="open-cart">
            <RouterLink to="/cart">
              <ui-badge overlap :count="$store.getters.currentStateCart"
              ><ui-icon outlined>shopping_cart</ui-icon></ui-badge
              ><span class="header-item__label">Корзина</span>
            </RouterLink>
          </div>
          <!-- <div class="header-item">
            <RouterLink to="/favourites">
              <ui-icon>star_outline</ui-icon
              ><span class="header-item__label">Избранное</span>
            </RouterLink>
          </div> -->
          <div v-if="isAuthorisedUser" class="header-item">
            <RouterLink to="/confirm/orders">
              <IconUnarchive />
              <span class="header-item__label">Заказы</span>
            </RouterLink>
          </div>
          <div
              v-if="$store.getters.isAuthenticated"
              v-on:click="openProfileDialog"
              class="header-item"
              id="open-profile-dialog"
          >
            <ui-icon>perm_identity</ui-icon
            ><span class="header-item__label">{{ $store.getters.currentUser.login }}</span>
          </div>
          <div 
            v-else
            v-on:click="openLogin" 
            class="header-item" 
            id="open-login"
          >
            <ui-icon>perm_identity</ui-icon
            ><span class="header-item__label">Вход</span>
          </div>
        </div>
      </div>
    </div>
  </header>

  <ui-dialog
      v-model="isProfileDialogOpen"
      sheet
      maskClosable
      class="profile-dialog"
  >
    <ProfileDialog
      :close-profile-dialog="closeProfileDialog"
      :on-notification-click="onNotificationClick"
      :on-logout="logout"
      v-bind:login="profile_user_data_info.login"
      @updatePage="updatePage"
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
      @closeDialog="closeLoginDialog"
      @isAuthorisedUser="authorisedUser"
      @isLoginOpen="loginOpen"
      @updatePage="updatePage"
    />
  </ui-dialog>
  <ui-dialog v-model="isNotificationOpen" sheet scrollable maskClosable>
    <NotificationsDialog v-if="isNotificationOpen" />
  </ui-dialog>
</template>

<script lang="ts">
import LineBreak from "../LineBreak.vue";
import LoginDialog from "../Dialogs/LoginDialog.vue";
import NotificationsDialog from "../Dialogs/NotificationsDialog.vue";
import ProfileDialog from "../Dialogs/ProfileDialog.vue";
import {defineComponent} from "vue";
import {store} from "@/store";
import {CHECK_AUTH, GET_NUMBER_CONFIRM_ORDERS, LOGOUT, USER_ME} from "@/store/actions_type";
import {mapGetters} from "vuex";
import type UserDataInfo from "@/types/UserDataInfo";
import type ResponseData from "@/types/ResponseData";
import router from "@/router";


export default defineComponent({
  name: "page-header",

  components: {
    LineBreak: LineBreak,
    LoginDialog: LoginDialog,
    NotificationsDialog: NotificationsDialog,
    ProfileDialog: ProfileDialog,
  },

  data() {

    return {
      profile_user_data_info: {} as UserDataInfo,
      isLoginOpen: false,
      isAuthorisedUser: false,
      isProfileDialogOpen: false,
      isNotificationOpen: false,
      isNavMenuOpen: false,
      user_data_info: {} as UserDataInfo,
      authKeyEnterShow: false,
    };
  },

  created: function () {
  },

  computed: {
    ...mapGetters(["isAuthenticated", "currentUser", "currentStateCart"])
  },

  methods: {

    loginOpen() {
      this.isLoginOpen = false;
    },
    authorisedUser() {
      this.isAuthorisedUser = true;
    },

    authorisedUserKeyEnter() {
      if (this.authKeyEnterShow){
        this.authKeyEnterShow = false
      } else {
        this.authKeyEnterShow = true
      }
    },

    openLogin() {
      this.isLoginOpen = true;
    },
    onNotificationClick() {
      this.isNotificationOpen = true;
      this.closeProfileDialog();
    },
    closeLoginDialog() {
      this.isLoginOpen = false;
    },
    logout() {
      store.dispatch(LOGOUT);
      this.isAuthorisedUser = false;
      this.isProfileDialogOpen = false;
      this.updatePage()
    },
    openProfileDialog() {
      this.profile_user_data_info = store.getters.currentUser;
      this.isProfileDialogOpen = true;
    },
    closeProfileDialog() {
      this.isProfileDialogOpen = false;
    },
    updatePage() {
      router.go(0)
    },
  },
});
</script>

<style lang="scss" scoped>
@use "@/styles/vars";

header#app-header {
  background-color: black;
  max-height: 100px;
  padding-top: 18px;
  padding-bottom: 18px;

  &,
  & a,
  & .header-item {
    color: white;
  }

  @media (max-width: vars.$desktop) {
    max-height: 70px;
    padding-top: 9px;
    padding-bottom: 9px;
  }

  .logo {
    height: 26px;
    width: auto;

    @media (min-width: vars.$desktop) {
      height: 64px;
    }
  }

  .header-item {
    margin-left: 0.6rem;
    margin-right: 0.6rem;
    padding-bottom: 6px;

    cursor: pointer;

    * {
      vertical-align: middle;
    }

    &__label {
      display: none;

      @media (min-width: 1500px) {
        display: inline;
      }
    }
  }
  .header-item:first-child {
    margin-left: 0;
  }
  .header-item:last-child {
    margin-right: 0;
  }
  .header-item span {
    margin-left: 10px;
  }
}
</style>

<style lang="scss">
@use "@/styles/vars";

.login-dialog {
  .mdc-dialog__surface {
    @media (min-width: vars.$desktop) {
      max-width: 476px !important;
    }
  }
  .mdc-dialog__content {
    padding-left: 30px !important;
    padding-right: 30px !important;
  }
}

.profile-dialog {
  .mdc-dialog__content,
  .mdc-dialog__title {
    padding-left: 24px !important;
    padding-right: 24px !important;
  }
  .mdc-dialog__container {
    @media (min-width: vars.$desktop) {
      margin-left: auto;
      margin-right: 1.5rem;

      position: relative;

      .mdc-dialog__surface {
        position: absolute;
        top: 75px;
        right: 0;
        width: 310px;
      }
    }
  }
}
</style>