<div id="app">
  <textarea name="" id="" cols="30" rows="30" v-model="content" autofocus placeholder="请输入vue模板"></textarea>
  <div class="btn-center">
      <button @click="run">运行代码</button>
      <button @click="reset">清除</button>
  </div>
</div>
<div id="result"></div>
<script src="./node_modules/vue/dist/vue.js"></script>
<script>
  new Vue({
      el: "#app",
      data() {
          return {
              content: ""
          }
      },
      methods: {
          getSource(type) {
              const reg = new RegExp(`<${type}[^>]*>`);
              let content = this.content;
              let matches = content.match(reg);
              if (matches) {
                  let start = content.indexOf(matches[0]) + matches[0].length;
                  let end = content.lastIndexOf(`</${type}`);
                  console.log(content.slice(start, end));
                  return content.slice(start, end)
              }
          },
          run: function () {
              let template = this.getSource("template");
              if (!template) return
              let script = this.getSource("script");
              if (script) {
                  script = script.replace(/export default/, "return");
              }
              let styleCss = this.getSource("style");
              let style = document.createElement("style");
              style.innerHTML = styleCss;
              document.head.appendChild(style);
              let obj = new Function(script)();
              obj.template = template;
              let Profile = Vue.extend(obj);
              new Profile().$mount("#result")
          },
          reset() {
              this.content = ''
          }
      }
  })
</script>
