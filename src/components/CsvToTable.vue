<template>
  <div id="csv-to-table">
    <section>
      <form action>
        <div class="field is-horizontal">
          <div class="field-label is-normal">
            <label class="label">Header Row count</label>
          </div>
          <div class="field-body">
            <div class="field">
              <div class="control">
                <input id="headerRowCount" class="input" type="number" v-model="headerRowCount" />
              </div>
            </div>
          </div>
        </div>

        <div class="field is-horizontal">
          <div class="field-label is-normal">
            <label class="label">CSV file</label>
          </div>
          <div class="field-body">
            <div class="file has-name is-fullwidth">
              <label class="file-label">
                <input class="file-input" type="file" @change="uploaded" />
                <span class="file-cta">
                  <span class="file-icon">
                    <i class="fas fa-upload"></i>
                  </span>
                  <span class="file-label">Choose a file…</span>
                </span>
                <span class="file-name">{{ fileName }}</span>
              </label>
            </div>
          </div>
        </div>
      </form>
    </section>

    <section class="table-container">
      <table class="table is-bordered is-hoverable is-fullwidth">
        <thead>
          <tr v-for="(row, rowIndex) in csvHeader" v-bind:key="rowIndex">
            <td v-if="showCheckbox && rowIndex === 0">
              <input type="checkbox" />
            </td>
            <th v-for="(data, colIndex) in row" v-bind:key="colIndex">{{ data }}</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(row, rowIndex) in csvData" v-bind:key="rowIndex">
            <td v-if="showCheckbox">
              <input type="checkbox" @change="onCheckBoxCheck(row)" />
            </td>
            <td v-for="(data, colIndex) in row" v-bind:key="colIndex">{{ data }}</td>
          </tr>
        </tbody>
      </table>
    </section>
  </div>
</template>

<script>
import Encoding from "encoding-japanese";

export default {
  name: "CsvToTable",
  props: {
    showCheckbox: {
      type: Boolean,
      default: true,
      required: false
    }
  },
  data() {
    return {
      fileName: "",
      headerRowCount: 2, // default 0
      csvHeader: [],
      csvData: []
    };
  },
  methods: {
    onCheckBoxCheck: function(row) {
      console.log(row);
    },
    uploaded: function(e) {
      const file = e.target && e.target.files && e.target.files[0];

      if (file) {
        this.fileName = file.name;

        this.loadCsv(file, sjisData => {
          const lineArray = sjisData.split("\r");
          const itemArray = [];

          for (let i = 0; i < lineArray.length; i++) {
            itemArray[i] = lineArray[i].split(",");
          }

          if (this.headerRowCount > 0) {
            this.csvHeader = itemArray.slice(0, this.headerRowCount);
            this.csvData = itemArray.slice(
              this.headerRowCount,
              itemArray.length - 1
            );
          } else {
            this.csvData = itemArray;
          }
        });
      }
    },
    loadCsv: function(file, callBack) {
      const reader = new FileReader();
      reader.onerror = function() {
        alert(`${file}の読み込みに失敗しました。`);
      };
      reader.onload = () => {
        const str = String.fromCharCode.apply(
          "",
          new Uint8Array(reader.result)
        );

        const sjisData = Encoding.convert(str, {
          to: "UNICODE",
          from: "AUTO"
        });

        callBack(sjisData);
      };

      reader.readAsArrayBuffer(file);
    }
  }
};
</script>

<style scoped>
section {
  margin: 1em;
}

input#headerRowCount {
  width: 4em;
}

table {
  table-layout: auto;
}
</style>