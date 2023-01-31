<template>
  <div>
    <div class="uk-flex">
      <span
          class="uk-button uk-margin-small-right"
          @click="toggleSwatchPanel()"
          :style="{ backgroundColor: model.color }"
          v-bind="model"
      >
        <i class="uk-icon-caret-down" :style="{ color: textColor }"></i>
      </span>
      <input type="text" class="uk-width-1-1" v-model="color">
    </div>
    <div class="uk-margin-small-top" v-show="showSwatchPanel">
      <v-swatches
          v-model="color"
          :swatches="swatches"
          popover-x="left"
          swatch-size="24"
          :spacing-size="0"
          :show-checkbox="false"
          :swatch-style="{ borderRadius: '0px' }"
          inline
      ></v-swatches>
    </div>
  </div>
</template>

<script>
import VSwatches from 'vue-swatches'
import 'vue-swatches/dist/vue-swatches.css'

export default {
  mixins: [window.Storyblok.plugin],
  components: {
    VSwatches
  },
  data () {
    return {
      showSwatchPanel: false,
      color: '#000000',
      swatches: []
    }
  },
  methods: {
    toggleSwatchPanel () {
      this.showSwatchPanel = !this.showSwatchPanel;
    },
    initWith () {
      return {
        plugin: 'touchwonders-color-picker',
        color: '#000000'
      }
    },
    pluginCreated() {
      let swatches = [];
      if (!this.model.color) {
        this.model.color = '#000000';
      }

      Object.keys(this.options).map((key) => {
        if (key.includes('-')) {
          let groupName = key.split('-', 1)[0];
          if (!swatches[groupName]) swatches[groupName] = [];
          swatches[groupName].push(this.options[key]);
        } else {
          swatches.push(this.options[key]);
        }
      });

      this.swatches = Object.keys(swatches).map((swatchIndex) => {
        return swatches[swatchIndex];
      })
    }
  },
  computed: {
    textColor () {
      let r = parseInt(this.color.substring(1,3),16);
      let g = parseInt(this.color.substring(3,5),16);
      let b = parseInt(this.color.substring(5,7),16);
      let yiq = ((r*299) + (g*587 ) + (b*114)) / 1000;
      return (yiq >= 128) ? '#000000' : '#ffffff';
    }
  },
  watch: {
    'color': {
      handler: function (color) {
        const model = Object.assign(this.model, { color });
        this.$emit('changed-model', model);
      },
      deep: true
    }
  }
}
</script>
<style scoped>
.uk-margin-small-top {
  box-shadow: rgb(0 0 0 / 20%) 4px 0px 8px 0px;
  border-radius: 3px;
  padding: 5px;
  border: 1px solid rgb(204, 204, 204);
  line-height: 0;
  display: inline-block;
}
</style>
