<template>
  <router-link class="mir-btn" :to="to" v-if="type === 'link'" exact>
    <span :class="btnClass">
      <i v-if="icon" :class="'mir-btn-icon'" aria-hidden="true">{{ icon }}</i> <img v-if="!icon && img" :src="img" :class="'mir-btn-img'" aria-hidden="true" />
      <span class="mir-btn-value" v-if="value">{{ value }}</span>
    </span>
  </router-link>

  <a class="mir-btn" v-else-if="type === 'anchor'">
    <span :class="btnClass">
      <i v-if="icon" :class="'mir-btn-icon'" aria-hidden="true">{{ icon }}</i> <span class="mir-btn-value" v-if="value"> {{ value }}</span>
    </span>
  </a>

  <button class="mir-btn" :type="type" v-else>
    <span :class="btnClass">
      <i v-if="icon" :class="'mir-btn-icon'" aria-hidden="true">{{ icon }}</i> <img v-if="!icon && img" :src="img" :class="'mir-btn-img'" aria-hidden="true" />
      <span class="mir-btn-value" v-if="value">{{ value }}</span>
    </span>
  </button>
</template>

<script>
export default {
  name: 'buttonAction',
  props: ['value', 'icon', 'icon-pos', 'img', 'type', 'size', 'theme', 'to', 'color'],
  computed: {
    btnClass() {
      let value = 'mir-btn-container';
      if (this.iconPos) {
        value += ` mir-btn-icon-${this.iconPos}`;
      }
      if (this.size) {
        value += ` mir-btn-size-${this.size}`;
      }
      if (this.theme) {
        value += ` mir-btn-theme-${this.theme}`;
      }
      if (this.color) {
        value += ` mir-btn-${this.color}`;
      }
      return value;
    },
  },
};
</script>

<style lang="scss">
.mir-btn {
  padding: 0;
  margin: 2px;
  width: 165px;
  border: none;
  text-decoration: none !important;
  -webkit-appearance: none;
  display: inline-block;
  font-size: 16px;
  border-radius: 4px;
  background-image: linear-gradient(to right, #0056ff, #0056ff);
}
/* firefox fix - padding */
.mir-btn::-moz-focus-inner {
  padding: 0;
  border: 0;
}
.mir-btn-container::before,
.mir-btn-container::after {
  content: '';
  flex: 1 0 auto;
}
.mir-btn-container {
  font-size: 0.95rem !important;
  font-weight: 400;
  height: 2em;
  line-height: 1;
  color: #fff !important;
  padding: 1.25rem 0.5rem;
  margin: 0;
  cursor: pointer;
  user-select: none;
  display: flex;
  justify-content: center;
  align-items: center;
  -webkit-appearance: none;
}
.mir-btn-container:hover:enabled {
  color: #fff;
  text-decoration: none;
  background: #fff;
}
.mir-btn-icon {
  font-size: 1em;
  line-height: 1;
}
.mir-btn-icon + .mir-btn-value,
.mir-btn-img + .mir-btn-value {
  padding-left: 0.375em;
}
.mir-btn-img {
  height: 1em;
}
.mir-btn-value {
  line-height: 1.5;
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
/* disabled */
.mir-btn.disabled,
.mir-btn[disabled] {
  opacity: 0.333;
  cursor: not-allowed;
  user-select: none;
  pointer-events: none;
  color: #fff !important;
}
.mir-btn.disabled:focus:enabled,
.mir-btn[disabled]:focus:enabled {
  outline: none;
}
/* right aligned icons */
.mir-btn-container.mir-btn-icon-right {
  flex-direction: row-reverse;
}
.mir-btn-container.mir-btn-icon-right .mir-btn-icon + .mir-btn-value {
  padding-left: 0;
  padding-right: 0.375em;
}
/* sizes */
.mir-btn-container.mir-btn-size-sm {
  font-size: 0.75em;
  height: 1.5rem;
  line-height: 1rem;
  padding: 0 1rem;
}
.mir-btn-container.mir-btn-size-sm i.fa,
.mir-btn-container.mir-btn-size-sm i {
  font-size: 0.75rem;
  line-height: 1;
}
.mir-btn-container.mir-btn-size-sm .mir-btn-value {
  font-size: 0.75rem;
}
.mir-btn-container.mir-btn-size-lg {
  font-size: 1.125em !important;
  height: 3rem;
  font-weight: normal;
  padding: 0 1rem;
}
.mir-btn-container.mir-btn-primary {
  background: #0056ff;
}
.mir-btn-container.mir-btn-primary i.fa,
.mir-btn-container.mir-btn-primary i {
  color: rgba(255, 255, 255, 0.667);
}
.mir-btn-container.mir-btn-primary .mir-btn-value {
  color: #fff;
}
.mir-btn-container.mir-btn-success {
  border-color: #fff;
}
.mir-btn-container.mir-btn-success .mir-btn-value {
  color: #000;
}
.mir-btn-container.mir-btn-danger {
  border-color: #fff;
}
.mir-btn-container.mir-btn-danger .mir-btn-value {
  color: #000;
}
</style>
