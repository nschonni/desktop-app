<template>
  <div id="main">
    <button class="serverSelectBtn" v-on:click="showServersList()">
      <div class="flexRow" style="height: 100%;">
        <div class="flexColumn" align="left">
          <div class="small_text" style="margin-top: 8px">
            {{ connectToText }}
          </div>
          <div style="min-height: 4px" />

          <div class="flexRow">
            <serverNameControl
              class="serverName"
              style="max-width: 245px;"
              :isLargeText="true"
              :server="fastestServer ? fastestServer : this.server"
              :isFastestServer="isFastestServer && !fastestServer"
              :isRandomServer="isRandomServer"
              :isShowPingPicture="!(isFastestServer || isRandomServer)"
            />
          </div>
        </div>

        <div class="flexRow flexRowRestSpace" />

        <div class="arrowRightSimple"></div>
      </div>
    </button>
  </div>
</template>

<script>
import serverNameControl from "@/components/controls/control-server-name.vue";
import { VpnStateEnum, PauseStateEnum } from "@/store/types";

export default {
  props: ["onShowServersPressed", "isExitServer"],
  components: {
    serverNameControl
  },
  computed: {
    connectToText: function() {
      if (this.isConnecting && !this.isExitServer) {
        return "Connecting to ...";
      }
      // Multi-Hop
      if (this.isMultiHop === true) {
        if (this.isExitServer) {
          if (this.isConnected) return "Traffic is routed via exit server";
          return "Exit server is";
        }
        if (this.isConnected) return "Traffic is routed via entry server";
        return "Entry server is";
      }

      // Single-Hop
      if (this.isConnected && !this.isPaused)
        return "Traffic is routed via server";

      if (
        (this.$store.getters["settings/isFastestServer"] &&
          this.fastestServer) ||
        this.$store.getters["vpnState/isDisconnecting"]
      )
        return "Fastest available server";

      return "Selected server";
    },
    server: function() {
      return this.isExitServer
        ? this.$store.state.settings.serverExit
        : this.$store.state.settings.serverEntry;
    },
    isConnected: function() {
      return (
        this.$store.state.vpnState.connectionState === VpnStateEnum.CONNECTED
      );
    },
    isConnecting: function() {
      return this.$store.getters["vpnState/isConnecting"];
    },
    isDisconnected: function() {
      return (
        this.$store.state.vpnState.connectionState === VpnStateEnum.DISCONNECTED
      );
    },
    isPingingServers: function() {
      return this.$store.state.vpnState.isPingingServers;
    },
    isFastestServer: function() {
      if (
        this.isDisconnected /*|| this.isPingingServers*/ &&
        this.$store.getters["settings/isFastestServer"]
      )
        return true;
      return false;
    },
    isPaused: function() {
      return this.$store.state.vpnState.pauseState == PauseStateEnum.Paused;
    },
    fastestServer: function() {
      if (!this.isFastestServer || this.isPingingServers == true) return null;
      return this.$store.getters["vpnState/fastestServer"];
    },
    isRandomServer: function() {
      if (!this.isDisconnected) return false;
      return this.isExitServer
        ? this.$store.getters["settings/isRandomExitServer"]
        : this.$store.getters["settings/isRandomServer"];
    },
    isMultiHop: function() {
      return this.$store.state.settings.isMultiHop;
    }
  },
  methods: {
    showServersList() {
      if (this.onShowServersPressed != null)
        this.onShowServersPressed(this.isExitServer);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@import "@/components/scss/constants";

#main {
  @extend .left_panel_block;
}

.small_text {
  font-size: 14px;
  line-height: 17px;
  letter-spacing: -0.3px;
  color: var(--text-color-details);
}

.serverSelectBtn {
  padding: 0px;
  border: none;
  background-color: inherit;
  outline-width: 0;
  cursor: pointer;

  height: 82px;
  width: 100%;

  padding-bottom: 4px;
}

.serverName {
  max-width: 270px;
}
</style>
