<template>
  <input v-model="date" >
  <div @click="showPicker">选择日期</div>
  <app-date-picker :status="startDate.status" :year-value="startDate.year"
                   :month-value="startDate.month" :day-value="startDate.day" :year-scope="startDate.yearScope"></app-date-picker>
</template>

<script>
  import DatePicker from '../src/DatePicker.vue';

export default {
  data(){
    return {
      startDate:{
        status:false,
        year:2001,
        month:11,
        day:12,
        yearScope:[1991,2018]//可选,默认为今年前后20年
      },
      date:'2012-12-12'
    }
  },
  components: {
    'app-date-picker': DatePicker
  },
  events:{
    getDate: function(date){
      this.date = date.year+'-'+date.month+'-'+date.day;//选择之后的回调函数
    },
    cancelPicker:function(){
      this.startDate.status = false;//取消之后的回调函数
    }
  },
  methods:{
    showPicker:function(){
      this.startDate.status = true;
    }
  }
}
</script>
