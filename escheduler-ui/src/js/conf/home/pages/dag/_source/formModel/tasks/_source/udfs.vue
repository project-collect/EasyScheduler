<template>
  <div class="udfs-model">
    <x-select multiple
              v-model="udfsStr"
              :disabled="isDetails"
              style="width: 100%;">
      <x-option
              v-for="city in udfsList"
              :key="city.id"
              :value="city"
              :label="city.code">
      </x-option>
    </x-select>
  </div>
</template>
<script>
  import _ from 'lodash'
  import disabledState from '@/module/mixin/disabledState'

  export default {
    name: 'udfs',
    data () {
      return {
        // UDFS Function
        udfsStr: [],
        // UDFS Function(List)
        udfsList: []
      }
    },
    mixins: [disabledState],
    props: {
      udfs: String,
      type: String
    },
    methods: {
      /**
       * verification
       */
      _verifUdfs () {
        this.$emit('on-udfsData', _.map(this.udfsStr, v => v.id).join(','))
        return true
      },
      /**
       * Get UDFS function data
       */
      _getUdfList () {
        this.udfsList = []
        this.store.dispatch('dag/getUdfList', { type: this.type }).then(res => {
          this.udfsList = _.map(res.data, v => {
            return {
              id: v.id,
              code: v.funcName
            }
          })

          let udfs = _.cloneDeep(this.udfs.split(','))
          if (udfs.length) {
            let arr = []
            _.map(udfs, v => {
              _.map(this.udfsList, v1 => {
                if (parseInt(v) === v1.id) {
                  arr.push(v1)
                }
              })
            })
            this.$nextTick(() => {
              this.udfsStr = arr
            })
          }
        })
      }
    },
    watch: {
      type (a) {
        // The props parameter needs to be changed due to the scene.
        this.udfs = ''
        if (a === 'HIVE') {
          this._getUdfList()
        } else {
          this.udfsList = []
        }
      }
    },
    created () {
      this._getUdfList()
    },
    mounted () {
    }
  }
</script>

<style lang="scss" rel="stylesheet/scss">
  .udfs-model {
    .add-udfs {
      color: #0097e0;
    }
    .t-list {
      margin-bottom: 14px;
      .v-btn {
        width: 208px;
      }
      &:hover {
      }
      .delect-btn {
        display: inline-block;
        width: 54px !important;
        vertical-align: middle;
        background: #ff0000;
        border-color: #ff0000;
        span {
          vertical-align: middle !important;
        }
      }
    }
  }
</style>
