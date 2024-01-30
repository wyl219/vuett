

<template>
  <div class="calculator" >
    <!-- div1 -->
    <div class='wide_div'>

      <div class="div1">

        <div>
          <label for="input1"  class="label-fixed-width">{{ formatObject(labelNames[0]) }}:</label>
          <input ref="input1Ref" type="text" v-model="input1" @blur="handleNumericInput('input1')" @keyup.enter="handleEnterKey('input2')" />

        </div>
        <div>
          <label for="input2"  class="label-fixed-width">{{ formatObject(labelNames[1]) }}:</label>
          <input ref="input2Ref" type="text" v-model="input2" @blur="handleNumericInput('input2')" @keyup.enter="handleEnterKey('input3')" />
        </div>
        <div>
          <label for="input3"  class="label-fixed-width">{{ formatObject(labelNames[2])}}:</label>
          <input ref="input3Ref" type="text" v-model="input3" @blur="handleNumericInput('input3')" @keyup.enter="handleEnterKey('save')" />
        </div>
        <div>
          <label for="select"  class="label-fixed-width">{{ formatObject(labelNames[3]) }}:</label>
          <select ref="selectRef" v-model="selectedOption"  class="short-select" @change="change_sel">
            <option v-for="(option,index) in options" :key="index" :value="Object.values(option)[0]">{{ Object.keys(option)[0] }}</option>
          </select>
        </div>
        <div>
          <label for="result"  class="label-fixed-width">{{ formatObject(labelNames[4])}}:</label>
          <input ref="resultRef" type="text" v-model="result" readonly />
        </div>
        <!-- 新增复选框 -->
        <div class="external-left-align">
          <label for="reverseMode">
            <input class="narrow-input" type="checkbox" id="reverseMode" v-model="reverseMode" >
            求电机功率
          </label>
        </div>



      </div>

      <!-- div2 -->
      <!--     <div class="div2" v-show="! reverseMode">-->
      <div class="div2" >
        <div class="header">
          <span v-for="(label , index ) in labelNames" :key="label" @click="sortData(labelToFieldMap[index])">{{  Object.keys(label)[0] }}</span>
          <span @click="clearData" style="text-decoration: underline; cursor: pointer;">Clear</span>
        </div>
        <div class="data">
          <!--         <div class="data" @mouseover="highlightRow(index)" @mouseleave="highlightRow(null)">-->
          <div v-for="(row, rowIndex) in currentData" :key="rowIndex" class="data-row" @click="loadData(rowIndex)">
            <span v-for="label in row" :key="label">{{ label }}</span>
            <span @click="deleteRow(rowIndex)" style="text-decoration: underline; cursor: pointer;">Delete</span>
          </div>
        </div>

      </div>

    </div>

    <!-- div3 -->
    <div class="div3">
      <!--      {{div3_info}}-->
      这是一个计算器，可以计算风机的轴功率或电机功率。
      <br>
      普通模式下,输入计算参数，即可计算轴功率。在前两个输入框内回车会自动跳转到下一个输入框,在数量输入框内回车会自动保存数据。
      <br>
      在右侧表格内点击一行数据，可以将数据载入输入框内，点击Delete可以删除数据。点击上面的clear可以清空表格。
      <br>

    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      module_name:"fan_power_cal",
      input1: "",
      input2: "",
      input3: "",
      reverseMode:false,
      selectedOption: null,
      data: [],
      data2: [], // 用于保存反推模式下的数据
      // data3: [], // 用于在div2中显示的数据
      options: [
        {"风量大于5万 76%": 76},
        {"风量大于3万 71%": 71},
        {"风量小于2万 67%": 67}
      ],
      // highlightedRow: null,
      labelNames1: [{"风量":"m³/h"}, {"风压":"Pa"}, {"效率":"%"}, {"预设类型":""}, {"轴功率":"KW"}],
      labelNames2: [{"风量":"m³/h"}, {"风压":"Pa"}, {"效率":"%"}, {"预设类型":""}, {"电机功率":"KW"}],
      labelToFieldMap: ["input1", "input2", "input3", "selectedOption", "result"],
      // div3_info:`这是一个计算器，可以计算空调的冷量。
      // 输入面积、冷指标、数量，即可计算单台空调的冷量。`
    };
  },
  created() {
    // 在 created 钩子中设置 selectedOption 的初始值为 options 数组的第一个选项
    this.selectedOption = Object.values(this.options[0])[0];
    this.input3=this.selectedOption
    // this.selectedOption = null;
    const savedData = localStorage.getItem(this.module_name);
    if (savedData) {
      this.data = JSON.parse(savedData);
    }
    // this.data3=this.data;
    this.reverseMode=false;
  },

  updated() {
    const jsonData = JSON.stringify(this.data);
    localStorage.setItem(this.module_name, jsonData);
  },
  computed: {
    labelNames(){
      return this.reverseMode ? this.labelNames2 : this.labelNames1;
    },
    currentData() {
      return this.reverseMode ? this.data2 : this.data;
    },
    result() {
      const num1 = parseFloat(this.input1) || 0;
      const num2 = parseFloat(this.input2) || 0;
      const num3 = 0.01* parseFloat(this.input3) || 1;
      const shaft_power=(num1 * num2 / num3 /3600/1000)

      if (this.reverseMode) {
        // 求电机功率

        return this.get_power(shaft_power).toFixed(2);
      } else {
        return shaft_power.toFixed(2);
      }

    }
  },
  methods: {
    get_power(power) {
      const powerArray =[0.75, 1.1, 1.5, 2.2, 3.7, 5.5, 7.5, 11, 15, 18.5, 22, 30, 37, 45, 55, 75,
        90, 110, 132, 150, 160, 185, 200, 220, 250];

      if(power < 0.55){
        power=power*1.4
      }else if(power < 2.2){
        power=power*1.3
      }else if(power < 7.5){
        power=power*1.2
      }else {
        power=power*1.15
      }

      for (let i = 0; i < powerArray.length; i++) {
        if (powerArray[i] >= power) {
          return powerArray[i];
        }
      }

      return powerArray[powerArray.length - 1];
    },
    formatObject(obj) {
      const key = Object.keys(obj)[0];
      const value = obj[key];
      return `${key} ${value}`;
    },

    sortData(field) {

      // console.log(field)
      // 判断是否要升序还是降序
      const ascending = this.sortField === field ? !this.sortAscending : true;


      // 更新排序字段和排序顺序
      this.sortField = field;
      this.sortAscending = ascending;

      // 对数据进行排序
      this.data.sort((a, b) => {
        // 根据排序字段进行比较



        const valueA = a[field];
        const valueB = b[field];

        if (ascending) {
          return valueA > valueB ? 1 : valueA < valueB ? -1 : 0;
        } else {
          return valueB > valueA ? 1 : valueB < valueA ? -1 : 0;
        }
      });
    },
    // handleReverseModeChange() {
    //   // 如果选择了反推模式，修改标签名称
    //   if (this.reverseMode) {
    //     this.labelNames[0] = "边长";
    //     this.labelNames[2] = "单台冷量";
    //     this.labelNames[4] = "另一边长";
    //     // this.data3=this.data2;
    //   } else {
    //     this.labelNames[0] = "面积";
    //     this.labelNames[2] = "数量";
    //     this.labelNames[4] = "单台冷量";
    //     // this.data3=this.data;
    //   }
    // },

    change_sel() {
      this.input3 = this.selectedOption;
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
      // if(this.reverseMode){
      //
      //   return;
      // }
      //
      // // 获取选中选项的文本
      // // console.log(this.selectedOption)
      // const selectedOptionText = Object.keys(this.options.find(option => Object.values(option)[0] === this.selectedOption))[0];
      // // console.log(selectedOptionText)
      // this.data.push({
      //   input1: this.input1,
      //   input2: this.input2,
      //   input3: this.input3,
      //   selectedOption: selectedOptionText,
      //   result: this.result
      // });
      // this.clearInputs();
      const selectedOptionText = Object.keys(this.options.find(option => Object.values(option)[0] === this.selectedOption))[0];
      if (this.reverseMode) {
        // console.log('反推')
        // 如果是反推模式，保存到 data2
        this.data2.push({
          input1: this.input1,
          input2: this.input2,
          input3: this.input3,
          selectedOption: selectedOptionText,
          result: this.result,
        });
      } else {
        // console.log('正常')
        // 否则保存到 data
        this.data.push({
          input1: this.input1,
          input2: this.input2,
          input3: this.input3,
          selectedOption: selectedOptionText,
          result: this.result,
        });
      }
      this.clearInputs();
    },

    // highlightRow(index) {
    //   this.highlightedRow = index;
    // },
    loadData(index) {
      if(this.reverseMode){

        return;
      }
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
.external-left-align label {
  text-align: left;
  display: flex;
  align-items: center;
}
.narrow-input {
  width: 20px; /* 或者设置为合适的宽度 */
}
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
  white-space: normal;
  width: 800px;
  height: 300px;
  //background-color: rgba(0, 0, 0, 0);
}

.label-fixed-width {
  display: inline-block; /* 将 display 设置为 inline-block */
  width: 100px; /* 设置固定宽度，根据需要进行调整 */
}

.short-select,
input {
  //flex: 1;
  width: calc(100% - 100px)
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
