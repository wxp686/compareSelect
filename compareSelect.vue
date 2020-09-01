<template>
  <div class="compare-select">
    <el-popover placement="bottom"
                trigger="click"
                v-model="popoverVisible"
                :visible-arrow="false"
                popper-class="compare-select-popper">
      <div class="content">
        <div class="left">
          <div class="greater"
               @click="switchType(1)"
               :class="type===1?'active':''">
            <span>大于</span>
            <i class="el-icon-arrow-right"></i>
          </div>
          <div class="less"
               @click="switchType(2)"
               :class="type===2?'active':''">
            <span>小于</span>
            <i class="el-icon-arrow-left"></i>
          </div>
        </div>
        <div class="right">
          <div class="input">
            <el-input v-model="money"
                      @input="money = money.replace(/[ \D]/g,'')"></el-input>&nbsp;&nbsp;{{unit}}
          </div>
          <span style="color:red;font-size:12px"
                v-show="valideMoney">请输入正确的数值</span>
          <div class="use">
            <el-button type="primary"
                       @click="use">应用</el-button>
          </div>
        </div>
      </div>
      <el-select v-model="select"
                 slot="reference"
                 :popper-append-to-body="false"
                 class="compare-el-select"
                 clearable
                 :title="selectTitle"
                 @clear="clear">
        <el-option v-for="item in selectOption"
                   :key="item.value"
                   :label="item.label"
                   :value="item.value">
        </el-option>
      </el-select>
    </el-popover>
  </div>
</template>
<script>
export default {
  props: {
    value: {
      type: Array,
      required: true
    },
    unit: {
      type: String,
      default: ''
    }
  },
  components: {},
  data () {
    return {
      select: '',
      popoverVisible: false,
      selectOption: [],
      type: 1,
      money: '',
      valideMoney: false
    }
  },
  created () { },
  mounted () {
    document.body.addEventListener('mousedown', this.handlerVisible)
  },
  beforeDestroy () {
    document.body.removeEventListener('mousedown', this.handlerVisible)
  },
  watch: {
    value: {
      handler (val) {
        if (!val || !val[0]) {
          Object.assign(this.$data, this.$options.data())
          return
        }
        this.valideMoney = isNaN(val[1]) || !val[1]
        if (this.valideMoney) return
        this.selectOption = [{ label: `大于${val[1]}${this.unit}`, value: 1 }, { label: `小于${val[1]}${this.unit}`, value: 2 }]
        this.select = val[0] === '>' ? 1 : 2
        this.type = this.select
        this.money = val[1]
      },
      immediate: true
    }
  },
  computed: {
    selectTitle () {
      let obj = this.selectOption.find(item => item.value === this.select)
      return obj ? obj.label : ''
    }
  },
  methods: {
    switchType (val) {
      this.type = val
      this.money = ''
    },
    handlerVisible (e) {
      let els = document.getElementsByClassName('compare-select-popper')
      let state = [...els].some(item => item.contains(e.target))
      if (!state) {
        this.type = 1
        this.money = ''
        this.popoverVisible = false
        this.valideMoney = false
      }
    },
    use () {
      this.valideMoney = isNaN(this.money) || !this.money
      if (this.valideMoney) return
      this.selectOption = [{ label: `大于${this.money}${this.unit}`, value: 1 }, { label: `小于${this.money}${this.unit}`, value: 2 }]
      this.select = this.type
      this.$emit('input', [this.type === 1 ? '>' : '<', this.money])
      this.popoverVisible = false
      this.valideMoney = false
      this.type = 1
      this.money = ''
    },
    clear () {
      this.select = ''
      this.selectOption = []
      this.type = 1
      this.money = ''
      this.$emit('input', [])
      this.valideMoney = false
      this.popoverVisible = false
    }
  }
}
</script>
<style scoped lang='scss'>
/deep/ .compare-el-select .el-select-dropdown {
  display: none;
}
.active {
  color: #338aff;
}
.content {
  width: 2.5rem;
  height: 150px;
  font-size: 14px;
  color: rgba(51, 51, 51, 1);
  display: flex;
  .left {
    width: 1rem;
    padding: 10px 0;
    box-sizing: border-box;
    border-right: 1px solid rgba(228, 233, 237, 1);
    .greater,
    .less {
      padding: 6px 0.2rem;
      display: flex;
      justify-content: space-between;
      align-items: center;
      cursor: pointer;
    }
  }
  .right {
    width: 1.5rem;
    padding-left: 10px;
    box-sizing: border-box;
    position: relative;
    .input {
      display: flex;
      padding: 10px 3px;
      align-items: center;
    }
    .use {
      position: absolute;
      right: 0;
      bottom: 0;
      text-align: right;
    }
  }
}
</style>
