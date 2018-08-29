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
        this.list = merge.clone(this.selects).map((s, index) => merge.recursive(true, {index, value: null, label: null, clearable: true, all: true, data: [], style: {marginRight: `${this.gap}px`}}, s));
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
                let data = await item.options(parent.value);
                if (typeof item.filter === 'function') {
                    data = await item.filter(data, item);
                }
                item.data = data;
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
