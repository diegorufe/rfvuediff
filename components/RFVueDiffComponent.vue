<template>
  <div id="RFVueDiffComponent">
    <div v-html="html" v-highlight></div>
  </div>
</template>

<script>
import { createPatch } from "diff";
import { Diff2Html } from "diff2html";
import hljs from "highlight.js";
import "highlight.js/styles/googlecode.css";
import "diff2html/dist/diff2html.css";
export default {
  name: "RFVueDiffComponent",
  props: {
    oldString: {
      type: String,
      default: ""
    },
    newString: {
      type: String,
      default: ""
    },
    context: {
      type: Number,
      default: 5
    },
    outputFormat: {
      type: String,
      default: "side-by-side"
    }
  },
  directives: {
    highlight: function(el) {
      let blocks = el.querySelectorAll("code");
      blocks.forEach(block => {
        hljs.highlightBlock(block);
      });
    }
  },
  computed: {
    html() {
      return this.createdHtml(
        this.oldString,
        this.newString,
        this.context,
        this.outputFormat
      );
    }
  },
  methods: {
    /**
     * Function replace higligh values
     * @param html for replace data
     */
    hljs: function(html) {
      let htmlReturn = html.replace(
        /<span class="d2h-code-line-ctn">(.+?)<\/span>/g,
        '<span class="d2h-code-line-ctn"><code>$1</code></span>'
      );
      return htmlReturn;
    },
    /**
     * Method for create html for component
     * @param oldString
     * @param newString
     * @param context
     * @param outputFormat
     */
    createdHtml: function(oldString, newString, context, outputFormat) {
      let args = ["", oldString, newString, "", "", { context: context }];
      let dd = createPatch(...args);
      let outStr = Diff2Html.getJsonFromDiff(dd, {
        inputFormat: "diff",
        outputFormat: outputFormat,
        showFiles: false,
        matching: "lines"
      });
      let html = Diff2Html.getPrettyHtml(outStr, {
        inputFormat: "json",
        outputFormat: outputFormat,
        showFiles: false,
        matching: "lines"
      });
      return this.hljs(html);
    }
  }
};
</script>

<style>
.RFVueDiffComponent {
  float: left;
  width: 100%;
  height: 100%;
}
.hljs {
  display: inline-block;
  padding: 0;
  background: transparent;
  vertical-align: middle;
}
.d2h-file-header {
  display: none;
}
</style>