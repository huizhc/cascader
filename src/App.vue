<template>
  <div id="app">
    {{text}}
    <cascader :data="data" @on-change="handleChange" v-model="value"></cascader>
  </div>
</template>

<script>
import cascader from './components/cascader';
export default {
  components: {
    cascader
  },
  data () {
    return {
      text: '未选择',
      value: ['jiangsu', 'suzhou', 'zhuozhengyuan'],
      data: [
        {
          value: 'beijing',
          label: '北京',
          children: [
            {
              value: 'gugong',
              label: '故宫'
            },
            {
              value: 'tiantan',
              label: '天坛'
            }
          ]
        },
        {
          value: 'jiangsu',
          label: '江苏',
          children: [
            {
              value: 'suzhou',
              label: '苏州',
              children: [
                {
                  value: 'zhuozhengyuan',
                  label: '拙政园'
                },
                {
                  value: 'shiziling',
                  label: '狮子林'
                },
              ]
            },
            {
              value: 'nanjing',
              label: '南京'
            }
          ]
        },
        
      ],
    }
  },
  methods: {
    handleChange (val, selectedData) {
      this.text = selectedData.map(o => o.label).join(', ');
    }
  }
}
</script>

<style>
ul {
  list-style: none;
  border: 1px solid black;
}
li {
  border: 1px solid red;
}
</style>