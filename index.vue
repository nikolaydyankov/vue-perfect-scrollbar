<template>
  <section class="ps-container" :is="$props.tagname" @mouseover.once="update" @ps-scroll-y="scrollHanle" @ps-scroll-x="scrollHanle" @ps-scroll-up="scrollHanle" @ps-scroll-down="scrollHanle" @ps-scroll-left="scrollHanle" @ps-scroll-right="scrollHanle" @ps-y-reach-start="scrollHanle" @ps-y-reach-end="scrollHanle" @ps-x-reach-start="scrollHanle" @ps-x-reach-end="scrollHanle">
    <slot></slot>
  </section>
</template>
<style lang="scss">
@import '~perfect-scrollbar/css/perfect-scrollbar.css';

.ps-container {
  position: relative;
}

</style>
<script>
import PerfectScrollbar from 'perfect-scrollbar'

export default {
  name: 'vue-perfect-scrollbar',
  props: {
    settings: {
      default: undefined
    },
    swicher: {
      type: Boolean,
      default: true,
    },
    tagname: {
      type: String,
      default: "section"
    }
  },
  data() {
    return {
      ps: undefined
    }
  },
  methods: {
    scrollHanle(evt) {
      this.$emit(evt.type, evt)
    },

    update() {
      this.ps.update(this.$el)
    },

    __init() {
      if (this.swicher) {
        if (!this._ps_inited) {
          this._ps_inited = true
          // scrollBar.initialize(this.$el, this.settings)
          this.ps = new PerfectScrollbar(this.$el);
        } else {
          this.update(this.$el)
        }
      }
    },

    __uninit() {
      this.ps.destroy(this.$el)
      this._ps_inited = false
    },
  },

  watch: {
    swicher(val) {
      if (val && !this._ps_inited) {
        this.__init()
      }
      if (!val && this._ps_inited) {
        this.__uninit()
      }
    },

    $route() {
      this.update()
    },

  },

  mounted() {
    // for support ssr
    if (!this.$isServer) {
      this.__init()
    }
  },

  updated() {
    this.$nextTick(this.update)
  },

  activated() {
    this.__init()
  },

  deactivated() {
    this.__uninit()
  },

  beforeDestroy() {
    this.__uninit()
  },
}
</script>