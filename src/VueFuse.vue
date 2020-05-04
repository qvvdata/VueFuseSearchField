<template>
  <input
    type="search"
    ref="search_field"
    v-model="value"
    autocomplete="chrome-off"
    :placeholder="placeholder"
    :class="{active: is_active, qvvdatasearchfield: true}"
    @focus="activate"
    @keydown="forwardArrowkey"
    @input="value=$event.target.value"
    @blur="deactivate"
    :id="input_id?input_id:undefined"
    >
</template>
<script>
// originally taken from https://github.com/shayneo/vue-fuse/blob/master/src/components/VueFuse.vue at 20a56de
import Fuse from 'fuse.js';

export default {
  name: 'VueFuse',
  data () {
    return {
      fuse: null,
      value: '',
      result: [],
      is_active: false,
    }
  },
  props: {
    placeholder: {
      type: String,
      default: ''
    },
    search: {
      type: String,
      default: ''
    },
    eventName: {
      type: String,
      default: 'fuseResultsUpdated'
    },
    inputChangeEventName: {
      type: String,
      default: 'fuseInputChanged'
    },
    defaultAll: {
      type: Boolean,
      default: true
    },
    list: {
      type: Array
    },
    caseSensitive: {
      type: Boolean,
      default: false
    },
    includeScore: {
      type: Boolean,
      default: true
    },
    includeMatches: {
      type: Boolean,
      default: true
    },
    tokenize: {
      type: Boolean,
      default: false
    },
    matchAllTokens: {
      type: Boolean,
      default: false
    },
    findAllMatches: {
      type: Boolean,
      default: false
    },
    id: {
      type: String,
      default: ''
    },
    shouldSort: {
      type: Boolean,
      default: true
    },
    threshold: {
      type: Number,
      default: 0.6
    },
    location: {
      type: Number,
      default: 0
    },
    distance: {
      type: Number,
      default: 100
    },
    maxPatternLength: {
      type: Number,
      default: 32
    },
    minMatchCharLength: {
      type: Number,
      default: 1
    },
    keys: {
      type: Array
    },
    input_id: {
      type: String
    }
  },
  computed: {
    options () {
      var options = {
        caseSensitive: this.caseSensitive,
        includeScore: this.includeScore,
        includeMatches: this.includeMatches,
        tokenize: this.tokenize,
        matchAllTokens: this.matchAllTokens,
        findAllMatches: this.findAllMatches,
        shouldSort: this.shouldSort,
        threshold: this.threshold,
        location: this.location,
        distance: this.distance,
        maxPatternLength: this.maxPatternLength,
        minMatchCharLength: this.minMatchCharLength,
        keys: this.keys
      }
      if (this.id !== '') {
        options.id = this.id
      }
      return options
    }
  },
  watch: {
    list () {
      this.fuse.list = this.list
      this.fuseSearch()
    },
    search () {
      this.value = this.search;
    },
    value () {
      this.$parent.$emit(this.inputChangeEventName, this.value)
      this.$emit(this.inputChangeEventName, this.value)
      this.fuseSearch()
    },
    result () {
      this.$emit(this.eventName, this.result)
      this.$parent.$emit(this.eventName, this.result)
    }
  },
  methods: {
    initFuse () {
      this.fuse = new Fuse(this.list, this.options)
      if (this.defaultAll) {
        this.result = this.list
      }
      if (this.search) {
        this.value = this.search
      }
    },
    fuseSearch () {
      if (this.value.trim() === '') {
        if (this.defaultAll) {
          this.result = this.list
        } else {
          this.result = []
        }
      } else {
        const r = this.fuse.search(this.value.trim());
        this.result = r
          .filter(d => this.value.trim().length>5 || d.score < 0.2)
          .map(d => d.item);
      }
    },
    activate () {
      this.is_active = true;
      this.fuseSearch();
    },
    deactivate () {
      this.is_active = false;
    },
    clear () {
      this.value = "";
    },
    forwardArrowkey (event) {
      if(event.keyCode==40) {
        this.$emit('arrowkeys', 'down');
        event.preventDefault()
        return;
      }

      if(event.keyCode==38) {
        this.$emit('arrowkeys', 'up');
        event.preventDefault()
        return;
      }

      if(event.keyCode==13) {
        this.$emit('arrowkeys', 'enter');
        event.preventDefault()
        return;
      }
    }
  },
  /**
  * Vue 1.x
  */
  ready () {
    this.initFuse()
  },
  /**
  * Vue 2.x
  */
  mounted () {
    this.initFuse()
  }
}
</script>

<style >
.qvvdatasearchfield::placeholder {
  font-weight: normal;
  color: black;
  -webkit-text-fill-color: black;
  font-family: Sailec, sans-serif;
}
.qvvdatasearchfield.active::placeholder {
  font-weight: normal;
  color: grey;
  -webkit-text-fill-color: grey;
}
</style>