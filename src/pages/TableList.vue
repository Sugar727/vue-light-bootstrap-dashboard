<template>
  <div class="content">
    <div class="container-fluid">
      <div class="row">
        <div class="col-12">
          <card class="strpied-tabled-with-hover"
                body-classes="table-full-width table-responsive">
            <template slot="header">
              <el-upload
                action="/your-upload-api"
                :before-upload="beforeUpload"
                drag
                list-type="text">
                <i class="el-icon-upload"></i>
                <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
              </el-upload>
            </template>
            <l-table class="table-hover table-striped table-sm"
                     :columns="headers"
                     :data="jsonData">
            </l-table>
          </card>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  import LTable from 'src/components/Table.vue'
  import Card from 'src/components/Cards/Card.vue'
  import * as XLSX from 'xlsx';
  export default {
    components: {
      LTable,
      Card
    },
    data () {
      return {
        jsonData: [],
        headers: [],
      }
    },
    methods: {
      beforeUpload(file) {
        // 在这里可以添加一些文件上传前的逻辑，如果返回 false 则取消上传
        this.parseExcel(file);
        return true;
      },
      parseExcel(file) {
        const reader = new FileReader();

        reader.onload = (e) => {
          const data = new Uint8Array(e.target.result);
          const workbook = XLSX.read(data, { type: 'array' });

          // 选择第一个工作表
          const sheetName = workbook.SheetNames[0];
          const sheet = workbook.Sheets[sheetName];

          // 将 Excel 转换为 JSON
          this.jsonData = XLSX.utils.sheet_to_json(sheet, { header: 1 });
          // 提取表头
          if (this.jsonData.length > 0) {
            this.headers = this.jsonData[0];
          }
        };
        console.log(this.jsonData);
        reader.readAsArrayBuffer(file);
      },
      uploadExcel() {
        // 在这里处理上传逻辑，例如将数据发送到服务器

      },
    },
  }
</script>
<style>
.el-upload{
  width: 100%;
}
.el-upload-dragger{
  width: 100%;
  height: 100px;
}
.el-upload-dragger .el-icon-upload{
  margin: 4px 0 16px;
}

</style>
