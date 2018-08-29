<template>
    <div :style="wrapStyle">
        <template v-for="(s, index) in list">
            <label v-if="s.label" :style="s.labelStyle" :key="`c-s-l-${index}`">{{ s.label }}</label>
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
    watch: {
        value () {
            this.setValue();
        }
    },
    async created () {
        this.list = merge.clone(this.selects).map((s, index) => merge.recursive(true, {index, value: null, label: null, clearable: true, all: true, data: [], style: {marginRight: `${this.gap}px`}}, s, {isLoad: false}));
        Reflect.deleteProperty(this.list[this.list.length - 1].style, 'marginRight');
    },
    mounted () {
        this.setValue();
    },
    methods: {
        setValue () {
            this.value.forEach((v, index) => {
                this.load(index);
                this.list[index].value = v;
            });
        },
        async load (index) {
            const item = this.list[index];
            if (item.isLoad === false) {
                let data = await item.options(this.list[index === 0 ? 0 : index - 1].value);
                if (typeof item.filter === 'function') {
                    data = await item.filter(data, item);
                }
                item.data = data;
                item.isLoad = true;
            }
        },
        async handlerChange (index, val) {
            this.list.filter(s => s.index > index).forEach(s => s.value = null);
            this.$emit('input', this.list.map(s => s.value));
            if (index < this.list.length - 1) {
                await this.load(index + 1);
            }
            this.$emit('change', index, val);
        }
    }
};
</script>
