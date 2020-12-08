<template>
  <q-page class="flex flex-center">

    <div class="row card">
      <q-btn outline label="4-1 为学生集合更新数据" class="btn bg-white border text-black"
             @click="onSubmit('4-1'); upload_url = '4-1'"/>
      <q-btn outline label="4-2 为教师集合更新数据" class="btn bg-white border text-black"
             @click="onSubmit('4-2'); upload_url = '4-2'"/>
      <q-btn outline label="4-3 为课程集合更新数据" class="btn bg-white border text-black"
             @click="onSubmit('4-3'); upload_url = '4-3'"/>
    </div>

    <div class="card-table">
      <q-table dense flat hide-bottom class="table"
               :data="update_data" :columns="columns" row-key="_id.counter" :pagination.sync="pagination">
        <template v-slot:top="props">
          <div class="q-table__title row q-pa-none">
            <q-item>更新数据</q-item>
          </div>
          <q-space />
          <q-btn flat outline dense icon="cloud_upload" label="上传" @click="onUpdate" class="q-ml-md"/>
        </template>
        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td key="none" :props="props" v-for="line in columns" :key="line.name"
                  :class="isFitData(props.row, line.name) ? 'normal' : 'changed'">
              {{ props.row[line.name] }}
              <q-popup-edit v-model="props.row[line.name]">
                <q-input v-model="props.row[line.name]" dense autofocus counter />
              </q-popup-edit>
            </q-td>
          </q-tr>
        </template>
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
      update_data: [],
      upload_url: null
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
        this.update_data = JSON.parse(JSON.stringify(json));
      }else{
        this.columns = [
          { name: 'none', label: '无数据', field: 'none', align: "center"}
        ];
        this.data = [{'none': '无数据'}];
        this.update_data = [{'none': '无数据'}];
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
    onUpdate(){
      let size = 0;
      for(let i = 0;i < this.update_data.length; i++){
        axios({
          method: 'get', url: '/db/' + this.upload_url + "-u", params: this.update_data[i],
          headers: {'content-type': "application/json"}, responseType: 'json'
        }).then(response => {
          console.log(response);
          this.success();
          size ++;
          if(size == this.update_data.length){
            this.onSubmit(this.upload_url)
          }
        }).catch(error => {
          console.log(error);
          this.failed();
        });
      }
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
    },
    isFitData(row, name){
      if(row._id === undefined)
        return false;
      for(let i in this.data){
        if(this.data[i]._id.counter == row._id.counter){
          return this.data[i][name] === row[name];
        }
      }
      return false;
    }
  }
}
</script>

<style scoped>
.btn{
  width: 33.33%;
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
.normal{
  color: black;
}
.changed{
  color: red;
}
</style>
