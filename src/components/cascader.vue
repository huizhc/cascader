<template>
  <div class="cascader">
    <div>
        <caspanel ref="caspanel" :data="data"></caspanel>
    </div>
  </div>
</template>

<script>
import caspanel from '../components/caspanel';
import Emitter from '../commit/emitter';
export default {
    mixins: [ Emitter ],
    name: 'cascader',
    components: {
        caspanel
    },
    props: {
        data: {
            type: Array,
            default () {
                return [];
            }
        },
        value: {
            type: Array,
            default () {
                return [];
            }
        }
    },
    data () {
        return {
            visible: false,
            selected: [],
            tmpSelected: [],
            currentVal: this.value
        }
    },
    methods: {
        updateSelected () {
            this.broadcast('caspanel', 'o-f-selected', {
                value: this.currentVal
            })
        },
        emitValue (val, oldVal) {
            if (JSON.stringify(val) !== oldVal) {
                this.$emit('on-change', this.currentVal, JSON.parse(JSON.stringify(this.selected)));
            }

        },
        handleClose () {
            this.visible = false;
        },
        updateResult (result) {
            this.tmpSelected = result;
        }
    },
    mounted () {
        this.updateSelected();
        this.$on('o-r-change', params => {
            const lastValue = params.lastValue;
            if (lastValue) {
                const oldVal = JSON.stringify(this.currentVal);
                this.selected = this.tmpSelected;
                let newVal = [];
                this.selected.forEach(item => {
                    newVal.push(item.value);
                })
                this.currentVal = newVal;
                this.emitValue(this.currentVal, oldVal)
                // this.handleClose();
            }
        })
    },
    watch: {
        visible (val) {
            if (val) {
                if (this.currentVal.length) {
                    this.updateSelected();
                }
            }
            this.$emit('o-v-change', val);
        },
        value (val) {
            this.currentVal = val;
            if (!val.length) this.selected = [];
        },
        currentVal () {
            this.$emit('input', this.currentVal);
            // this.updateSelected();
        },
        data () {
            this.$nextTick(() => this.updateSelected());
        }
    }
}
</script>

<style>

</style>