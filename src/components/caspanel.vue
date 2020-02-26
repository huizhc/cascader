<template>
  <span class="caspanel">
      <ul v-if="data && data.length">
          <casitem v-for="(item, index) in data" :key="index" :tmp-item="tmpItem" :data="item" @click.native.stop="handleClickItem(item)"></casitem>
      </ul>
      <caspanel v-if="sublist && sublist.length" :data="sublist"></caspanel>
  </span>
</template>

<script>
import Emitter from '../commit/emitter';
import casitem from '../components/casitem.vue'
export default {
    name: 'caspanel',
    mixins: [ Emitter ],
    components: {
        casitem
    },
    props: {
        data: {
            type: Array,
            default () {
                return [];
            }
        }
    },
    data () {
        return {
            tmpItem: {},
            result: [],
            sublist: []
        }
    },
    methods: {
        handleClickItem (item) {
            this.handleTriggerItem(item);
        },
        handleTriggerItem (item) {
            let backItem = this.getBaseItem(item);
            this.tmpItem = backItem;
            this.emitUpdate([backItem]);
            if (item.children && item.children.length) {
                this.sublist = item.children;
                this.dispatch('cascader', 'o-r-change', {
                    lastValue: true
                })
            } else {
                this.sublist = [];
                this.dispatch('cascader', 'o-r-change', {
                    lastValue: true
                })
            }
        },
        updateResult(item) {
            this.result = [this.tmpItem].concat(item);
            this.emitUpdate(this.result);
        },
        getBaseItem (item) {
            let backItem = Object.assign({}, item);
            if (backItem.children) {
                delete backItem.children;
            }
            return backItem;
        },
        emitUpdate (result) {
            this.$parent.updateResult(result);
        }
    },
    mounted () {
        this.$on('o-f-selected', params => {
            const val = params.value;
            let value = [...val];
            for (let i = 0; i < value.length; i ++) {
                for (let j = 0; j < this.data.length; j ++) {
                    if (value[i] === this.data[j].value) {
                        this.handleTriggerItem(this.data[j]);
                        value.splice(0, 1);
                        this.$nextTick(() => {
                            this.broadcast('caspanel', 'o-f-selected', {
                                value: value
                            });
                        });
                        return false;
                    }
                }
            }
        })
    },
    watch: {
        data () {
            this.sublist = [];
        }
    }
}
</script>

<style>

</style>