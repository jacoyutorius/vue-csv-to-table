<template>
  <div id="csv-to-table">
    <form action>
      <div>
        <label>Header Row count</label>
        <input type="number" v-model="headerRowCount" />
      </div>
      <div>
        <input type="file" @change="uploaded" />
      </div>
    </form>

    <section>
      <table>
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