<template>
  <header class="header" id="header">
    <nav class="inner">
       <div @click="resetApp" class="site-name site-logo">
        <img class="logo-img" src="../../assets/sedaily-logo.png">
          Software Daily
       </div>
      <span class="pull-right">
        <search-bar />
        <span v-if="!isLoggedIn" class="register">
          <button @click="signIn" name="submit-button" class="btn-sign-in">SIGN IN</button>
        </span>
        <router-link v-if="!alreadySubscribed" to="/premium" class="button-submit call-to-action-secondary">Subscribe</router-link>
        <router-link v-else to="/subscribe" class="subscribed">Subscribed</router-link>
        <span class="active-without-border" v-if="isLoggedIn">
          <div>
            <b-dropdown variant="link" size="lg" no-caret>
              <template slot="button-content">
                <div class="crop-image" v-if="isLoggedIn">
                  <img class="profile-img" :src="errorImg || avatarUrl" @error="imgOnError">
                </div>
              </template>
              <b-dropdown-item to="/profile">
                <div class="arrow"></div>
                Profile
              </b-dropdown-item>
              <b-dropdown-item to="/settings">
                Settings
              </b-dropdown-item>
              <b-dropdown-item @click.prevent="logoutHandler">
                Logout
              </b-dropdown-item>
            </b-dropdown>
          </div>
        </span>

      </span>
    </nav>
    <NavMobile/>
  </header>
</template>

<script>
import { Dropdown } from 'bootstrap-vue/esm/components/dropdown'
import { mapGetters, mapState } from "vuex";
import SearchBar from "@/components/search/SearchBar";
import NavMobile from "./NavBarMobile"

export default {
  name: "navigation-bar",
  components: {
    SearchBar,
    NavMobile
  },
  props: {
    userData: {
      type: Object,
      default: function() {
        return {
          avatarUrl: ""
        };
      }
    },
    ownProfile: {
      type: Boolean,
      default: false
    }
  },
  watch: {
    searchTerm() {
      this.makeSearch();
    }
  },
  data: () => ({
    showDropdown: false,
    clickedDropdown: false,
    searchTerm: null,
    showFilteringElements: true,
    searchActive: false,
    errorImg: ''

  }),
  computed: {
    ...mapGetters(["isLoggedIn"]),
    ...mapState({
      alreadySubscribed(state) {
        if (!this.isLoggedIn) return false;
        if (state.me && state.me.subscription && state.me.subscription.active) {
          return true;
        } else {
          return false;
        }
      },
      avatarUrl(state) {
          return state.me.avatarUrl || state.placeholderAvatar;
        }
    })
  },
  methods: {
    signIn() {
      this.$router.push({ path: `/login` })
    },
    logoutHandler() {
      this.$auth.logout()
      this.$router.replace('/')
    },
    mouseOverDropdown() {
      if (!this.showDropdown) this.showDropdown = true;
    },
    mouseLeaveDropdown() {
      if (this.showDropdown && !this.clickedDropdown) this.showDropdown = false;
    },
    onClickPodcastButton() {
      this.clickedDropdown = !this.clickedDropdown;
      this.clickedDropdown
        ? (this.showDropdown = true)
        : (this.showDropdown = false);
    },
    onClickDropdownMenu() {
      this.clickedDropdown = false;
      this.showDropdown = false;
    },
    onSearchActive() {
      !this.searchActive
        ? (this.searchActive = true)
        : (this.searchActive = false);
    },
    resetApp() {
      this.$store.commit('setSearchTerm', { searchTerm: null })
      this.$router.push({ path: `/` })
      document.location.reload(true)
    },
    imgOnError() {
        this.errorImg = 'https://s3-us-west-2.amazonaws.com/sd-profile-pictures/profile-icon-9.png'
      }
  }
}

$(function() {
  var lastScrollTop = 0,
    delta = 5,
    last = "up",
    foo = 99999999,
    state = "fixed",
    lastpos;
  document.addEventListener('touchmove', function (event) {
  if (event.scale !== 1) { event.preventDefault(); }
  }, false);
  $(window).scroll(function(event) {
    var st = $(this).scrollTop();
    // if(Math.abs(lastScrollTop - st) <= delta) return;
    if (st > lastScrollTop) {
      // scrolling down
      if (last == "up") {
        if (state == "fixed") {
          lastpos = document.documentElement.scrollTop - 1;
          $("#header").css({ position: "absolute", top: "0" });
          state = "absolute";
        }
        last = "down";
      }
    } else {
      // scrolling up
      let posnow = document.body.scrollTop || document.documentElement.scrollTop;
      if (posnow - lastpos > 50) {
        if (last == "down") {
          foo = posnow - 51;
          $("#header").css({ position: "absolute", top: "0" });
        } else {
          if (foo > st) {
            $("#header").css({ position: "fixed", top: "0"});
            state = "fixed";
          }
        }
      }
      last = "up";
    }
    lastScrollTop = st;
  });
});
</script>

<style lang="stylus">
@import '../../css/variables'
.header
  .dropdown-menu .dropdown-item
    border-bottom none
    text-transform capitalize
    width 100%
    padding 8px 10px
    &:hover
      color primary-color
    &:active,
    &.router-link-active
      font-weight bold
      color #fff
      background-color primary-color
      border-color white
    &.router-link-active
      pointer-events none
