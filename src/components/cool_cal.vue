

<template>
  <div class="calculator" >
    <!-- div1 -->
   <div class='wide_div'>

     <div class="div1">

       <div>
         <label for="input1"  class="label-fixed-width">{{ labelNames[0] }}:</label>
         <input ref="input1Ref" type="text" v-model="input1" @blur="handleNumericInput('input1')" @keyup.enter="handleEnterKey('input2')" />
       </div>
       <div>
         <label for="input2"  class="label-fixed-width">{{ labelNames[1] }}:</label>
         <input ref="input2Ref" type="text" v-model="input2" @blur="handleNumericInput('input2')" @keyup.enter="handleEnterKey('input3')" />
       </div>
       <div>
         <label for="input3"  class="label-fixed-width">{{ labelNames[2] }}:</label>
         <input ref="input3Ref" type="text" v-model="input3" @blur="handleNumericInput('input3')" @keyup.enter="handleEnterKey('save')" />
       </div>
       <div>
         <label for="select"  class="label-fixed-width">{{ labelNames[3] }}:</label>
         <select ref="selectRef" v-model="selectedOption"  class="short-select" @change="change_sel">
           <option v-for="(option,index) in options" :key="index" :value="Object.values(option)[0]">{{ Object.keys(option)[0] }}</option>
         </select>
       </div>
       <div>
         <label for="result"  class="label-fixed-width">{{ labelNames[4] }}:</label>
         <input ref="resultRef" type="text" v-model="result" readonly />
       </div>
     </div>

     <!-- div2 -->
     <div class="div2">
       <div class="header">
         <span v-for="label in labelNames" :key="label" @click="sortData(`input${label.charAt(label.length - 1)}`)">{{ label }}</span>
         <span @click="clearData" style="text-decoration: underline; cursor: pointer;">Clear</span>
       </div>
       <div class="data" @mouseover="highlightRow(index)" @mouseleave="highlightRow(null)">
         <div v-for="(row, rowIndex) in data" :key="rowIndex" class="data-row" @click="loadData(rowIndex)">
           <span v-for="label in row" :key="label">{{ label }}</span>
           <span @click="deleteRow(rowIndex)" style="text-decoration: underline; cursor: pointer;">Delete</span>
         </div>
       </div>

     </div>

   </div>

    <!-- div3 -->
    <div class="div3">
      <p>{{div3_info}}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      module_name:"cool_cal",
      input1: "",
      input2: "",
      input3: "",
      selectedOption: null,
      data: [],
      options: [
        {"办公室": 200},
        {"病房、旅馆": 230},
        {"会议室": 350}
      ],
      highlightedRow: null,
      labelNames: ["面积", "冷指标", "数量", "预设类型", "单台冷量"],
      div3_info:"这是一个计算器，可以计算空调的冷量。\n输入面积、冷指标、数量，即可计算单台空调的冷量。"
    };
  },
  created() {
    // 在 created 钩子中设置 selectedOption 的初始值为 options 数组的第一个选项
    this.selectedOption = Object.values(this.options[0])[0];
    const savedData = localStorage.getItem(this.module_name);
    if (savedData) {
      this.data = JSON.parse(savedData);
    }
  },

  updated() {
    const jsonData = JSON.stringify(this.data);
    localStorage.setItem(this.module_name, jsonData);
  },
  computed: {
    result() {
      const num1 = parseFloat(this.input1) || 0;
      const num2 = parseFloat(this.input2) || 0;
      const num3 = parseFloat(this.input3) || 1;

      // 将 input1、input2、input3 相加，并保留两位小数
      return (num1 * num2 / num3).toFixed(2);
    }
  },
  methods: {
    change_sel() {
      this.input2 = this.selectedOption;
    },
    handleNumericInput(inputField) {

      let inputValue = this[inputField];

      // 仅保留数字、简单的四则混合运算符号和小括号
      inputValue = inputValue.replace(/[^0-9+\-*/().]/g, '');

      try {
        // 使用 eval 进行简单的四则混合运算
        const result = eval(inputValue);

        // 如果运算成功，更新输入框的值
        if (!isNaN(result)) {
          this[inputField] = result.toString();
        }
      } catch (error) {
        // 忽略运算错误，保留原始输入值
      }
    },
    handleEnterKey(nextInput) {
      if (nextInput === 'save') {
        this.saveData();
      } else {
        this.$refs[nextInput + 'Ref'].focus();
      }
    },
    saveData() {
      // 获取选中选项的文本
      // console.log(this.selectedOption)
      const selectedOptionText = Object.keys(this.options.find(option => Object.values(option)[0] === this.selectedOption))[0];
      // console.log(selectedOptionText)
      this.data.push({
        input1: this.input1,
        input2: this.input2,
        input3: this.input3,
        selectedOption: selectedOptionText,
        result: this.result
      });
      this.clearInputs();
    },
    highlightRow(index) {
      this.highlightedRow = index;
    },
    loadData(index) {
      const selectedData = this.data[index];
      this.input1 = selectedData.input1;
      this.input2 = selectedData.input2;
      this.input3 = selectedData.input3;
      // 根据选项的文本找到对应的选项对象
      console.log(selectedData.selectedOption)
      this.selectedOption = Object.values(this.options.find(option => Object.keys(option)[0] === selectedData.selectedOption))[0];
      // Assuming you have a function to calculate result based on loaded data
      // this.result = this.calculateResult();
    },
    deleteRow(index) {
      this.data.splice(index, 1);
    },
    clearData() {
      this.data = [];
    },
    clearInputs() {
      this.input1 = "";
      this.input2 = "";
      this.input3 = "";

    }
  }
};
</script>


