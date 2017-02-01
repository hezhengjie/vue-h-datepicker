<template>
  <input v-model="date" >
  <div @click="showPicker">选择日期</div>
  <div class="wrap">
    <app-date-picker :status="status" :start-date="startDate" :year-scope="yearScope" v-on:confirm="getDate" v-on:cancel="cancelPicker"></app-date-picker>
  </div>
 </template>
<style>
  html{
    font-size: 10px;
  }
</style>
<script>
  import DatePicker from '../src/DatePicker.vue';

export default {
  data(){
    return {
      status:false,
      startDate:{
        year:2001,
        month:11,
        day:12
      },
      yearScope:[1991,2018],//可选,默认为今年前后20年
      date:'2012-12-12'
    }
  },
  components: {
    'app-date-picker': DatePicker
  },
  methods:{
    showPicker:function(){
      this.status = true;
    },
    getDate: function(date){
      this.date = date.year+'-'+date.month+'-'+date.day;//选择之后的回调函数
    },
    cancelPicker:function(){
      this.status = false;//取消之后的回调函数
    }
  }
}
</script>
