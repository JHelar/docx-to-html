<template>
  <div id="app">
    <input type="file" id="file" v-on:change="openFile">
    <ToC :items="toc" />
	<Page v-for="(page, eIndx) in pages" :elements="page" :key="'page-' + eIndx" /> 
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Page from "./components/elements/Page.vue";
import ToC from "./components/elements/ToC.vue";

import Docx from "docx4js";

interface ToCBranch {
  text: string;
  children: ToCBranch[];
  level: number;
  parent: ToCBranch | null;
  id: string;
}

const getInnertext = (element: any) => {
  if (element.data) {
    return element.data;
  } else {
    return element.children.map(getInnertext).join("");
  }
};

const getPicureURL = (crc32: number, docx: any) => {
  const { parts } = docx;

  for (let key in parts) {
    if (
      parts[key]._data &&
      parts[key]._data.crc32 &&
      parts[key]._data.crc32 === crc32
    ) {
      const blob = new Blob([parts[key]._data], { type: "image/jpeg" });
      return URL.createObjectURL(blob);
    }
  }
};

@Component({
  components: {
    Page,
    ToC
  }
})
export default class App extends Vue {
  currentPage: any[] = [];
  pages: any[] = [];
  tempList: any = null;
  toc: ToCBranch[] = [];
  currentBranch: ToCBranch | null = null;

  closeOpenList() {
    if (this.tempList) {
      this.currentPage.push(Object.assign({}, this.tempList));
      this.tempList = null;
    }
  }
  openFile(e: any) {
    this.pages = [];
    this.currentPage = [];
    const [file] = e.target.files;
    Docx.load(file)
      .then((docx: any) => {
        docx.render(this.renderElement(docx));
      })
      .catch(console.error);
  }

  insertBranch(branch: ToCBranch) {
    if (branch.level === 1) {
        this.toc.push(branch);
    } else {
        if(branch.level > this.currentBranch!.level) {
            this.currentBranch!.children.push(branch)
            branch.parent = this.currentBranch;
        } else if(branch.level < this.currentBranch!.level) {
            let tempBranch = this.currentBranch!.parent;
            while(tempBranch!.level !== branch.level) {
                tempBranch = tempBranch!.parent;
            }
            tempBranch!.parent!.children.push(branch);
            branch.parent = tempBranch!.parent;
        } else {
            this.currentBranch!.parent!.children.push(branch);
            branch.parent = this.currentBranch!.parent;
        }
    }
    this.currentBranch = Object.assign({}, branch)
  }

  renderElement(docx: any) {
    return (type: string, props: any) => {
      switch (type) {
        case "heading":
          this.closeOpenList();
          {
            const { level, node } = props;
            const text = getInnertext(node);
            const id = `${level}-${encodeURI(text)}`;
            this.currentPage.push({
              data: {
                text,
                level,
                id
              },
              component: "Header"
            });

            const branch: ToCBranch = {
              text,
              level,
              id,
              children: [],
              parent: null
            };

            this.insertBranch(branch);
          }
          break;
        case "p":
          this.closeOpenList();
          const text = getInnertext(props.node);

          if (props.node.children.find((c: any) => c.name === "w:hyperlink")) {
            this.currentPage.push({
              data: {
                href: text,
                text
              },
              component: "Hyperlink"
            });
          } else {
            this.currentPage.push({
              data: {
                text
              },
              component: "Paragraph"
            });
          }
          break;
        case "picture":
          this.closeOpenList();
          this.currentPage.push({
            component: "Picture",
            data: {
              src: getPicureURL(props.crc32, docx)
            }
          });
          break;
        case "list":
          if (!this.tempList) {
            this.tempList = {
              data: {
                items: []
              },
              component: "List"
            };
          }
          this.tempList.data.items.push({
            text: getInnertext(props.node)
          });
          break;
        case "lastRenderedPageBreak":
          this.closeOpenList();
          this.pages.push(this.currentPage.splice(0));
          this.currentPage = [];
          break;
        default:
          break;
      }
    };
  }
}
</script>

<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