</style>

<style scoped lang="stylus">
@import '../../css/variables'
.btn-sign-in
  font-size 14px
  font-weight 700
  line-height 16px
  border 0
  letter-spacing 1.05px
  padding 10px 20px
.button-submit
  margin-left 15px
  font-weight 700
.site-logo
  margin-right 0.75em
  cursor pointer
.btn-secondary
  font-size 14px
  margin-top 8px
  box-shadow none
  background-color white
  color black
  &:hover
    border-color white
    color white
    background-color primary-color
  &:focus
    box-shadow none
.show
  .btn-secondary
    background-color white
    &.dropdown-toggle
      border-color white
      color white
      background-color primary-color
.dropdown-menu-activez
  .btn-secondary
    background-color white
    box-shadow 0 0 0 0.2rem rgba(108, 117, 125, 0.5)
    &.dropdown-toggle
      border-color white
      color white
      background-color primary-color
.dropdown-menu
  display block
  transform translate3d(-91px, 52px, 0px) !important
  .dropdown-menu
    opacity 0
    position absolute
    top 0px
    left 0px
    will-change transform
.dropdown-enter-active
  animation dropdown-in 0.2s
.dropdown-leave-active
  animation dropdown-in 0.1s reverse
@keyframes dropdown
  0%
    display none
  100%
    display block
.forum-nav-link
  margin-right 15px
  margin-left 15px
.feed-nav-link
  margin-right 15px
.header
  z-index 999
  top 0
  left 0
  right 0
  position absolute
  background-color white
  .register
    display flex
    align-items center
    margin-left 5px
  .logo-img
    max-height 30px
    margin-right 10px
  .profile-img
    width 35px
    height 35px
    border-radius 50%
  .search-img
    max-height 35px
    width 35px
  .inner
    text-transform uppercase
    max-width 1050px
    box-sizing border-box
    margin 0px auto
    padding 15px 15px
    display flex
    align-items center
    justify-content space-between
    .pull-right
      display flex
      align-items center
  .inner-mobile
    text-transform uppercase
    max-width 1200px
    box-sizing border-box
    margin 0px auto
    padding 15px 5px
    display flex
    align-items center
    justify-content space-between
    .pull-right
      display flex
      align-items center
      justify-content space-between
      width 100%
  .active-without-border
    a.router-link-active
      text-decoration none
      border none
      line-height 25px
  a
    font-size 14px
    line-height 16px
    transition color .15s ease
    color black
    display inline-block
    vertical-align middle
    letter-spacing .075em
    margin-right 0.75em
    &:hover
      color #666
      text-decoration none
    &.router-link-active
      text-decoration none
      border-bottom 1.5px solid rgba(primary-color,1.0)
      line-height 25px
    &:nth-child(2)
      margin-right 0
  .dropdown-menu .dropdown-item
    border-bottom none
    text-transform capitalize
    width 100%
    padding 8px 10px
    &:hover
      color primary-color
    &:active
      font-weight bold
      color #fff
      border-color white
      padding-top 2px
      color primary-color
      background-color primary-color
  .github
    color #fff
    font-size .9em
    margin 0
    float right
  .site-name
    text-transform uppercase
    font-size 20px
    color #000
    line-height 25px
    letter-spacing normal
    font-weight bold
    display flex
    align-items center
    &:hover
      text-decoration none
      color #000
    &.router-link-active
      text-decoration none
      border-bottom none
  .call-to-action
    text-decoration none
    box-shadow 0 0px 0px rgba(#000, 0.3)
    line-height 25px
    color white
    background-color primary-color
    border-radius 4px
    padding-top 4px
    text-decoration none
    transition all .5s ease
    margin-right 1em
    &.router-link-active
      background #dcdcdd
      color #000
      border-bottom 0px solid rgba(0,0,0,0.5)
    &:hover
      color #fff
      background-color #a591ff
      box-shadow 0 5px 15px rgba(#000, 0.3)
  .call-to-action-secondary
    padding 10px 30px
    border-radius 2px
    color #fff !important
  .register-nav-link
    margin-right 1em
  .arrow
    &:before
      content: '';
      width: 10px;
      height: 10px;
      border-top: 0 solid #fff;
      border-left: 0 solid #fff;
      border-bottom: 0 solid #fff;
      border-right: 0 solid #fff;
      position: absolute;
      right: 18%;
      top: -5px;
      margin-left: -6px;
      -webkit-transform: rotate(45deg);
      transform: rotate(45deg);
      background-color: #fff;
      -webkit-box-shadow: -1px -1px 0 0 rgba(0,0,0,0.1);
      box-shadow: -1px -1px 0 0 rgba(0,0,0,0.1);

.active-without-border >>> .dropdown-menu {
        transform translate3d(-91px, 52px, 0px) !important
}

@media (max-width 659px)
  .inner
    display none!important
  .inner-mobile
    .search-bar
      justify-content center
      margin 15px auto!important
      margin 15px
      margin-left 0
      width 90%
@media (min-width 660px)
  .inner-mobile
    display none!important


</style>
