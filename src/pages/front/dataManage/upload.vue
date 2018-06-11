<template>
  <div class="upload-data">
    <div class="title">数据上传</div>
    <div class="main">
      <ul class="top">
        <li class="left">数据上传</li>
        <li class="right">
          <el-button class="btn">
            <a :href="ServerHttp" target="_blank">下载全部模板</a>
          </el-button>
        </li>
      </ul>
      <ul class="body">
        <li>
          <el-button @click="uploadSuccess"><i class="el-icon-upload2"></i> 销售订单</el-button>
          <input type="file"  @change="uploadSO" ref="uploadSO" />
        </li>
        <li>
          <el-button><i class="el-icon-upload2"></i> 每日工单数据</el-button>
          <input type="file"  @change="uploadWO" ref="uploadWO"/>
        </li>
        <li>
          <el-button><i class="el-icon-upload2"></i> 生产工单</el-button>
          <input type="file"  @change="uploadPRO" ref="uploadPRO"/>
        </li>
        <li>
          <el-button><i class="el-icon-upload2"></i> 每日库存数据</el-button>
          <input type="file"  @change="uploadStock" ref="uploadStock"/>
        </li>
        <li>
          <el-button><i class="el-icon-upload2"></i> 每日交易数据</el-button>
          <input type="file"  @change="uploadTransaction" ref="uploadTransaction"/>
        </li>
        <li>
          <el-button><i class="el-icon-upload2"></i> 导入SKU</el-button>
          <input type="file"  @change="uploadSKU" ref="uploadSKU"/>
        </li>
      </ul>
    </div>
    <div class="err-data" v-if="failedCount">
      <ul class="top">
        <li class="left">
          上传失败数据
          <span>*共上传{{totalSize}}条数据，上传成功{{successCount}}条，上传失败{{failedCount}}条，请完善后重新上传</span>
        </li>
        <li class="right">
          <el-button class="btn">导出表格</el-button>
        </li>
      </ul>
      <div class="table">
        <ul class="header">
          <li class="label-1">序号</li>
          <li class="label-2">数据ID</li>
          <li class="label-3">异常原因</li>
        </ul>
        <ul class="body" v-for="(item, index) in failedCauseList" :key="item.id">
          <li class="label-1">{{index + 1}}</li>
          <li class="label-2">{{item.id}}</li>
          <li class="label-3">{{item.cause}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import * as funSets from '@/api/front/dataManage/uploadData'
import { getUserInfo } from '@/utils/auth'
export default {
  name: 'upload',
  data () {
    return {
      totalSize: null, // 数据总条数
      successCount: null, // 成功数量
      failedCount: null, // 失败数量
      failedCauseList: [], // 失败数据列表
      // 导入数据时1请求参数
      params: {
        currentAccountId: getUserInfo().accountId,
        currentAccountName: getUserInfo().accountName,
        supplierId: 'fef00851582711e8b8cf525400a13961', // getUserInfo().supplierId,
        filePath: ''
      }
    }
  },
  mounted () {
  },
  methods: {
    // 下载模板
    downloadTemplate () {
    },
    // 销售订单
    uploadSO (e) {
      let t = this
      funSets.uploadFile(e, t).then(
        res => {
          if (res.data.code === 200) {
            this.params.filePath = this.ServerHttp + res.data.result[0].photoBig
            funSets.importSO(this.params).then(
              res => {
                let curRef = 'uploadSO'
                this.uploadSuccess(res, curRef)
              }
            ).catch(
              err => {
                this.$message.error(err)
              }
            )
          }
        }
      ).catch(
        err => {
          this.$message.error(err)
        }
      )
    },
    // 每日工单
    uploadWO (e) {
      let t = this
      funSets.uploadFile(e, t).then(
        res => {
          if (res.data.code === 200) {
            this.params.filePath = this.ServerHttp + res.data.result[0].photoBig
            funSets.importWO(this.params).then(
              res => {
                let curRef = 'uploadWO'
                this.uploadSuccess(res, curRef)
              }
            ).catch(
              err => {
                this.$message.error(err)
              }
            )
          }
        }
      ).catch(
        err => {
          this.$message.error(err)
        }
      )
    },
    // 生产工单
    uploadPRO (e) {
      let t = this
      funSets.uploadFile(e, t).then(
        res => {
          if (res.data.code === 200) {
            this.params.filePath = this.ServerHttp + res.data.result[0].photoBig
            console.log(456456, this.params.filePath)
            funSets.importPRO(this.params).then(
              res => {
                let curRef = 'uploadPRO'
                this.uploadSuccess(res, curRef)
              }
            ).catch(
              err => {
                this.$message.error(err)
              }
            )
          }
        }
      ).catch(
        err => {
          this.$message.error(err)
        }
      )
    },
    // 每日库存
    uploadStock (e) {
      let t = this
      funSets.uploadFile(e, t).then(
        res => {
          if (res.data.code === 200) {
            this.params.filePath = this.ServerHttp + res.data.result[0].photoBig
            console.log(456456, this.params.filePath)
            funSets.importStockStatus(this.params).then(
              res => {
                let curRef = 'uploadStock'
                this.uploadSuccess(res, curRef)
              }
            ).catch(
              err => {
                this.$message.error(err)
              }
            )
          }
        }
      ).catch(
        err => {
          this.$message.error(err)
        }
      )
    },
    // 每日交易
    uploadTransaction (e) {
      let t = this
      funSets.uploadFile(e, t).then(
        res => {
          if (res.data.code === 200) {
            this.params.filePath = this.ServerHttp + res.data.result[0].photoBig
            console.log(456456, this.params.filePath)
            funSets.importStockTransactions(this.params).then(
              res => {
                let curRef = 'uploadTransaction'
                this.uploadSuccess(res, curRef)
              }
            ).catch(
              err => {
                this.$message.error(err)
              }
            )
          }
        }
      ).catch(
        err => {
          this.$message.error(err)
        }
      )
    },
    // SKU
    uploadSKU (e) {
      let t = this
      funSets.uploadFile(e, t).then(
        res => {
          if (res.data.code === 200) {
            this.params.filePath = this.ServerHttp + res.data.result[0].photoBig
            console.log(456456, this.params.filePath)
            funSets.importSku(this.params).then(
              res => {
                let curRef = 'uploadSKU'
                this.uploadSuccess(res, curRef)
              }
            ).catch(
              err => {
                this.$message.error(err)
              }
            )
          }
        }
      ).catch(
        err => {
          this.$message.error(err)
        }
      )
    },
    // 上传成功函数
    uploadSuccess (res, curRef) {
      console.log('ref', curRef)
      let data = res.data.data
      this.totalSize = data.totalSize
      this.successCount = data.successCount
      this.failedCount = data.failedCount
      if (!data.failedCount) {
        this.$alert(
          `<div class="content"><p class="img"><i class="el-icon-check"></i></p><ul class="des"><li>已成功上传${this.successCount}条工单数据</li></ul></div>`,
          '数据上传提示',
          {
            confirmButtonText: '确定',
            dangerouslyUseHTMLString: true,
            customClass: 'upload-alert'
          }
        )
      } else {
        this.failedCauseList = data.failedCauseList
      }
      // 清空当前input[file]
      this.$refs[curRef].value = null
    }
  }
}
</script>

<style lang="sass">
  @import "upload"
</style>
