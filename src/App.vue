<template>
    <div id="app">
        <iview-cascade-select :selects="selects" v-model="app"></iview-cascade-select>
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
            app: null,
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
