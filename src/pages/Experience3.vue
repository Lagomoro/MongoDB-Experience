<template>
  <q-page class="flex flex-center">

    <div class="row card">
      <q-btn outline label="3-1 为学生集合插入数据" class="btn bg-white border text-black"
             @click="onSubmit('3-1'); upload_url = '3-1'"/>
      <q-btn outline label="3-2 为教师集合插入数据" class="btn bg-white border text-black"
             @click="onSubmit('3-2'); upload_url = '3-2'"/>
      <q-btn outline label="3-3 为课程集合插入数据" class="btn bg-white border text-black"
             @click="onSubmit('3-3'); upload_url = '3-3'"/>
    </div>

    <div class="card-table-m">
      <q-table dense flat hide-bottom class="table"
               :data="data" :columns="columns" row-key="_id.counter" :pagination.sync="pagination">
      </q-table>
    </div>

    <div class="card-table">
      <q-table dense flat hide-bottom class="table"
               :data="insert_data" :columns="columns" row-key="_id.counter" :pagination.sync="pagination">
        <template v-slot:top="props">
          <div class="q-table__title row q-pa-none">
            <q-item>插入新的数据</q-item>
          </div>
          <q-space />
          <el-upload ref="input" action="/" :show-file-list="false" :auto-upload="false" :on-change="importXlsx" type="file">
            <q-btn flat outline dense icon="table_view" label="从Excel文件导入" class="q-ml-md"/>
          </el-upload>
          <q-btn flat outline dense icon="add" label="新建行" @click="addLine" class="q-ml-md"/>
          <q-btn flat outline dense icon="cloud_upload" label="上传" @click="onInsert" class="q-ml-md"/>
        </template>
        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td key="none" :props="props" v-for="line in columns" :key="line.name">
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
import XLSX from 'xlsx'

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
      insert_data: [],
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
        this.insert_data = [];
      }else{
        this.columns = [
          { name: 'none', label: '无数据', field: 'none', align: "center"}
        ];
        this.data = [{'none': '无数据'}];
        this.insert_data = [];
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
    onInsert(){
      let size = 0;
      for(let i = 0;i < this.insert_data.length; i++){
        axios({
          method: 'get', url: '/db/' + this.upload_url + "-u", params: this.insert_data[i],
          headers: {'content-type': "application/json"}, responseType: 'json'
        }).then(response => {
          console.log(response);
          this.success();
          size ++;
          if(size == this.insert_data.length){
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
    addLine(){
      let object = {};
      for(let i in this.columns){
        object[this.columns[i].name] = null;
      }
      console.log(object)
      this.insert_data.unshift(object);
    },
    importXlsx(file){
      this.readXlsx(file).then(tabJson => {
        console.log(tabJson);
        this.insert_data = tabJson[0].sheet;
      });
    },
    readXlsx(file){
      return new Promise(function(resolve, reject) {
        const reader = new FileReader();
        reader.onload = function(e) {
          const data = e.target.result;
          this.wb = XLSX.read(data, {
            type: "binary"
          });
          const result = [];
          this.wb.SheetNames.forEach(sheetName => {
            result.push({
              sheetName: sheetName,
              sheet: XLSX.utils.sheet_to_json(this.wb.Sheets[sheetName])
            });
          });
          resolve(result);
        };
        reader.readAsBinaryString(file.raw);
      });
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
.card-table-m{
  width: 100%;
  max-width: 1024px;
  padding-bottom: 2%;
}
.table{
  width: 100%;
  max-width: 1024px;
}
</style>
