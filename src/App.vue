<template>
    <div id="app">
        <Input type="text" v-model.number="a" style="width: 150px"/>
        <Input type="text" v-model.number="b" style="width: 150px"/>
        <div></div>
        <iview-cascade-select :selects="selects" v-model="app"></iview-cascade-select>
        <div>{{ app }}</div>
    </div>
</template>

<script>
import IviewCascadeSelect from './components';

export default {
    name: 'app',
    components: {
        IviewCascadeSelect
    },
    data () {
        return {
            a: 1,
            b: null,
            selects: [{
                style: {
                    width: '150px'
                },
                label: 'Demo',
                all: false,
                defaultValue: 1,
                options: async p => {
                    return [{
                        id: 1,
                        name: 'A'
                    }];
                }
            }, {
                style: {
                    width: '150px'
                },
                options: async p => {
                    return [{
                        id: 1,
                        name: 'B1'
                    }, {
                        id: 2,
                        name: 'B2'
                    }, {
                        id: 3,
                        name: 'B3'
                    }];
                },
                filter: (data, item) => {
                    return data.filter(d => d.id > 1);
                }
            }]
        };
    },
    computed: {
        app: {
            get () {
                return [this.a, this.b];
            },
            set (val) {
                this.a = val[0];
                this.b = val[1];
            }
        }
    }
};
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
