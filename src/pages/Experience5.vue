<template>
  <q-page class="flex flex-center">

    <div class="row card">
      <q-input filled v-model="sid" fill-input label="学号" class="input"/>
      <q-btn outline label="5-1 查询学生选课列表" class="btn bg-white border text-black"
             @click="onSubmit('5-1'); upload_url = '5-1'"/>
    </div>

    <div class="card-table">
      <q-table dense flat hide-bottom class="table"
               :data="update_data" :columns="columns" row-key="_id.counter" :pagination.sync="pagination">
        <template v-slot:top="props">
          <div class="q-table__title row q-pa-none">
            <q-item>更新数据</q-item>
          </div>
          <q-space/>
          <q-select filled v-model="course" clearable use-input hide-selected fill-input input-debounce="0" type="text" class="q-mx-sm"
                    label="选择课程" :options="course_opts" @filter="filterFn" @filter-abort="abortFilterFn" style="width: 200px">
            <template v-slot:no-option>
              <q-item>
                <q-item-section class="text-grey">
                  No results
                </q-item-section>
              </q-item>
            </template>
          </q-select>
          <q-btn flat outline dense icon="add" label="新增选课" @click="onAdd" class="q-ml-md"/>
          <q-btn flat outline dense icon="remove" label="删除选课" @click="onAdd" class="q-ml-md"/>
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
      sid: null,
      update_data: [],
      upload_url: null,

      course: null,
      course_info: null,
      course_opts: null,
      course_opts_tmp: null,
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
        method: 'get', url: '/db/' + url, params: {sid : this.sid},
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
    onAdd() {
      axios({
        method: 'get', url: '/db/' + this.upload_url + "-i",
        params: {
          sid : this.sid,
          cid : this.course.split(" - ")[0]
        },
        headers: {'content-type': "application/json"}, responseType: 'json'
      }).then(response => {
        console.log(response);
        this.success();
        this.onSubmit(this.upload_url)
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
    },
    filterFn (val, update, abort) {
      if (this.course_opts_tmp !== null) {
        update(() => {
          const needle = val.toLowerCase()
          this.course_opts = this.course_opts_tmp.filter(v => v.toLowerCase().indexOf(needle) > -1)
        }, ref => {
          if (val !== '' && ref.options.length > 0 && ref.optionIndex === -1) {
            ref.moveOptionSelection(1, true) // focus the first selectable option and do not update the input-value
            ref.toggleOption(ref.options[ref.optionIndex], true) // toggle the focused option
          }
        });
        return;
      }
      axios({
        method: 'get', url: '/db/5-1-c', params: {},
        headers: {'content-type': "application/json"}, responseType: 'json'
      }).then(response => {
        console.log(response.data)
        this.course_info = response.data;
        let list = [];
        for(let i in this.course_info)
          list.push(this.course_info[i].cid + " - " + this.course_info[i].name);
        update(() => {
          this.course_opts_tmp = list;
          const needle = val.toLowerCase();
          this.course_opts = this.course_opts_tmp.filter(v => v.toLowerCase().indexOf(needle) > -1);
        });
      }).catch(error => {
        abort();
        console.log(error);
      });
    },
    abortFilterFn () {
      // console.log('delayed filter aborted')
    }
  }
}
</script>

<style scoped>
.input{
  width: 50%;
}
.btn{
  width: 50%;
  height: 56px;
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
