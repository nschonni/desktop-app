<template>
  <div ef="contentdiv" style="margin: 40px">
    <p class="big_text">Install IVPN Privileged Helper Tool</p>
    <p class="small_text">
      Adhering to Apple's security Best Practices, IVPN uses a privileged helper
      tool for coordinating actions that require elevated privileges (e.g.
      updating the macOS routing table when connecting to VPN) whilst reducing
      the amount of code that runs with elevated privileges.
    </p>
    <p class="small_text">
      To install this helper tool, MacOS will prompt you for the username and
      password that you use to login to macOS.
    </p>

    <div class="flexRow">
      <div class="flexRowRestSpace" />
      <button class="master btn" v-on:click="onOk">
        OK
      </button>
      <button class="slave btn" v-on:click="onCancel">
        Cancel
      </button>
    </div>
  </div>
</template>

<script>
const sender = window.ipcSender;

export default {
  methods: {
    onOk: function() {
      this.$store.dispatch("daemonAllowedToInstallMacOS", true);
      this.doClose();
    },
    onCancel: function() {
      this.$store.dispatch("daemonAllowedToInstallMacOS", false);
      this.doClose();
    },
    doClose: async function() {
      await sender.CloseWindow("macOSDaemonInstallRequiredWindow");
    }
  }
};
</script>

<style scoped lang="scss">
@import "@/components/scss/constants";

.big_text {
  @extend .settingsBoldFont;
  font-size: 24px;
}
.small_text {
  @extend .settingsGrayDescriptionFont;
}

.btn {
  width: auto;
  height: 32px;

  padding-left: 20px;
  padding-right: 20px;
  margin-left: 10px;
}
</style>
