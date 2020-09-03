>
<template>
  <div id="app">
    <transition name="fade">
      <router-view v-if="isRouterAlive"></router-view>
    </transition>
  </div>
</template>
<script>
export default {
  name: "App",
  provide() {
    //父组件中通过provide来提供变量，在子组件中通过inject来注入变量。
    return {
      reload: this.reload
    };
  },
  data() {
    return {
      isRouterAlive: true //控制视图是否显示的变量
    };
  },
  created() {
    this.rem();
  },
  methods: {
    reload() {
      this.isRouterAlive = false; //先关闭，
      this.$nextTick(function() {
        this.isRouterAlive = true; //再打开
      });
    },
    rem() {
      (function(designWidth, maxWidth) {
        var doc = document,
          win = window,
          docEl = doc.documentElement,
          remStyle = document.createElement("style"),
          tid;

        function refreshRem() {
          var width = docEl.getBoundingClientRect().width;
          maxWidth = maxWidth || 540;
          width > maxWidth && (width = maxWidth);
          var rem = (width * 100) / designWidth;
          remStyle.innerHTML = "html{font-size:" + rem + "px;}";
        }

        if (docEl.firstElementChild) {
          docEl.firstElementChild.appendChild(remStyle);
        } else {
          var wrap = doc.createElement("div");
          wrap.appendChild(remStyle);
          doc.write(wrap.innerHTML);
          wrap = null;
        }
        //要等 wiewport 设置好后才能执行 refreshRem，不然 refreshRem 会执行2次；
        refreshRem();

        win.addEventListener(
          "resize",
          function() {
            clearTimeout(tid); //防止执行两次
            tid = setTimeout(refreshRem, 300);
          },
          false
        );

        win.addEventListener(
          "pageshow",
          function(e) {
            if (e.persisted) {
              // 浏览器后退的时候重新计算
              clearTimeout(tid);
              tid = setTimeout(refreshRem, 300);
            }
          },
          false
        );

        if (doc.readyState === "complete") {
          doc.body.style.fontSize = "16px";
        } else {
          doc.addEventListener(
            "DOMContentLoaded",
            function(e) {
              doc.body.style.fontSize = "16px";
            },
            false
          );
        }
      })(750, 750);
    }
  }
};
</script>
