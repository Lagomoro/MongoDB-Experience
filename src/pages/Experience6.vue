<template>
  <q-page class="flex flex-center">
    <img alt="Quasar logo" src="~assets/quasar-logo-full.svg">
    <el-form :model="form">
      <el-form-item label="上传Excel" :label-width="formLabelWidth">
        <el-upload ref="input" action="/" :show-file-list="false" :auto-upload="false" :on-change="importXlsx" type="file">
          <el-button size="small" plain>选择文件</el-button>
        </el-upload>
        <el-button size="small" type="primary" @click="uploadFile">立即上传</el-button>
      </el-form-item>
    </el-form>

  </q-page>
</template>

<script>
import XLSX from 'xlsx'
export default {
  name: 'PageIndex',
  data () {
    return {

    }
  },
  methods:{
    importXlsx(file){
      this.readXlsx(file).then(tabJson => {
        console.log(tabJson);
      });
      /*alert()
      let workbook = xlsx.read("F:/SynologyDrive/学习/非关系数据库/course.xlsx",{cellStyles:true});
      let worksheet = workbook.Sheets[workbook.SheetNames[0]];
      let linenumber = 2;
      while(!this.isXlsxEnd(worksheet, linenumber)){
        console.log({
          "id":this.readXlsxCellValue(worksheet,'A'+linenumber,'string'),
          "name":this.readXlsxCellValue(worksheet,'B'+linenumber,'string'),
          "display":this.readXlsxCellValue(worksheet,'C'+linenumber,'string')
        })
        linenumber ++;
      }*/
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
