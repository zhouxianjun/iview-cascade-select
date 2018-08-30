<template>
    <div :style="wrapStyle">
        <template v-for="(s, index) in list">
            <label v-if="s.label" :style="s.labelStyle" :key="`c-s-l-${index}`">{{ s.label }}</label>
            <Select :key="`c-s-${index}`"
                    v-model="s.value"
                    :clearable="s.clearable"
                    :placeholder="s.placeholder"
                    :placement="s.placement"
                    :transfer="s.transfer"
                    :style="s.style"
                    @on-change="handlerChange(index, $event)">
                <Option value="" v-if="s.all">{{ allText }}</Option>
                <Option v-for="item in s.data" :value="item.id" :key="item.id" :disabled="item.disabled">{{ item.name }}</Option>
            </Select>
        </template>
    </div>
</template>
<script>
import merge from 'merge';
export default {
    name: 'IviewCascadeSelect',
    props: {
        value: Array,
        selects: {
            type: Array,
            required: true
        },
        inline: {
            type: Boolean,
            default: true
        },
        gap: {
            type: Number,
            default: 5
        },
        allText: {
            type: String,
            default: '全部'
        },
        all: {
            type: Boolean,
            default: true
        },
        clearable: {
            type: Boolean,
            default: true
        },
        cache: {
            type: Boolean,
            default: true
        },
        transfer: {
            type: Boolean,
            default: false
        },
        placeholder: {
            type: String,
            default: '请选择'
        },
        placement: {
            type: String,
            default: 'bottom-start'
        }
    },
    data () {
        return {
            list: [],
            cacheData: Array.from({length: this.selects.length}, () => [])
        };
    },
    computed: {
        wrapStyle () {
            let style = {};
            if (this.inline) {
                style.display = 'inline';
            }
            return style;
        },
        currentValue () {
            return this.list.map(s => s.value);
        }
    },
    watch: {
        value (val) {
            if (val.length !== this.currentValue.length || val.some((v, i) => v !== this.currentValue[i])) {
                this.setValue();
            }
        }
    },
    async created () {
        this.list = merge.clone(this.selects).map((s, index) => merge.recursive(true, {
            index,
            value: null,
            label: null,
            clearable: this.clearable,
            all: this.all,
            data: [],
            style: {marginRight: `${this.gap}px`},
            placeholder: this.placeholder,
            placement: this.placement,
            transfer: this.transfer
        }, s));
        Reflect.deleteProperty(this.list[this.list.length - 1].style, 'marginRight');
    },
    mounted () {
        this.setValue();
        if (this.value.length === 0) {
            this.load(0);
        }
    },
    methods: {
        async setValue () {
            for (let [index, v] of this.value.entries()) {
                await this.load(index);
                this.list[index].value = v;
                const nextIndex = index + 1;
                if (nextIndex < this.list.length && nextIndex >= this.value.length) {
                    await this.load(nextIndex);
                }
            }
        },
        async load (index) {
            const parent = this.list[index === 0 ? 0 : index - 1];
            const item = this.list[index];
            if (index === 0 || parent.data.some(d => d.id === parent.value)) {
                if (this.cache) {
                    const cache = this.cacheData[index].find(c => c.parent === parent.value);
                    if (cache) {
                        item.data = cache.data;
                        return;
                    }
                }
                let data = await item.options(parent.value);
                if (typeof item.filter === 'function') {
                    data = await item.filter(data, item);
                }
                item.data = data;
                if (this.cache) {
                    this.cacheData[index].push({parent: parent.value, data});
                }
            }
        },
        async handlerChange (index, val) {
            this.list.filter(s => s.index > index).forEach(s => {
                s.value = null;
                s.data = [];
            });
            this.$emit('input', this.currentValue);
            if (index < this.list.length - 1) {
                await this.load(index + 1);
            }
            this.$emit('change', index, val);
        }
    }
};
</script>
