<template>
    <div class="vuefusesearchfield">
        <VueFuse
            input_id="qvv_bezirke_input"
            :placeholder="placeholder"
            :list="list"
            :keys="search_keys"
            @fuseResultsUpdated="results=$event"
            :defaultAll="defaultAll"
            ref="vue_search"
            @arrowkeys="arrowkeys">
        </VueFuse>
        <div class="results" v-if="results.length>0">
            <div
                :class="{r:true, highlight: index==highlight_index}"
                v-for="(r,index) in results.filter((r, index) => index < 6)"
                @click="select(r)"
                @mouseover="highlight_index=index"
                :key="element_key(r)">
                <slot v-bind:result="r">
                </slot>
            </div>
        </div>
    </div>
</template>
<script>
import VueFuse from './VueFuse.vue';

export default {
    name: 'VueFuseSearchField',
    components: {
        VueFuse
    },
    props: [
        'list',
        'search_keys',
        'element_key',
        'defaultAll',
        'placeholder',
    ],
    data: () => ({
        results: [],
        highlight_index: -1,
        selected_element: null,
    }),
    methods: {
        arrowkeys(key) {
            if(key=='down') {
                this.highlight_index+=1;
            }
            if(key=='up') {
                this.highlight_index-=1;
            }
            this.highlight_index = Math.max(Math.min(this.highlight_index, this.results.length-1), -1);
            if(key=='enter') {
                this.select(this.results[this.highlight_index]);
                this.$refs.vue_search.$refs.search_field.blur();
            }
        },
        select(r) {
            this.selected_element=r;
            this.results=[];
            this.$refs['vue_search'].clear();
            this.highlight_index = -1;
        }
    },
    watch: {
        selected_element() {
            this.$emit('selected', this.selected_element);
        }
    }
}
</script>

<style scoped>

.vuefusesearchfield .results {
  width: 100%;
  max-width: 450px;
  position: absolute;
  border: 1px solid #333;
  border-bottom: none;
  box-sizing: border-box;
  color: black;
  z-index: 100;
}

.vuefusesearchfield .results .r {
  border-bottom: 1px solid black;
  padding: 5px 10px;
  cursor: pointer;
  background: rgba(255, 255, 255, 0.8)
}

.vuefusesearchfield .results >>> .r.highlight {
  background: #A0968B;
}
.vuefusesearchfield >>> input {
  font-family: Sailec, sans-serif;
  line-height: 19px;
  margin-top: -1px;
}
.vuefusesearchfield >>> input::placeholder {
  font-family: Sailec, sans-serif;
}
</style>