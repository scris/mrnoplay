<i18n src="@/assets/json/lang.json"></i18n>
<template>
  <div>
    <notify ref="notify"></notify>
    <div class="container setting-container">
      <div class="setting-left" style="-webkit-app-region: no-drag">
        <div id="settingsnbsppart"></div>
        <b-btn
          variant="light"
          class="new on main-like settingbtn settingleftbtn hover-red"
          @click="goback"
        >
          <div class="back chromeheight"></div>
        </b-btn>
      </div>
      <div class="setting-center">
        <div id="settingsnbsppart"></div>
        <div id="setting-main" style="-webkit-app-region: no-drag">
          <!-- -------------- -->
          <!-- Start On Login -->
          <!-- -------------- -->
          <div v-if="iselectron && !default_lockmode" class="settingfield main-like">
            <span class="label settingslabel">{{ $t("startonlogin") }}</span>
            <br />
            <switcher
              :left="$t('turnoff')"
              :right="$t('turnon')"
              :default="default_startonlogin"
              @toleft="notstartonlogin"
              @toright="startonlogin"
            ></switcher>
          </div>
          <!-- -------- -->
          <!-- Language -->
          <!-- -------- -->
          <div class="settingfield main-like" v-if="!default_lockmode">
            <span class="label settingslabel">语言/Language</span>
            <switcher left="中文" right="English" :default="default_lang" @toleft="cn" @toright="en"></switcher>
          </div>
          <!-- ------ -->
          <!-- Update -->
          <!-- ------ -->
          <div v-if="iselectron && !default_lockmode" class="settingfield main-like">
            <span class="label settingslabel">{{ $t("update") }}</span>
            <br />
            <div>
              <a
                class="tutorial-a check-a"
                @click="check"
                v-if="!checking"
              >{{ $t("checkforupdate") }}</a>
              <span class="label" @click="check" v-if="checking">{{ $t("checking") }}</span>
            </div>
          </div>
          <!-- ------------ -->
          <!-- Default Time -->
          <!-- ------------ -->
          <div class="settingfield main-like" v-if="!default_lockmode">
            <span class="label settingslabel">{{ $t("defaulttime") }}</span>
            <div class="input-btn">
              <input
                type="tel"
                maxlength="4"
                required
                class="off settinginput"
                v-model="default_time"
                @keyup.enter="setdefault_time"
              />
              <b-btn variant="light" class="new submit settingbtn on" @click="setdefault_time"></b-btn>
            </div>
            <div class="warnfather warn settingwarn" v-if="timeNAN">
              <div class="breathe-div"></div>
              <div class="warn">{{ $t("enterinteger") }}</div>
            </div>
          </div>          
          <!-- ----------------- -->
          <!-- Maximun Over Time -->
          <!-- ----------------- -->
          <div class="settingfield main-like" v-if="!default_lockmode">
            <span class="label settingslabel">{{ $t("maximumovertime") }}</span>
            <div class="input-btn">
              <input
                type="tel"
                maxlength="2"
                required
                class="off settinginput"
                v-model="default_maximumOverTime"
                @keyup.enter="setdefault_maximumOverTime"
              />
              <b-btn variant="light" class="new submit settingbtn on" @click="setdefault_maximumOverTime"></b-btn>
            </div>
            <div class="warnfather warn settingwarn" v-if="maximumOverTimeNAN">
              <div class="breathe-div"></div>
              <div class="warn">{{ $t("enterinteger") }}</div>
            </div>
          </div>
          <!-- --------- -->
          <!-- Lock Mode -->
          <!-- --------- -->
          <div class="settingfield settingfield-lockmode main-like">
            <span class="label settingslabel" v-if="!default_lockmode">
              {{ $t("lockmode") }}
              <b>{{ $t("lockmode_off") }}</b>
            </span>
            <div class="input-btn notlockmode" v-if="!default_lockmode">
              <input
                type="password"
                required
                class="off settinginput"
                v-model="default_lockmode_on"
                @keyup.enter="setdefault_lockmode"
                :placeholder="$t('enterpassword')"
              />
              <div class="notlockmode_latterdiv">
                <input
                  type="password"
                  required
                  class="off settinginput"
                  v-model="default_lockmode_on_check"
                  @keyup.enter="setdefault_lockmode_on"
                  :placeholder="$t('enterpassword-reenter')"
                />
                <b-btn
                  variant="light"
                  class="new submit settingbtn on"
                  @click="setdefault_lockmode_on"
                  :placeholder="$t('enterpassword')"
                ></b-btn>
              </div>
              <div class="smallerlabelfather lockmodenoticefather">
                <span class="label smallerlabel">{{ $t("lockmode-notice1") }}</span>
              </div>
              <div class="smallerlabelfather lockmodenoticefather" v-if="iselectron">
                <span class="label smallerlabel">{{ $t("lockmode-notice2") }}</span>
              </div>
            </div>
            <span class="label settingslabel" v-if="default_lockmode">
              {{ $t("lockmode") }}
              <b>{{ $t("lockmode_on") }}</b>
            </span>
            <div class="input-btn" v-if="default_lockmode">
              <input
                type="password"
                required
                class="off settinginput"
                v-model="default_lockmode_off"
                @keyup.enter="setdefault_lockmode_off"
                :placeholder="$t('enterpassword')"
              />
              <b-btn
                variant="light"
                class="new submit settingbtn on"
                @click="setdefault_lockmode_off"
              ></b-btn>
            </div>
          </div>
          <!-- --------- -->
          <!-- Blacklist -->
          <!-- --------- -->
          <div class="settingfield main-like" v-if="!default_lockmode && iselectron">
            <span class="label settingslabel">{{ $t("blacklist") }}</span>
            <div id="download-blacklist" v-if="!blacklist_installed">
              <div class="smallerlabel settingssmallerlabel">{{ $t('blacklistdesc') }}</div>
              <div class="select">
                <b-btn
                  variant="light"
                  class="new on main-like settingbtn download"
                  @click="install_blacklist"
                ></b-btn>
                <div class="notinstalllabel">{{ $t("notinstalled") }}</div>
              </div>
            </div>
            <div id="set-blacklist" v-if="blacklist_installed">
              <div class="smallerlabel settingssmallerlabel">{{ $t('blacklistdesc-installed') }}</div>
              <div class="select">
                <b-btn
                  variant="light"
                  class="new on main-like settingbtn blacklist-settings"
                  @click="set_blacklist"
                ></b-btn>
                <div class="notinstalllabel">{{ $t("installed") }}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/vue-loading.css";
