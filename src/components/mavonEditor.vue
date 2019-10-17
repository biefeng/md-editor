<template>
  <div id="mavon">
    <div style="text-align: left;margin-top: 30px;width: 600px;">
      <el-input
        placeholder="请输入标题.."
        v-model="title"
        clearable>
      </el-input>
    </div>
    <div id="editArea">
      <mavonEditor class="mavonEditor"
                   :subfield="subfield"
                   :codeStyle="code_style"
                   :ishljs="true"
                   :externalLink="externalLink" v-model="context" v-on:save="saveData(context,render)"></mavonEditor>
      <!--<el-row>
        <el-button type="primary" @click="back" round>返回</el-button>
        <el-button type="success" @click="submit" round>提交</el-button>
      </el-row>-->
    </div>
  </div>
</template>

<script>
  import {mavonEditor} from "mavon-editor";

  export default {
    name: "editor",
    data() {
      return {
        title: '', //标题
        blogId: '', //当前编辑的博客的id
        context: " ", //输入的数据
        ishljs: true,
        toolbarsFlag: true,
        toolbars: {
          bold: true, // 粗体
          italic: true, // 斜体
          header: true, // 标题
          underline: true, // 下划线
          mark: true, // 标记
          superscript: true, // 上角标
          quote: true, // 引用
          ol: true, // 有序列表
          link: true, // 链接
          imagelink: true, // 图片链接
          help: true, // 帮助
          code: true, // code
          subfield: true, // 是否需要分栏
          fullscreen: true, // 全屏编辑
          readmodel: true, // 沉浸式阅读
          /* 1.3.5 */
          undo: true, // 上一步
          trash: true, // 清空
          save: true, // 保存（触发events中的save事件）
          /* 1.4.2 */
          navigation: true // 导航目录
        },

        subfield: true,
        code_style: "atom-one-dark",
        externalLink: {
          markdown_css: function () {
            // 这是你的markdown css文件路径
            return "./markdown/github-markdown.min.css";
          },
          hljs_js: function () {
            // 这是你的hljs文件路径
            return "./highlightjs/highlight.min.js";
          },
          hljs_css: function (css) {
            // 这是你的代码高亮配色文件路径
            return "./highlightjs/styles/" + css + ".min.css";
          },
          hljs_lang: function (lang) {
            // 这是你的代码高亮语言解析路径
            return "./highlightjs/languages/" + lang + ".min.js";
          },
          katex_css: function () {
            // 这是你的katex配色方案路径路径
            return "./katex/katex.min.css";
          },
          katex_js: function () {
            // 这是你的katex.js路径
            return "./katex/katex.min.js";
          }
        }
      };
    },
    methods: {
      back() {
        this.$router.go(-1)
      },
      submit() {

      },
      saveData(value, render) {
        let title = this.title
        if (!title || title == '') {
          this.$message({
            message: "请输入文章标题",
            type: 'warning'
          })
          return
        }
        let markdownIt = mavonEditor.getMarkdownIt();
        let htmlValue = markdownIt.render(value)
        /*var params = new URLSearchParams();
        params.append('md', value);
        params.append('html', htmlValue);*/
        let params = {
          "md": value,
          "html": htmlValue,
          "title": title,
          'guid': this.blogId
        }

        this.$http.post('service-1/blog/save', params, {
          headers: {
            post: {
              'Content-Type': 'application/json'
            }
          }
        }).then(response => {
          this.blogId = response.data.data
          this.$message({
            message: response.data.message,
            type: 'success'
          })
        }).catch(e => {
          this.$message(e.message)
        })
      }
    },
    components: {
      mavonEditor
    }
  };
</script>
<style scoped>
  #mavon {
    position: relative;
    text-align: center;
    margin: auto;
    height: 100%;
    width: 95%;
  }

  .el-input__inner {
    width: 400px;
  }

  .mavonEditor {
    position: relative;
    width: 100%;
    height: 700px;
  }

  div .el-row {
    margin-top: 10px;
    margin-left: 0px;
    text-align: right;
  }

  .el-button {
    margin-right: 20px;
  }

  div #editArea {
    margin: auto;
    margin-top: 30px;
    width: 100%;
  }

  div.el-input {
    display: inline;
    width: 400px;
  }


</style>

