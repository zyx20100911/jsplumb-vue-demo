<template>
  <div class="node-item" ref="node"
    :class="[(isActive || isSelected) ? 'active' : '']"
    :style="flowNodeContainer"
    v-click-outside="setNotActive"
    @click="setActive"
    @mouseenter="showAnchor"
    @mouseleave="hideAnchor"
    @dblclick.prevent="editNode"
    @contextmenu.prevent="onContextmenu">
    <!--
    <div class="log-wrap">
      <img :src="node.logImg" alt="">
    </div>
    -->
    <div class="nodeName">{{node.nodeName}}</div>
      <!--连线用--//触发连线的区域-->
<!--      <div class="node-anchor anchor-top" v-show="mouseEnter"></div>
      <div class="node-anchor anchor-right" v-show="mouseEnter"></div>
      <div class="node-anchor anchor-bottom" v-show="mouseEnter"></div>
      <div class="node-anchor anchor-left" v-show="mouseEnter"></div>-->
    <editFormInfo ref="editFormInfo" @setNodeData="setNodeData"></editFormInfo>
  </div>
</template>

<script>
import editFormInfo from "@/views/components/editFormInfo";
import ClickOutside from 'vue-click-outside'
export default {
  name: "nodeItem",
  props: {
      node: Object
  },
  directives: {
    ClickOutside
  },
  components:{
    editFormInfo
  },
  computed: {
    // 节点容器样式
    flowNodeContainer: {
      get() {
        return {
          top: this.node.top,
          left: this.node.left
        };
      }
    }
  },
  data() {
    return {
      mouseEnter: false,
      isActive: false,
      isSelected: false
    };
  },
  mounted() {
    console.log(this.node);
  },
  methods: {
    showAnchor() {
      this.mouseEnter = true
    },
    hideAnchor() {
      this.mouseEnter = false
    },
    onContextmenu() {
      this.$contextmenu({
        items: [{
          label: '删除',
          disabled: false,
          icon: "",
          onClick: () => {
            this.deleteNode()
          }
        },{label: '添加子节点',
          disabled: false,
          icon: "",
          onClick: () => {
            this.add()
          }
        }],
        event,
        customClass: 'custom-class',
        zIndex: 9999,
        minWidth: 180
      })
    },
    setNodeData(data){
      this.node.data = data
      this.$emit('setNodeName', this.node.id, data.nodeName)

    },
    setActive() {
      if(window.event.ctrlKey){
        this.isSelected = !this.isSelected
        return false
      }
      this.isActive = true
      this.isSelected = false
      setTimeout(() => {
        this.$emit("changeLineState", this.node.id, true)
      },0)
    },
    setNotActive() {
      if(!window.event.ctrlKey){
        this.isSelected = false
      }
      if(!this.isActive) {
        return
      }
      this.$emit("changeLineState", this.node.id, false)
      this.isActive = false
    },
    add(){
      this.$emit('add', this.node.id, this.node)
    },
    editNode() {
       this.newNodeName = this.node.nodeName
      this.$refs.editFormInfo.init(this.node.data)
    /*  this.$Modal.confirm({
        render: (h) => {
          return h('form', {
              props: {
                value: this.newNodeName,
                autofocus: true
              },
              on: {
                input: (val) => {
                  this.newNodeName = val;
                }
              }
          })
        },
        onOk: () => {
          console.log(this.newNodeName)
          this.$emit('setNodeName', this.node.id, this.newNodeName)
        }
      })*/
    },
    deleteNode() {
      this.$emit("deleteNode", this.node)
    }
  }
};
</script>

<style lang="less" scoped>
@labelColor: #409eff;
@nodeSize: 20px;
@viewSize: 10px;
.node-item {
  position: absolute;
  display: flex;
  height: 40px;
  width: 120px;
  justify-content: center;
  align-items: center;
  border: 1px solid #b7b6b6;
  border-radius: 4px;
  cursor: move;
  box-sizing: content-box;
  z-index: 9995;
  &:hover {
    z-index: 9998;
    .delete-btn{
      display: block;
    }
  }
  .log-wrap{
    width: 40px;
    height: 40px;
    border-right: 1px solid  #b7b6b6;
  }
  .nodeName {
    flex-grow: 1;
    width: 0;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
  .node-anchor {
    display: flex;
    position: absolute;
    width: @nodeSize;
    height: @nodeSize;
    align-items: center;
    justify-content: center;
    border-radius: 10px;
    cursor: crosshair;
    z-index: 9999;
    //background: -webkit-radial-gradient(sandybrown 10%, white 30%, #9a54ff 60%);
  }
  .anchor-top{
    top: calc((@nodeSize / 2)*-1);
    left: 50%;
    margin-left: calc((@nodeSize/2)*-1);
  }
  .anchor-right{
    top: 50%;
    right: calc((@nodeSize / 2)*-1);
    margin-top: calc((@nodeSize / 2)*-1);
  }
  .anchor-bottom{
    bottom: calc((@nodeSize / 2)*-1);
    left: 50%;
    margin-left: calc((@nodeSize / 2)*-1);
  }
  .anchor-left{
    top: 50%;
    left: calc((@nodeSize / 2)*-1);
    margin-top: calc((@nodeSize / 2)*-1);
  }
}
.active{
  border: 1px dashed @labelColor;
  box-shadow: 0px 5px 9px 0px rgba(0,0,0,0.5);
}
</style>
