<template>
    <!-- 时间块组件 -->
  <div class="c-time-block">
      <div class="block-box">
          <div :class="['block', `${item.status}`]" v-for="item in blocks" :key="item.text" @click="sel_block(item.text)">
              {{item.text}}
          </div>
      </div>
  </div>
</template>
<script>
import { blocks } from './data/time.js'
import { deepCopy } from './js/c_common.js'
export default {
  name: 'c-time-block',
  data() {
    return {
        blocks: deepCopy(blocks),
        selected_blocks: []
    }
  },
  props: {
      clear: {
          type: Boolean,
          default() {
              return false
          }
      },
      num: {
          type: Number,
          default() {
              return 14
          }
      },
      disabled: {
          type: Array,
          default() {
              return []
          }
      }
  },
  watch: {
      disabled(n, o){
            this.blocks.forEach(blo => {
                if(n.includes(blo.text)) { // 未选禁用
                    blo.status = 'disabled'
                } else { // 解除禁用
                    blo.status = blo.status==='disabled'?'unselected':blo.status
                }
            })
      },
      clear(n, o) {
          if(n) {
            this.blocks.map(i => i.status='unselected')
            this.selected_blocks = []
            this.$emit('getBlocks', this.selected_blocks)
          }
      }
  },
  methods: {
      sel_block(b) {
          if(!this.disabled.includes(b)) {
                if(!this.selected_blocks.includes(b)) { // 点击未选
                    this.blocks.forEach(blo => {
                        if(blo.text===b) {
                            blo.status = 'selected'
                        }
                    })
                    this.selected_blocks.push(b)
                    this.$emit('getBlocks', this.selected_blocks)
                } else { // 点击已选
                    this.blocks.forEach(blo => {
                        if(blo.text===b) {
                            blo.status = 'unselected'
                        }
                    })
                        let idx = this.selected_blocks.findIndex(bb => bb===b)
                        this.selected_blocks.splice(idx, 1)
                        this.$emit('getBlocks', this.selected_blocks)
                }
          }
          
      }
  },
  created() {
      // 时间块初始化
      let blocks_temp = []
      for(let i=1;i<=this.num;i++) {
          let s = this.disabled.find(ss => ss === i)? 'disabled':'unselected'
          let b = {
              text: i,
              status: s
          }
        blocks_temp.push(b)
      }
      this.blocks = blocks_temp
  }
}
</script>
<style lang="less" scoped>
.c-time-block {
    background: #def2fb;
    padding: 6px 0 0 6px;
}
.block-box {
    display: flex;
    flex-wrap: wrap;
}
.block {
    width: 30px;
    height: 30px;
    border: 2px solid #27bdff;
    background: #fff;
    color: #27bdff;
    line-height: 26px;
    font-size: 16px;
    text-align: center;
    margin: 0 6px 6px 0;
    cursor: pointer;
    transition: .3s;
    &:hover {
        background: #27bdff;
        color: #fff;
    }
}
.selected {
    border-color: #27bdff;
    background: #27bdff;
    color: #fff;
}
.disabled {
    border-color: #b8e6fb;
    background: #b8e6fb;
    color: #fff;
    &:hover {
        background: #b8e6fb;
        color: #fff;
    }
}
</style>
