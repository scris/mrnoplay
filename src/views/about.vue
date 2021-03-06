<i18n src="@/assets/json/lang.json"></i18n>
<template>
  <div>
    <notify ref="notify"></notify>
    <div class="container" style="-webkit-app-region: no-drag">
      <div id="nbsppart"></div>
      <div id="main">
        <div class="notifyboard border">
          <div class="juniornotifyboard on-notbtn">
            <div class="digitaltop notifytop">{{ $t("about") }}</div>
            <div
              class="notifyfather notifyfather-about"
              style="-webkit-app-region: no-drag"
            >{{ $t("abouttext") }}</div>
          </div>
        </div>
        <b-btn variant="light" class="new on largebtn" @click="goback">
          <div class="largebtn-innertext">{{ $t("back") }}</div>
        </b-btn>
        <br />
        <div>
          <div class="authorinfo">Copyright &copy; 2019-2020 {{ $t("tianze") }}.</div>
          <div class="authorinfo">
            <a class="tutorial-a" @click="github">Github</a> @scris
          </div>
          <div class="authorinfo">{{ $t("scrisproduct") }}</div>
        </div>
        <div class="authorinfo">{{ $t("version") }} {{ version }} ({{ buildnumber }})</div>
        <div class="authorinfo">
          <a class="tutorial-a" @click="tutorial">{{ $t("tutorial") }}</a>&nbsp;
          <a class="tutorial-a" @click="website">{{ $t("website") }}</a>&nbsp;
          <a class="tutorial-a" @click="feedback">{{ $t("feedback") }}</a>&nbsp;
          <a class="tutorial-a" @click="donate">{{ $t("donate") }}</a>&nbsp;
          <a class="tutorial-a" @click="roadmap">{{ $t("roadmap") }}</a>
        </div>
      </div>
    </div>
    <br />
    <div class="authorinfo red weblimited" v-if="isweb">{{ $t("webversion.1") }}</div>
    <div class="authorinfo red weblimited" v-if="isweb">{{ $t("webversion.2") }}</div>
  </div>
</template>

<script>
import loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/vue-loading.css";
import { Plugins } from "@capacitor/core";
const { Storage } = Plugins;
import titlepart from "@/components/titlepart";
import notify from "@/components/linxf/notify";
var ipc = null;
var _this = null;
if (process.env.VUE_APP_LINXF == "electron") {
  ipc = window.require("electron").ipcRenderer; //use window.require instead of require
}
export default {
  name: "about",
  components: {
    loading,
    titlepart,
    notify
  },
  data() {
    return {
      time: "00:00",
      loading: true,
      iselectron: false,
      isonios: false,
      isweb: false,
      lang: "en",
      version: "1.0.0",
      todaydate: new Date(),
      todaydate_parsed: "todaytime002000",
      buildnumber: "0",
    };
  },
  watch: {
    async lang(val) {
      this.storagesetlang(val);
      this.$i18n.locale = val;
    }
  },
  mounted: function() {
    this.version = process.env.VUE_APP_VER;
    this.buildnumber = process.env.VUE_APP_BUILD;
    this.i18nsetlang();
    this.storagesetjson("cannotify", false);
    if (process.env.VUE_APP_LINXF == "electron") {
      this.iselectron = true;
    }
    if (
      process.env.VUE_APP_LINXF == "web" ||
      process.env.NODE_ENV == "development"
    ) {
      this.isweb = true;
    }
    this.isonios = this.isiOS(navigator.userAgent);
    _this = this;
    this.loading = false;
    this.timing = true;
    this.todaydate_parsed =
      "todaytime" +
      this.todaydate.getDate() +
      this.todaydate.getMonth() +
      this.todaydate.getFullYear();
    this.gettodaydata();
  },
  beforeDestroy: function() {},
  methods: {
    isiPad(userAgent) {
      return userAgent.indexOf("iPad") > -1;
    },
    isiPhone(userAgent) {
      return userAgent.indexOf("iPhone") > -1;
    },
    isiOS(userAgent) {
      return this.isiPad(userAgent) || this.isiPhone(userAgent);
    },
    async storagesetlang(val) {
      await Storage.set({
        key: "lang",
        value: val
      });
    },
    async storagesetjson(key, val) {
      await Storage.set({
        key: key,
        value: JSON.stringify(val)
      });
    },
    async i18nsetlang() {
      const keys = await Storage.keys();
      if (keys.keys.indexOf("lang") != -1) {
        const retlang = await Storage.get({ key: "lang" });
        if (retlang.value != null) _this.lang = retlang.value;
        else (_this.lang = "en"), _this.storagesetlang("en");
      } else (_this.lang = "en"), _this.storagesetlang("en");
      _this.$i18n.locale = _this.lang;
    },
    async gettodaydata() {
      const keys = await Storage.keys();
      if (keys.keys.indexOf(this.todaydate_parsed) != -1) {
        const ret_ttl = await Storage.get({ key: this.todaydate_parsed });
        if (JSON.parse(ret_ttl.value) != null) {
          this.todayset = true;
        }
      } else {
        this.todayset = false;
      }
    },
    i18nchinese() {
      this.lang = "cn";
    },
    i18nenglish() {
      this.lang = "en";
    },
    goback() {
      this.timing = false;
      this.storagesetjson("exit_type", "about");
      this.$router.push("/");
    },
    tutorial() {
      this.$router.push("tutorial");
    },
    github() {
      if (this.iselectron) {
        if (this.todayset) {
          this.$refs.notify.send({
            title: this.$t("cannotrun"),
            id: 21,
            message: this.$t("cannotgithub")
          });
        } else {
          ipc.send("github");
        }
      } else {
        window.open("https://github.com/scris/mrnoplay/", "_blank");
      }
    },
    website() {
      if (this.iselectron) {
        if (this.todayset) {
          this.$refs.notify.send({
            title: this.$t("cannotrun"),
            id: 22,
            message: this.$t("cannotwebsite")
          });
        } else {
          ipc.send("website");
        }
      } else {
        window.open("https://mrnoplay.scris.top/", "_blank");
      }
    },
    roadmap() {
      if (this.iselectron) {
        if (this.todayset) {
          this.$refs.notify.send({
            title: this.$t("cannotrun"),
            id: 31,
            message: this.$t("cannotwebsite")
          });
        } else {
          ipc.send("roadmap");
        }
      } else {
        window.open("https://roadmap-for-mrnoplay.now.sh/", "_blank");
      }
    },
    feedback() {
      if (this.iselectron) {
        if (this.todayset) {
          this.$refs.notify.send({
            title: this.$t("cannotrun"),
            id: 23,
            message: this.$t("cannotfeedback")
          });
        } else {
          if (this.lang == "cn") ipc.send("feedback-cn");
          else ipc.send("feedback-en");
        }
      } else {
        if (this.lang == "cn")
          window.open("https://support.qq.com/products/127085?", "_blank");
        else window.open("mailto:tianze@scris.top");
      }
    },
    donate() {
      this.$router.push("donate");
    },
  },
  async clear_dangerous() {
    await Storage.clear();
  }
};
</script>
