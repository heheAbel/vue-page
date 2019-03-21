<template>
  <div class="eble-page">
    <span
      v-if="firstLastPage"
      :class="{'forbid-page': 1 === chooseNumber}"
      @click="changeNumber(1)"
    >&lt;&lt;</span>
    <span
      @click="addSubtractFun(true)"
      v-if="addSubtractPage"
      :class="{'forbid-page': 1 === chooseNumber}"
    >&lt;</span>
    <Less
      v-if="pagesNumber <= 10"
      :pagesArr="pagesArr"
      :chooseNumber="chooseNumber"
      @changeNumber="changeNumber"
    />
    <Greater
      v-if="pagesNumber > 10"
      :pagesArr="pagesArr"
      :chooseNumber="chooseNumber"
      :pagesNumber="pagesNumber"
      @changeNumber="changeNumber"
    />
    <span
      @click="addSubtractFun(false)"
      v-if="addSubtractPage"
      :class="{'forbid-page': pagesNumber === chooseNumber}"
    >&gt;</span>
    <span
      v-if="firstLastPage"
      :class="{'forbid-page': pagesNumber === chooseNumber}"
      @click="changeNumber(pagesNumber)"
    >&gt;&gt;</span>
    <p class="jump-page">
      跳转
      <input type="text" v-model="jumpNumber">
      页
      <button @click="jumpNumberFun">Go</button>
    </p>
  </div>
</template>

<script>
import Less from "./less";
import Greater from "./greater";
export default {
  name: "Pager",
  components: { Less, Greater },
  props: {
    pagesNumber: {
      type: Number,
      required: true
    },
    jumpPage: {
      type: Boolean,
      default: false
    },
    firstLastPage: {
      type: Boolean,
      default: true
    },
    addSubtractPage: {
      type: Boolean,
      default: true
    },
    performFun: {
      type: Function,
      // 对象或数组默认值必须从一个工厂函数获取
      default: number => {
        return number;
      }
    }
  },
  data() {
    return {
      pagesArr: [],
      chooseNumber: 1,
      jumpNumber: ""
    };
  },
  created() {
    const temporaryArr = [];
    for (let i = 1; i <= this.pagesNumber; i++) {
      temporaryArr.push(i);
    }
    this.pagesArr = temporaryArr;
  },
  methods: {
    callbackFun() {
      if (this.performFun) {
        this.$emit("performFun", this.chooseNumber);
      }
    },
    changeNumber(chooseNumber) {
      this.chooseNumber = chooseNumber;
      this.callbackFun();
    },
    addSubtractFun(boolean) {
      if (boolean && this.chooseNumber > 1) {
        this.chooseNumber = this.chooseNumber - 1;
      } else if (!boolean && this.chooseNumber < this.pagesNumber) {
        this.chooseNumber = this.chooseNumber + 1;
      }
      this.callbackFun();
    },
    jumpNumberFun() {
      if (
        !isNaN(parseInt(this.jumpNumber, 10)) &&
        this.jumpNumber >= 1 &&
        this.jumpNumber <= this.pagesNumber
      ) {
        this.chooseNumber = parseInt(this.jumpNumber, 10);
        this.callbackFun();
      }
    }
  },
  watch: {
    jumpNumber: function(newData) {
      const temporaryNumber = parseInt(newData, 10);
      if (isNaN(temporaryNumber)) {
        this.jumpNumber = "";
      } else {
        this.jumpNumber = temporaryNumber;
      }
    }
  }
};
</script>

<style scoped>
.eble-page {
  margin: 0 auto;
  text-align: center;
}
.eble-page div,
.eble-page ul {
  padding: 0;
  margin: 0;
}
.eble-page *[role="button"],
input,
button {
  outline: none;
}
.eble-page-ul {
  display: inline-block;
}
.eble-page span {
  display: inline-block;
  width: 30px;
  height: 30px;
  line-height: 30px;
  text-align: center;
  border-radius: 5px;
  border: 1px solid #ddd;
  margin-right: 5px;
  cursor: pointer;
  user-select: none;
}
.eble-page span:last-child {
  margin-right: 0;
}
.eble-page span:hover {
  background-color: #f00;
  color: #fff;
}
.forbid-page,
.forbid-page:hover {
  cursor: default;
  background: #aaa;
  color: #fff;
  pointer-events: none;
}
.jump-page {
  display: inline-block;
  margin-left: 5px;
}
.jump-page input {
  width: 30px;
  border-radius: 3px;
  border: 1px solid #ddd;
  text-align: center;
  height: 20px;
  line-height: 20px;
}
.jump-page button {
  cursor: pointer;
  display: inline-block;
  margin-left: 5px;
  border: none;
  background: #f00;
  width: 45px;
  height: 25px;
  line-height: 25px;
  border-radius: 3px;
  color: #fff;
}
</style>
