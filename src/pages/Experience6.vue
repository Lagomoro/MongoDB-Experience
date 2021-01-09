<template>
  <q-page class="flex flex-center">

    <div class="row card">
      <q-btn outline label="6-1 列出student集合中出现过的所有课程名称（distinct）" class="btn bg-white border text-black"
             @click="onSubmit('6-1');"/>
      <q-btn outline label="6-2 找出平均成绩排名前10的学生" class="btn bg-white border text-black"
             @click="onSubmit('6-2');"/>
      <q-btn outline label="6-3 找出选课数目排名前10的学生" class="btn bg-white border text-black"
             @click="onSubmit('6-3');"/>
      <q-btn outline label="6-4 找出每位同学的最高成绩以及最高成绩对应的课程名" class="btn bg-white border text-black"
             @click="onSubmit('6-4');"/>
      <q-btn outline label="6-5 求每位同学的成绩分布：优秀、良好、合格、不合格的课程门数" class="btn bg-white border text-black"
             @click="onSubmit('6-5');"/>
      <q-btn outline label="6-6 求每门课程的选修人数和平均成绩" class="btn bg-white border text-black"
             @click="onSubmit('6-6');"/>
      <q-btn outline label="6-7 求每门课程最高成绩以及最高成绩对应的学生姓名" class="btn bg-white border text-black"
             @click="onSubmit('6-7');"/>
      <q-btn outline label="6-8 求平均成绩排名前10的课程" class="btn bg-white border text-black"
             @click="onSubmit('6-8');"/>
      <q-btn outline label="6-9 求选课人数排名前10的课程" class="btn bg-white border text-black"
             @click="onSubmit('6-9');"/>
      <q-btn outline label="" class="btn bg-white border text-black"/>
    </div>

    <div class="card-table">
      <q-table dense flat hide-bottom class="table"
               :data="data" :columns="columns" row-key="_id.counter" :pagination.sync="pagination">
      </q-table>
    </div>
  </q-page>
</template>

<script>
import axios from "axios";
axios.defaults.withCredentials = true;

export default {
  name: 'PageIndex',
  data () {
    return {
      pagination: {
        page: 1,
        rowsPerPage: "all",
        rowsNumber: 50
      },
      columns: [
        { name: 'none', label: '无数据', field: 'none', align: "center"}
      ],
      data: [{'none': '无数据'}],
    }
  },
  methods:{
    interpretData(json){
      if (json.length > 0){
        this.columns = [];
        for(let i in json[0]){
          if(i !== '_id'){
            this.columns.push({ name: i, label: i, field: i, align: "center"});
          }
        }
        this.data = json;
      }else{
        this.columns = [
          { name: 'none', label: '无数据', field: 'none', align: "center"}
        ];
        this.data = [{'none': '无数据'}];
      }
    },
    onSubmit(url){
      axios({
        method: 'get', url: '/db/' + url, params: {},
        headers: {'content-type': "application/json"}, responseType: 'json'
      }).then(response => {
        console.log(response);
        this.success();
        this.interpretData(response.data)
      }).catch(error => {
        console.log(error);
        this.failed();
      });
    },
    success(){
      this.$q.notify({
        color : 'white',
        textColor : 'green-4',
        message : '操作成功！',
        icon: 'check',
        position : 'top'
      });
    },
    failed(){
      this.$q.notify({
        color : 'white',
        textColor : 'red-5',
        message : '系统错误，请稍后尝试。',
        icon: 'warning',
        position : 'top'
      });
    }
  }
}
</script>

<style scoped>
.btn{
  width: 50%;
  height: 40px;
}
.card{
  width: 100%;
  height: 30%;
  max-width: 1024px;
  padding: 2% 10%;
}
.card-table{
  width: 100%;
  max-width: 1024px;
  padding-bottom: 10%;
}
.table{
  width: 100%;
  max-width: 1024px;
}
</style>