import { Plugins } from "@capacitor/core";
const { Storage, Modals } = Plugins;
import titlepart from "@/components/titlepart";
import notify from "@/components/linxf/notify";
import switcher from "@/components/linxf/switcher";
var ipc = null;
var remote = null;
var fs = null;
var _this = null;
var md5 = require("md5");
if (process.env.VUE_APP_LINXF == "electron") {
  ipc = window.require("electron").ipcRenderer;
  remote = window.require("electron").remote;
  fs = remote.require("fs");
  ipc.on("updatefailed", function (event, arg) {
    this.checking = false;
    this.checked = true;
    this.checkedtext = "Failed";
  });
  ipc.on("updateok", function (event, arg) {
    this.checking = false;
    this.checked = true;
    this.checkedtext = "Found";
  });
  ipc.on("updateno", function (event, arg) {
    this.checking = false;
    this.checked = true;
    this.checkedtext = "No update";
  });
}
export default {
  name: "settings",
  components: {
    loading,
    titlepart,
    notify,
    switcher,
  },
  data() {
    return {
      time: "00:00",
      loading: true,
      iselectron: false,
      isonios: false,
      lang: "en",
      version: "1.0.0",
      checking: false,
      checked: false,
      checkedtext: "",
      // --------
      // Defaults
      // --------
      default_lang: false,
      default_startonlogin: false,
      default_time: 5,
      default_maximumOverTime: 10,
      default_lockmode: false,
      default_lockmode_on: "",
      default_lockmode_on_check: "",
      default_lockmode_off: "",
      // ------------
      // Defaults End
      // ------------
      timeNAN: false,
      todaydate: new Date(),
      todaydate_parsed: "todaytime002000",
      blacklist_installed: false,
      maximumOverTimeNAN: false,
    };
  },
  watch: {
    async lang(val) {
      this.storagesetlang(val);
      this.$i18n.locale = val;
    },
  },
  mounted: function () {
    this.checking = false;
    this.version = process.env.VUE_APP_VER;
    this.todaydate_parsed =
      "todaytime" +
      this.todaydate.getDate() +
      this.todaydate.getMonth() +
      this.todaydate.getFullYear();
    this.storagesetjson("cannotify", false);
    this.getdefault_lang();
    this.getdefault_startonlogin();
    this.getdefault_time();
    this.getdefault_lockmode();
    this.gettodaydata();
    if (process.env.VUE_APP_LINXF == "electron") {
      this.iselectron = true;
      ipc.send("full-screen");
      // macOS
      if (/macintosh|mac os x/i.test(navigator.userAgent)) {
        if (fs.existsSync("/Applications/Mr Noplay Tools")) {
          this.blacklist_installed = true;
        }
      }
      // Windows
      else {
        if (fs.existsSync("C:\\Program Files (x86)\\Mr Noplay Blacklist")) {
          this.blacklist_installed = true;
        }
      }
    }
    this.isonios = this.isiOS(navigator.userAgent);
    _this = this;
    this.loading = false;
    this.timing = true;
    setTimeout(this.interval, 3000);
  },
  beforeDestroy: function () {},
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
        value: val,
      });
    },
    async storagesetjson(key, val) {
      await Storage.set({
        key: key,
        value: JSON.stringify(val),
      });
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
    async getdefault_lang() {
      const keys = await Storage.keys();
      if (keys.keys.indexOf("lang") != -1) {
        const retlang = await Storage.get({ key: "lang" });
        if (retlang.value != null) this.lang = retlang.value;
        else (this.lang = "en"), this.storagesetlang("en");
      } else (this.lang = "en"), this.storagesetlang("en");
      this.$i18n.locale = this.lang;
      if (this.lang == "en") this.default_lang = true;
      else this.default_lang = false;
    },
    async getdefault_startonlogin() {
      const keys = await Storage.keys();
      if (keys.keys.indexOf("startonlogin") != -1) {
        const ret = await Storage.get({ key: "startonlogin" });
        if (ret.value != null) {
          if (ret.value == false || ret.value == "false")
            this.default_startonlogin = false;
          else this.default_startonlogin = true;
        } else
          (this.default_startonlogin = false),
            this.storagesetjson("startonlogin", false);
      } else
        (this.default_startonlogin = false),
          this.storagesetjson("startonlogin", false);
    },
    async getdefault_time() {
      const keys = await Storage.keys();
      // Default Time
      if (keys.keys.indexOf("default_time") != -1) {
        const ret = await Storage.get({ key: "default_time" });
        if (ret.value != null) {
          this.default_time = Number(JSON.parse(ret.value));
        } else (this.default_time = 5), this.storagesetjson("default_time", 5);
      } else (this.default_time = 5), this.storagesetjson("default_time", 5);
      // Maximum Over-Time 
      if (keys.keys.indexOf("default_maximumOverTime") != -1) {
        const ret = await Storage.get({ key: "default_maximumOverTime" });
        if (ret.value != null) {
          this.default_maximumOverTime = Number(JSON.parse(ret.value));
        } else (this.default_maximumOverTime = 10), this.storagesetjson("default_maximumOverTime", 10);
      } else (this.default_maximumOverTime = 10), this.storagesetjson("default_maximumOverTime", 10);
    },
    async getdefault_lockmode() {
      const keys = await Storage.keys();
      if (keys.keys.indexOf("lockmode") != -1) {
        const ret = await Storage.get({ key: "lockmode" });
        if (ret.value != null) {
          if (ret.value == false || ret.value == "false")
            this.default_lockmode = false;
          else this.default_lockmode = true;
        } else
          (this.default_lockmode = false),
            this.storagesetjson("lockmode", false);
      } else
        (this.default_lockmode = false), this.storagesetjson("lockmode", false);
    },
    goback() {
      this.timing = false;
      this.storagesetjson("exit_type", "settings");
      this.$router.push("/");
    },
    startonlogin() {
      this.storagesetjson("startonlogin", true);
      if (process.env.VUE_APP_LINXF == "electron") {
        ipc.send("startonlogin");
        this.$refs.notify.send({
          title: this.$t("success"),
          id: 4,
          message: this.$t("on-startonlogin"),
        });
      }
    },
    notstartonlogin() {
      this.storagesetjson("startonlogin", false);
      if (process.env.VUE_APP_LINXF == "electron") {
        ipc.send("notstartonlogin");
        this.$refs.notify.send({
          title: this.$t("success"),
          id: 5,
          message: this.$t("off-startonlogin"),
        });
      }
    },
    cn() {
      this.lang = "cn";
      if (process.env.VUE_APP_LINXF == "electron") {
        ipc.send("cn");
      }
      this.$refs.notify.send({
        title: "成功",
        id: 6,
        message: "已经切换到中文。",
      });
    },
    en() {
      this.lang = "en";
      if (process.env.VUE_APP_LINXF == "electron") {
        ipc.send("en");
      }
      this.$refs.notify.send({
        title: "Success",
        id: 7,
        message: "Language is set to English.",
      });
    },
    check() {
      if (process.env.VUE_APP_LINXF == "electron") {
        if (this.todayset) {
          this.$refs.notify.send({
            title: this.$t("cannotcheck"),
            id: 20,
            message: this.$t("cannotchecktext"),
          });
        } else {
          ipc.send("checkupdate");
          this.checking = true;
        }
      }
    },
    install_blacklist() {
      if (process.env.VUE_APP_LINXF == "electron") {
        if (this.todayset) {
          this.$refs.notify.send({
            title: this.$t("cannotinstall"),
            id: 30,
            message: this.$t("cannotinstalltext"),
          });
        } else {
          if (this.lang == "cn") {
            ipc.send("blacklist-download-cn");
          } else {
            ipc.send("blacklist-download-en");
          }
        }
      }
    },
    set_blacklist() {
      if (process.env.VUE_APP_LINXF == "electron") {
        if (this.todayset) {
          this.$refs.notify.send({
            title: this.$t("cannotrun"),
            id: 30,
            message: this.$t("cannotsetblacklisttext"),
          });
        } else {
          ipc.send("blacklist-set");
        }
      }
    },
    async popup(title, message) {
      if (process.env.VUE_APP_LINXF == "electron") {
        ipc.send("focus");
        let alertRet = await Modals.alert({
          title: title,
          message: message,
        });
      }
    },
    setdefault_time() {
      if (/(^[1-9]\d*$)/.test(this.default_time)) {
        this.timeNAN = false;
        this.storagesetjson("default_time", this.default_time);
        this.$refs.notify.send({
          title: this.$t("success"),
          id: 14,
          message: this.$t("defaulttimesuccess"),
        });
      } else {
        this.timeNAN = true;
      }
    },
    setdefault_maximumOverTime() {
      if (/(^[1-9]\d*$)/.test(this.default_maximumOverTime)) {
        this.maximumOverTimeNAN = false;
        this.storagesetjson("default_maximumOverTime", this.default_maximumOverTime);
        this.$refs.notify.send({
          title: this.$t("success"),
          id: 33,
          message: this.$t("maximumovertimesuccess"),
        });
      } else {
        this.maximumOverTimeNAN = true;
      }
    },
    setdefault_lockmode_on() {
      if (this.default_lockmode_on_check == this.default_lockmode_on) {
        this.storagesetjson("lockmode", true);
        this.default_lockmode = true;
        this.storagesetjson("lockmode_password", md5(this.default_lockmode_on));
        this.$refs.notify.send({
          title: this.$t("success"),
          id: 13,
          message: this.$t("lockmode_on_success"),
        });
        if (this.iselectron) {
          ipc.send("turnlockon", md5(this.default_lockmode_on));
        }
        this.default_lockmode_on = "";
        this.default_lockmode_on_check = "";
      } else {
        this.$refs.notify.send({
          title: this.$t("fail"),
          id: 17,
          message: this.$t("lockmode_on_fail"),
        });
      }
    },
    async setdefault_lockmode_off() {
      var original = "";
      const keys = await Storage.keys();
      if (keys.keys.indexOf("lockmode_password") != -1) {
        const ret = await Storage.get({ key: "lockmode_password" });
        if (ret.value != null) {
          original = JSON.parse(ret.value);
          if (original == md5(this.default_lockmode_off))
            this._to_set_setdefault_lockmode_off();
          else {
            this.$refs.notify.send({
              title: this.$t("fail"),
              id: 16,
              message: this.$t("lockmode_off_fail"),
            });
          }
        } else this._to_set_setdefault_lockmode_off();
      } else this._to_set_setdefault_lockmode_off();
    },
    _to_set_setdefault_lockmode_off() {
      this.default_lockmode_off = "";
      this.default_lockmode = false;
      this.storagesetjson("lockmode", false);
      this.$refs.notify.send({
        title: this.$t("success"),
        id: 15,
        message: this.$t("lockmode_off_success"),
      });
      if (this.iselectron) {
        ipc.send("turnlockoff");
      }
    },
  },
};
</script>
