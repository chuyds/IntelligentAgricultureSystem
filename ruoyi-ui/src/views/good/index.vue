<template>
  <div id="index">
    <!-- 查询表单 -->
    <el-form ref="findForm" :model="formData" :rules="rules" :inline="true" size="mini" label-width="100px">
      <el-form-item label="产品类别" prop="field">
        <el-select id="select" v-model="formData.field" placeholder="如果需要查看某一类产品请在此处选择" clearable :style="{width: '300px'}">
          <el-option v-for="(item, index) in fieldOptions" :key="index" :label="item.label"
            :value="item.value" :disabled="item.disabled"></el-option>
        </el-select>
      </el-form-item>

      <el-form-item size="mini">
        <el-button type="primary" icon="el-icon-search" @click="submitForm">搜索</el-button>
        <el-button icon="el-icon-refresh" size="mini" @click="resetForm">重置</el-button>
      </el-form-item>

    </el-form>

    <!-- 显示产品 -->
    <el-row>
      <el-col :span="6" v-for="(good,index) in goods" class="card-box" style="margin: 0;overflow: hidden;">
        <el-card v-if="pageIndex === parseInt(index/pageCount) +1" style="height: 320px;">
          <img id="image" :src="good.imageUrl" alt="">
          <div id="text-description">
            <a href="javascript:void(0)"><h6  style="text-align: center;">{{good.name}}</h6></a>
            <span>{{good.description}}</span>
          </div>
        </el-card>
      </el-col>
    </el-row>

    <!-- 页码列表 -->
    <el-pagination
      background
      style="text-align: center;"
      layout="prev, pager, next"
      @current-change="currentChange"
      :page-size="pageCount"
      :total="goods.length"
      :current-page="pageIndex">
    </el-pagination>

  </div>

</template>

<script>
import { listGood, listGoodByCategory, getGood } from "@/api/system/good";

export default {
  components: {},
  props: [],
  data() {
    return {
      formData: {
        field: undefined,
      },
      rules: {
        field: [{
          required: true,
          message: '如果需要查看某一类产品请在此处选择',
          trigger: 'change'
        }],
      },
      fieldOptions: [{
        "label": "汽车",
        "value": 1
      },{
        "label": "酒",
        "value": 2
      },{
        "label": "茶叶",
        "value": 3
      },{
        "label": "旅游景点",
        "value": 4
      }],
      goods:[],     //存储产品列表
      goodsOfCategory:[],     //category类别的产品列表
      pageCount:8,            //分页时，每页显示的产品数量
      pageIndex:1             //当前页码
    }
  },
  computed: {},
  watch: {},
  created() {},
  mounted() {
    this.getList();
  },
  methods: {
    getList() {     //获取全部商品列表
      listGood().then(response => {
        this.goods = response.data;
      });
    },
    getListByCategory(category) {     //获取category类别商品列表
      listGoodByCategory(category).then(response => {
        this.goods = response.data;
      });
      this.pageIndex = 1;           //重置页码
    },
    submitForm() {
      var category;
      var categoryElt = document.getElementById("select");
      for (var i = 0; i < this.fieldOptions.length; i++) {      //获取当前类别id
        if(this.fieldOptions[i].label===categoryElt.value){
          category = this.fieldOptions[i].value;
        }
      }
      // alert(category);

      this.getListByCategory(category);
    },
    resetForm() {
      this.$refs['findForm'].resetFields();
      this.getList();
    },
    currentChange(pageNum) {
      this.pageIndex = pageNum;
    }
  }
}

</script>

<style>
    .el-row {
      margin-bottom: 20px;
      &:last-child {
        margin-bottom: 0;
      }
    }
    .el-col {
      border-radius: 4px;
    }
    .bg-purple {
      background: #d3dce6;
    }
    .bg-purple-light {
      background: #e5e9f2;
    }
    .grid-content {
      border-radius: 4px;
      min-height: 36px;
    }

    #image{
      width: 260px;
      height: 200px;
    }

    h6 {
      color: #FF7F50;
      transition: all 0.3s;
      text-decoration: none;
    }
    h6:hover {
      color: #1E90FF;
    }

</style>
