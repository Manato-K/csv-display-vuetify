<template>
  <v-container>
    <v-file-input
      label="Upload CSV"
      accept=".csv"
			v-model="fileData"
      @change="handleFileUpload"
    ></v-file-input>
    <v-data-table
      v-if="headers.length > 0"
      :headers="headers"
      :items="pagedRows"
      :items-per-page="20"
      class="elevation-1"
      v-model:page="page"
      :footer-props="{ showFirstLastPage: true }"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>CSV Data Table</v-toolbar-title>
          <v-spacer></v-spacer>
        </v-toolbar>
      </template>
    </v-data-table>
  </v-container>
</template>

<script setup>
import { ref, computed } from "vue";
import Papa from "papaparse";

const fileData = ref() // ファイルデータ格納用鵜
const rows = ref([]); // CSVデータ全体
const headers = ref([]); // ヘッダー
const page = ref(1); // 現在のページ番号

// ファイルアップロード時の処理
const handleFileUpload = () => {
	console.log(fileData.value)
	Papa.parse(fileData.value, {
    header: true,
    skipEmptyLines: true,
    complete: (result) => {
      headers.value = Object.keys(result.data[0]).map((key) => ({
        text: key,
        value: key,
      }));
      rows.value = result.data;
    },
  });
};

// 現在のページのデータを取得
const pagedRows = computed(() => {
  const start = (page.value - 1) * 20;
  const end = page.value * 20;
  return rows.value.slice(start, end);
});
</script>