<style scoped>
.wide_div{
  width: 800px;
  display: flex;
  border: 1px solid black;
  border-radius: 20px; /* 可以根据需要调整圆角的大小 */

}
.calculator {
  display: flex;
  flex-wrap: wrap;
  max-width: 800px;
}

.div1, .div2, .div3 {
  box-sizing: border-box;
  padding: 10px;
}

.div1 {
  width: 33%;
  height: 300px;
  overflow-y: auto;
}

.div2 {
  width: 66%;
  overflow-y: auto;
  height: 300px;
}

.div3 {
  width: 800px;
  height: 300px;
  //background-color: rgba(0, 0, 0, 0);
}

.label-fixed-width {
  display: inline-block; /* 将 display 设置为 inline-block */
  width: 70px; /* 设置固定宽度，根据需要进行调整 */
}

.short-select,
input {
  //flex: 1;
  width: calc(100% - 70px)
}

.header{
  display: flex;
  align-items: center;
  height: 20px;
}

.header span{
  text-align: center;
  font-weight: bold;
  height: 20px;

  box-sizing: border-box; /* 添加这一行 */

}

.header span {
  border-right: 1px solid #ccc; /* 添加这一行，设置右边框 */
  width: 90px; /* 设置固定宽度 */
}

.data span {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  width: 90px;
  box-sizing: border-box;
  display: flex;
  align-items: center; /* 居中对齐 */
  justify-content: center; /* 添加这一行 */
  border-right: 1px solid #ccc; /* 添加这一行，设置右边框 */
}

.data span div {
  flex: 1;
  padding: 0 5px; /* 调整文字左右内边距 */
}

.data {
  display: flex;
  flex-direction: column;
  overflow-y: auto;
  height: calc(100% - 40px);
}

.data-row {
  display: flex;
  align-items: center;
  width: 100%;
}

.data-row span {
  display: flex; /* 使用 flex 布局 */
  align-items: center; /* 居中对齐 */
  width: 90px;
  box-sizing: border-box;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
  justify-content: center; /* 添加这一行 */
}

.data-row span div {
  flex: 1;
  padding: 0 5px; /* 调整文字左右内边距 */
}

.data-row:hover {
  background-color: #d3d3d3;
}


</style>
