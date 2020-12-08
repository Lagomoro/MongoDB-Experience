<template>
  <q-page class="flex flex-center">

    <div class="row card">
      <q-btn outline label="2-1 找出年龄小于20岁的所有学生" class="btn bg-white border text-black"
             @click="onSubmit('2-1');"/>
      <q-btn outline label="2-2 找出年龄小于20岁且是软件学院的学生" class="btn bg-white border text-black"
             @click="onSubmit('2-2');"/>
      <q-btn outline label="2-3 找出学生关系中的所有学生" class="btn bg-white border text-black"
             @click="onSubmit('2-3');"/>
      <q-btn outline label="2-4 求所有学生的姓名、年龄" class="btn bg-white border text-black"
             @click="onSubmit('2-4');"/>
      <q-btn outline label="2-5 找出年龄小于20岁的学生的姓名、性别" class="btn bg-white border text-black"
             @click="onSubmit('2-5');"/>
      <q-btn outline label="2-6 检索所有课程情况" class="btn bg-white border text-black"
             @click="onSubmit('2-6');"/>
      <q-btn outline label="2-7 检索先行课号为“300001”的课程名" class="btn bg-white border text-black"
             @click="onSubmit('2-7');"/>
      <q-btn outline label="2-8 找出年龄大于50岁的老师" class="btn bg-white border text-black"
             @click="onSubmit('2-8');"/>
      <q-btn outline label="2-9 找出所有的男老师" class="btn bg-white border text-black"
             @click="onSubmit('2-9');"/>
      <q-btn outline label="2-10 找出所有在CS学院的老师" class="btn bg-white border text-black"
             @click="onSubmit('2-10');"/>
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
