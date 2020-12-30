<template>
  <div class="home">
    <div class="home-edit">
      <div class="home-eidt-tag">
        <el-button @click="atClick(item)" v-for="item in tagList" :key="item.id"
          >@{{ item.name }}</el-button
        >
      </div>
    </div>
    <div
      @keyup.enter="keyupEnter"
      ref="box"
      class="home-box"
      contenteditable
    ></div>
    <!-- <a
      href="http://ug-playlearn.oss-cn-beijing.aliyuncs.com/app/UserLog/18801136647_20201229_644.txt"
      target="show"
      >链接</a
    >
    <iframe
      name="show"
      id="show"
      width="500"
      height="500"
    >
      <head>
        <meta charset="utf-8" />
        <meta http-equiv="Content-Type" content="txt; charset=uft-8" />
      </head>
    </iframe> -->
  </div>
</template>

<script>
export default {
  name: "Home",
  components: {},
  data() {
    return {
      tagList: [
        { name: "标签1", id: 1 },
        { name: "标签2", id: 2 },
        { name: "标签3", id: 3 },
        { name: "标签4", id: 4 }
      ]
    };
  },
  created() {},
  methods: {
    // 键入Enter事件
    keyupEnter(event) {
      if (!event.shiftKey && event.keyCode === 13) {
        let dom = this.$refs.box;
        let html = dom.innerHTML;
        dom.innerHTML = "";
        this.splitHtml(html);
        event.preventDefault();
        return false;
      }
    },
    // 点击@事件
    atClick(item) {
      let dom = this.$refs.box;
      let b = document.createElement("b");
      // 设置当前插入元素不可编辑 此元素会被整体删除
      b.setAttribute("contenteditable", false);
      b.setAttribute("id", item.id);
      b.innerText = `@${item.name}`;
      dom.appendChild(b);
      // 在插入元素后添加空格
      dom.innerHTML += "&nbsp;";
    },
    splitHtml(str) {
      str = `<div id="strDiv">${str}</div>`;
      let parser = new DOMParser();
      let doc = parser.parseFromString(str, "text/html");
      let dom = doc.getElementById("strDiv");
      let childNodes = dom.childNodes;
      console.log(childNodes);
      let newChildNodes = [];
      for (let i = 0; i < childNodes.length; i++) {
        if (
          childNodes[i].nodeName != "B" &&
          childNodes[i].childNodes &&
          childNodes[i].childNodes.length != 0
        ) {
          newChildNodes = [...newChildNodes, ...childNodes[i].childNodes];
        } else {
          newChildNodes.push(childNodes[i]);
        }
      }
      console.log(newChildNodes);
      let arr = [];
      let index = -1;
      let obj = {
        id: [],
        name: [],
        text: ""
      };
      newChildNodes.forEach(item => {
        if (item.nodeType === 1 && item.nodeName === "B") {
          if (!arr[index] || (arr[index] && arr[index].text)) {
            obj = {
              id: [],
              name: [],
              text: ""
            };
            if (item.id) {
              obj.id.push(item.id);
              obj.name.push(item.innerText);
            }
            arr.push(obj);
            index++;
          } else {
            arr[index].id.push(item.id);
            arr[index].name.push(item.innerText);
          }
        } else if (item.nodeType === 3) {
          if (arr.length == 0) {
            arr.push(obj);
            index++;
          }
          if (item.nodeValue.trim()) {
            arr[index].text += item.nodeValue.trim();
          }
        }
      });
      console.log("结果", arr);
    }
  }
};
</script>

<style lang="less" scoped>
.home {
  width: 100%;
  height: 100%;

  &-edit {
    width: 500px;
    margin: 0 auto;

    &-tag {
      width: 100%;
    }
  }

  &-box {
    width: 500px;
    height: 200px;
    padding: 20px;
    overflow-y: auto;
    box-sizing: border-box;
    border: 1px solid #cccccc;
    margin: 0 auto;
    text-align: left;
  }
  /deep/ .el-tag {
    margin-right: 5px;
    cursor: pointer;
  }
}
</style>
