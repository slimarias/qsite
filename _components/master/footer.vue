<template>
  <div id="masterFooter">
    <!-- === FOOTER === -->
    <q-footer class="shadow-5" v-if="appConfig.mode == 'iadmin' ? true : false">
      <div id="footerContent" class="row q-md-hide items-center">
        <!-- Menu -->
        <div class="item-footer col cursor-pointer" @click="$eventBus.$emit('toggleMasterDrawer','menu')">
          <q-icon class="item-icon" name="fas fa-bars"/>
          <div>Menu</div>
        </div>
        <!-- Profile -->
        <div class="item-footer col cursor-pointer">
          <q-btn :to="{name: 'user.profile.me'}" flat no-caps v-if="quserState.authenticated"
                 class="item-icon" padding="none">
            <q-avatar size="20px">
              <img :src="quserState.userData.mainImage">
            </q-avatar>
          </q-btn>
          <div>{{ $tr('ui.label.profile') }}</div>
        </div>
        <!-- Main -->
        <div class="col" @click="doMainAction()">
          <div id="item-main" :class="`cursor-pointer row items-center justify-center bg-${mainAction.color}`">
            <q-icon class="item-icon" :name="mainAction.icon"/>
          </div>
        </div>
        <!-- Settings -->
        <div class="item-footer col cursor-pointer" @click="$eventBus.$emit('toggleMasterDrawer','config')">
          <q-icon class="item-icon" name="fas fa-cog"/>
          <div>{{ $tr('ui.label.setting') }}</div>
        </div>
        <!-- Others -->
        <div class="item-footer col cursor-pointer" @click="modal.show = true">
          <q-icon class="item-icon" name="fas fa-ellipsis-h"/>
          <div>{{ $trp('ui.label.other') }}</div>
        </div>
      </div>
    </q-footer>
    <!--Dialog other actions-->
    <q-dialog v-model="modal.show" position="bottom" v-if="appConfig.mode == 'iadmin' ? true : false">
      <q-card style="width: 350px">
        <!--Toolbar-->
        <q-toolbar>
          <q-icon class="item-icon" name="fas fa-ellipsis-v"/>
          <q-toolbar-title> {{ $trp('ui.label.other') }}</q-toolbar-title>
          <!--Close bottom-->
          <q-btn flat round dense icon="close" v-close-popup/>
        </q-toolbar>

        <!--Separator-->
        <q-separator/>

        <q-card-section class="row items-center no-wrap q-pa-none">
          <q-list separator class="full-width" v-close-popup>
            <!--Go to site action-->
            <q-item clickable v-ripple @click.native="$helper.openExternalURL($store.state.qsiteApp.baseUrl)">
              <q-item-section avatar>
                <q-icon color="primary" name="far fa-eye"/>
              </q-item-section>
              <q-item-section class="ellipsis">{{ $tr('ui.configList.goToSite') }}</q-item-section>
            </q-item>
            <!--Chat action-->
            <q-item clickable v-ripple v-if="$auth.hasAccess('ichat.conversations.index')"
                    @click.native="$eventBus.$emit('toggleMasterDrawer','chat')">
              <q-item-section avatar>
                <q-icon color="primary" name="far fa-comment-alt"/>
              </q-item-section>
              <q-item-section class="ellipsis">Chat</q-item-section>
            </q-item>
          </q-list>
        </q-card-section>
      </q-card>
    </q-dialog>
  </div>
</template>
<script>
export default {
  beforeDestroy() {
    this.$eventBus.$off('setMobileMainAction')
  },
  props: {},
  components: {},
  watch: {
    $route: {
      deep: true,
      handler: function (newValue) {
        this.mainAction = config('app.mobilMainAction')
      }
    },
  },
  mounted() {
    this.$nextTick(function () {
      this.init()
    })
  },
  data() {
    return {
      mainAction: config('app.mobilMainAction'),
      appConfig: config('app'),
      modal: {
        show: false
      }
    }
  },
  computed: {
    quserState() {
      return this.$store.state.quserAuth
    },
  },
  methods: {
    init() {
      this.$eventBus.$on('setMobileMainAction', (data) => {
        this.mainAction = {...this.mainAction, ...data}
      })
    },
    //Do main action
    doMainAction() {
      //Do callback
      if (this.mainAction.callBack) this.mainAction.callBack()
      //Redirect
      else if (this.mainAction.route && (this.$route.name != this.mainAction.route)) {
        this.$router.push({name: this.mainAction.route})
      }
    }
  }
}
</script>
<style lang="stylus">
#footerContent
  background-color white
  color $grey-7

  .item-footer
    text-align center
    font-size 11px
    padding 10px 0

    .item-icon
      font-size 20px
      margin-bottom 3px

  #item-main
    border-radius 50%
    width 50px
    height 50px
    margin auto
    font-size 26px
    color white
</style>
