<template>
  <foreignObject
    class="group"
    :x="minX - padding.left - margin"
    :y="minY - padding.top - margin"
    :width="width + (padding.left + padding.right) + margin * 2"
    :height="height + (padding.top + padding.bottom) + margin * 2"
    @mousedown="onMousedown">
      <div class="content" ref="content" :style="contentMargin">
        <slot>
        </slot>
      </div>
  </foreignObject>
</template>

<script>
/** */
import drag from '../mixins/drag'
export default {
  mixins: [
    drag
  ],
  props: {
    nodes: {
      type: Array,
      default: []
    },
    margin: {
      type: Number,
      default: 20
    },
    padding: { // additional area covered by group besides nodes minxy, maxxy
      type: Object,
      default: () => ({ left: 10, right: 10, top: 10, bottom: 10 })
    },
    disableDrag: false,
  },
  computed: {
    minX: vm => !vm.nodes.length ? 0 : vm.nodes.reduce((acc, node) => Math.min(acc, node.x), Infinity),
    maxX: vm => !vm.nodes.length ? 0 : vm.nodes.reduce((acc, node) => Math.max(acc, node.x + node.width), -Infinity),
    minY: vm => !vm.nodes.length ? 0 : vm.nodes.reduce((acc, node) => Math.min(acc, node.y), Infinity),
    maxY: vm => !vm.nodes.length ? 0 : vm.nodes.reduce((acc, node) => Math.max(acc, node.y + node.height), -Infinity),
    width: vm => vm.maxX - vm.minX,
    height: vm => vm.maxY - vm.minY,

    contentMargin: vm => vm.margin && ({
      margin: vm.margin + 'px',
      width: `calc(100% - ${vm.margin * 2}px)`,
      height: `calc(100% - ${vm.margin * 2}px)`
    })
  },
  methods: {
    onDrag ({ x,y }) {
      this.nodes.forEach(node => {
        node.x += x
        node.y += y
      })
    },
    onMousedown (e) {
      e.stopPropagation()
      if (!this.disableDrag) {
        e.preventDefault();
        this.startDrag(e);
      }
    }
  }
}
</script>

<style>
.group .content {
  width: 100%;
  height: 100%;
  position: absolute;
  border-radius: 7px;
  background-color: rgba(100, 100, 100, .25);
  display: inline-block;
}
</style>