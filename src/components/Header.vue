<template>
<header :class="$style.header">
  <slot />
</header>
</template>

<script>
import { offsetTo } from '@/utility/utils'
import parent from '@/utility/parent';

export default {
  props: {
    type: {
      type: String,
      default: 'smart',
      validator: (v) => ['normal', 'smart', 'pinned'].includes(v)
    },
    color: {
      type: String,
      default: 'header',
    }
  },
  data() {
    return {
      offsetToParent: 0,
    }
  },
  methods: {
    handleSmart(scrollTop, direction) {
      if (scrollTop > this.offsetToParent) {
        this.$el.classList.add('pre-show');
        if (direction === 'down') {
          this.$el.classList.remove('show');
        } else {
          this.$el.classList.add('show');
        }
      } else {
        this.$el.classList.remove('pre-show');
      }
    },
  },
  mounted() {
    if (this.type === 'smart') {
      this.offsetToParent = offsetTo(this.$el, null).top;
      this.$layout.on('scroll', this.handleSmart)
    }
  },
  beforeDestroy() {
    this.$layout.off('scroll', this.handleSmart)
  },
  style({ className }) {
    const positionMap = {
      'normal': 'static',
      'smart': 'sticky',
      'pinned': 'sticky',
    }
    return [
      className('header', {
        background: this.$theme.colors[this.color].normal,
        color: this.$theme.colors[this.color].text,
        boxShadow: `0 ${this.$theme.sizes.shadow.normal} ${this.$theme.sizes.shadow.normal} ${this.$theme.colors.background.shade(-50, 0.2)}`,
        borderBottom: `solid 1px ${this.$theme.colors[this.color].shade(-15)}`,
        width: '100%',
        position: positionMap[this.type],
        top: '0',
        padding: this.$theme.sizes.base.multiplyBy(2),
        transition: `transform 250ms ease-out`,
        willChange: 'transform',
        '&.pre-show': {
          transform: 'translateY(-100%)',
        },
        '&.show': {
          transform: 'translateY(0)',
        }
      })
    ];
  },
  inject: ['$layout', '$theme'],
}
</script>