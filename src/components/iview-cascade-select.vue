<template>
    <div :style="wrapStyle">
        <template v-for="(s, index) in list">
            <label v-if="s.label" :key="`c-s-l-${index}`">{{ s.label }}</label>
            <Select :key="`c-s-${index}`" v-model="s.value" :clearable="s.clearable" :style="s.style" @on-change="handlerChange(index, $event)">
                <Option value="" v-if="s.all">{{ allText }}</Option>
                <Option v-for="item in s.data" :value="item.id" :key="item.id">{{ item.name }}</Option>
            </Select>
        </template>
    </div>
</template>
<script>
import merge from 'merge';
export default {
    name: 'IviewCascadeSelect',
    props: {
        value: [String, Number],
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
        }
    },
    data () {
        return {
            list: []
        };
    },
    computed: {
        wrapStyle () {
            let style = {};
            if (this.inline) {
                style.display = 'inline';
            }
            return style;
        }
    },
    async created () {
        this.list = merge.clone(this.selects).map((s, index) => merge.recursive(true, {index, value: null, label: null, clearable: true, all: true, data: [], style: {marginRight: `${this.gap}px`}}, s));
        Reflect.deleteProperty(this.list[this.list.length - 1].style, 'marginRight');
        await this.options(0, true);

        this.list.slice(1).filter(s => s.defaultValue !== null && s.defaultValue !== undefined).reverse().forEach(s => {
            const parent = this.list[s.index - 1];
            if (parent && parent.value !== null && parent.value !== undefined) {
                this.options(s.index, true);
            }
        });
    },
    methods: {
        async options (index, setValue = false) {
            const item = this.list[index];
            if (!item.data || !item.data.length) {
                let data = await item.options(this.list[index === 0 ? 0 : index - 1].value);
                if (typeof item.filter === 'function') {
                    data = await item.filter(data, item);
                }
                item.data = data;
            }
            if (setValue) {
                item.value = item.defaultValue;
                this.handlerChange(index, item.value);
            }
        },
        async handlerChange (index, val) {
            if (index < this.list.length - 1) {
                await this.options(index + 1);
            } else {
                this.$emit('input', val);
            }
            this.list.filter(s => s.index > index).forEach(s => s.value = null);
            this.$emit('change', index, val);
        }
    }
};
</script>
