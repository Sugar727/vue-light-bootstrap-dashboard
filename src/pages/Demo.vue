<template>
  <div>
    <input type="file" ref="fileInput" @change="handleFileChange" />
    <button @click="uploadExcel">Upload Excel</button>

    <!-- 表格显示解析后的数据 -->
    <table class="table">
      <thead>
      <tr>
        <th v-for="(header, index) in headers" :key="index">{{ header }}</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(row, rowIndex) in jsonData" :key="rowIndex">
        <td v-for="(value, colIndex) in row" :key="colIndex">{{ value }}</td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import * as XLSX from 'xlsx';

export default {
  data() {
    return {
      jsonData: [],
      headers: [],
    };
  },
  methods: {
    handleFileChange() {
      // 处理文件变更事件
      const fileInput = this.$refs.fileInput;
      const file = fileInput.files[0];

      if (file) {
        this.parseExcel(file);
      }
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

      reader.readAsArrayBuffer(file);
    },
    uploadExcel() {
      // 在这里处理上传逻辑，例如将数据发送到服务器
      console.log('Uploading Excel Data:', this.jsonData);
      console.log('Uploading Excel Data:', this.headers);
    },
  },
};
</script>

<style>
/* 样式可以根据需要进行调整 */
</style>

