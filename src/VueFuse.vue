<template>
  <div style="position: relative" :class="{is_active}">
    <input
      type="search"
      ref="search_field"
      v-model="value"
      autocomplete="new-password"
      :placeholder="placeholder"
      :class="{active: is_active, qvvdatasearchfield: true}"
      @focus="activate"
      @keydown="forwardArrowkey"
      @input="value=$event.target.value"
      @blur="deactivate"
      :id="input_id?input_id:undefined"
      name="random_name_bla"
      >
      <div class="search_icon">
          <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
              <path fill-rule="evenodd" clip-rule="evenodd" d="M11 5C7.68629 5 5 7.68629 5 11C5 14.3137 7.68629 17 11 17C14.3137 17 17 14.3137 17 11C17 7.68629 14.3137 5 11 5ZM3 11C3 6.58172 6.58172 3 11 3C15.4183 3 19 6.58172 19 11C19 15.4183 15.4183 19 11 19C6.58172 19 3 15.4183 3 11Z" fill="#3B3A39"/>
              <path fill-rule="evenodd" clip-rule="evenodd" d="M15.2929 15.2929C15.6834 14.9024 16.3166 14.9024 16.7071 15.2929L20.7071 19.2929C21.0976 19.6834 21.0976 20.3166 20.7071 20.7071C20.3166 21.0976 19.6834 21.0976 19.2929 20.7071L15.2929 16.7071C14.9024 16.3166 14.9024 15.6834 15.2929 15.2929Z" fill="#3B3A39"/>
          </svg>
      </div>
  </div>
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
<style scoped>
.search_icon {
    position: absolute;
    right: 6px;
    top: 5px;
    pointer-events: none;
}

.is_active .search_icon path {
  fill: grey;
}

.qvvdatasearchfield[type="search"]::-webkit-search-decoration,
.qvvdatasearchfield[type="search"]::-webkit-search-cancel-button,
.qvvdatasearchfield[type="search"]::-webkit-search-results-button,
.qvvdatasearchfield[type="search"]::-webkit-search-results-decoration {
  -webkit-appearance:none;
}
</style>