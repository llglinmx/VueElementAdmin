<template>
  <div class="box">
    <el-container>
      <el-header>
        <div class="head-top">
          <div class="title">校区列表</div>
          <el-button type="primary" size="medium" @click="addCampus"
            >添加校区</el-button
          >
        </div>
      </el-header>
      <el-main>
        <!--列表-->
        <el-table
          :data="tableList"
          v-loading="listLoading"
          border
          element-loading-text="拼命加载中"
          style="width: 100%"
        >
          <el-table-column prop="uid" label="序号" width="65">
          </el-table-column>
          <el-table-column prop="cname" label="姓名"> </el-table-column>
          <el-table-column prop="title" min-width="150px" label="标题">
          </el-table-column>
          <el-table-column prop="number" label="阅读数" width="65">
          </el-table-column>
          <el-table-column prop="date" label="时间" width="110px">
          </el-table-column>
          <el-table-column label="状态" width="120">
            <template slot-scope="scope">
              <el-tag
                size="small"
                :type="scope.row.status | statusFilter"
                @click="isStatus(scope.$index, scope.row)"
                v-if="scope.row.status == 1"
                >启用</el-tag
              >
              <el-tag
                size="small"
                :type="scope.row.status | statusFilter"
                @click="isStatus(scope.$index, scope.row)"
                v-if="scope.row.status == 2"
                >禁用</el-tag
              >
            </template>
          </el-table-column>
          <el-table-column prop="operation" label="操作 ">
            <template slot-scope="scope">
              <el-button
                size="small"
                type="primary"
                @click="handleUpdate(scope.row)"
                >编辑</el-button
              >
              <el-button
                v-if="scope.row.status != '2'"
                size="mini"
                type="success"
                @click="handleModifyStatus(scope.row, '2')"
                >启用
              </el-button>
              <el-button
                v-if="scope.row.status != '1'"
                size="mini"
                @click="handleModifyStatus(scope.row, '1')"
                >禁用
              </el-button>
              <el-button
                size="small"
                type="danger"
                @click="deleteUpdate(scope.row)"
                >删除</el-button
              >
            </template>
          </el-table-column>
        </el-table>

        <!-- 新增校区 -->
        <el-dialog title="添加校区" :visible.sync="isAddCamp">
          <el-form label-width="80px" :model="temp" ref="dataForm">
            <el-form-item label="姓名" prop="cname">
              <el-input v-model="temp.cname"></el-input>
            </el-form-item>
            <el-form-item label="时间" prop="date">
              <el-input v-model="temp.date"></el-input>
            </el-form-item>
            <el-form-item label="状态" v-model="temp.status">
              <el-select v-model="temp.status" placeholder="启用状态">
                <el-option>下拉数据</el-option>
              </el-select>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="isAddCamp = false">取消</el-button>
            <el-button type="primary" @click="addCampusInfo">确定</el-button>
          </div>
        </el-dialog>
      </el-main>
      <el-footer>
        <!--工具条-->
        <el-pagination
          layout="total, prev, pager, next"
          background
          :page-size="pageSize"
          @size-change="handleSizeChange"
          :total="total"
          @current-change="handleCurrentChange"
          style="text-align: right"
        >
        </el-pagination>
      </el-footer>
    </el-container>
  </div>
</template>

<script>
import { getList } from "@/api/table";
export default {
  name: "campus",
  data() {
    return {
      listLoading: false,
      tableList: [],
      total: 0,
      page: 1,
      pageSize: 10,
      isAddCamp: false, // 新增校区
      temp: {
        uid: "",
        cname: "",
        date: "",
        status: "",
      },
    };
  },

  created() {
    this.fetchData();
  },
  methods: {
    // 添加校区按钮
    addCampus() {
      this.isAddCamp = true;
    },
    // 添加校区确定按钮
    addCampusInfo() {
      // this.listLoading = true

      setTimeout(() => {
        this.isAddCamp = false;
 this.$notify({
        title: '成功',
        message: '添加成功',
        type: 'success',
        duration: 2000
      })

      }, 1000);
    },

    fetchData() {
      this.listLoading = true;
      getList(this.listQuery).then((response) => {
        const limit = 10;
        const pageList = response.data.filter(
          (item, index) =>
            index < limit * this.page && index >= limit * (this.page - 1)
        );
        console.log(pageList);
        this.total = response.data.length;
        this.tableList = pageList;
        this.listLoading = false;
      });
    },
    handleSizeChange(val) {
      this.page = val;
      console.log(this.page);
      this.fetchData();
    },
    handleCurrentChange(val) {
      this.page = val;
      console.log(this.page);
      this.fetchData();
    },
  },
};
</script>
<style lang="scss" scoped>
.box {
  width: 100%;
  height: 100%;
  box-sizing: border-box;
  overflow-y: scroll;

  .head-top {
    display: flex;
    width: 100%;
    height: 100%;
    align-items: flex-end;
    justify-content: space-between;
    .title {
      font-size: 24px;
    }
  }
}
</style>