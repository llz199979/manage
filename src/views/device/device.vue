<template>
  <div class="height_100 eg1">
    <div class="buttonGroup" v-if="tableInfo.addVisible && tableInfo.updateVisible">
    <!-- {{permissionArr}} -->
		<el-input v-show="permissionArr.includes('view')" size="small" v-model="page.nameOrMacId"
      @keydown.enter.native="selectName" clearable
      @clear="selectName" :placeholder="$t('m.placeholder.username')"
    style="width:180px;margin-right:10px;"></el-input>
      <buttonList v-bind:permissionArr="permissionArr" @buttonEvents="buttonEvents"></buttonList>
		<!-- <el-button @click="selectName" size="small">
			<i class="el-icon-search"></i>
		查询</el-button>
		<el-button type="success" @click="tableInfo.addVisible=false;" size="small">
			<i class="el-icon-circle-plus-outline"></i>
			添加</el-button>
		<el-button type="warning" @click="updateAction" size="small">
			<i class="el-icon-edit"></i>
			修改</el-button>
		<el-button type="danger" @click="deleteAction" size="small">
			<i class="el-icon-delete"></i>
			删除</el-button> -->
     
	</div>
    <tables v-bind:tableInfo="tableInfo" @select="select"></tables>
    <labelTop v-if="tableInfo.addVisible && tableInfo.updateVisible" :label-info="page"
      @sizeChange="sizeChange"
      @currentChange="currentChange"></labelTop>
      
    <!-- {{tableInfo}} -->
    <!-- <el-table v-if="addVisible && updateVisible"
    ref="multipleTable"
    :data="get_DeviceList.records"
    tooltip-effect="dark"
    height="calc(100% - 46px)"
    style="width: 100%" 
    :row-key="getRowKey"
    :expand-row-keys="expands"
    @row-dblclick="dblclick"
    @select="select"
    @select-all="selectAll">
    <el-table-column
      type="selection"
      width="55">
    </el-table-column>
    <el-table-column type="index" :index="table_index" align="center" width="60" label="序数" />
    <el-table-column type="expand" width="24">
            <template slot-scope="props">
              <el-form label-position="left" inline class="demo-table-expand">
                <el-form-item label="Id">
                  <span>{{ props.row.id }}</span>
                </el-form-item>
                <el-form-item label="姓名">
                  <span>{{ props.row.name }}</span>
                </el-form-item>
              </el-form>
            </template>
          </el-table-column>
    <el-table-column
      prop="name"
      label="设备名"
      >
    </el-table-column>
    <el-table-column
      prop="macId"
      label="mac地址"
      >
    </el-table-column>
    <el-table-column
      prop="equipmentDesc"
      label="描述"
      show-overflow-tooltip>
    </el-table-column>
  </el-table> -->
  <!-- {{get_RoleList}} -->
  <el-card v-show="!tableInfo.addVisible || !tableInfo.updateVisible" class="box-card"
  shadow="always" style="height:99%;">
  <!-- <div v-for="o in 4" :key="o" class="text item">
    {{'列表内容 ' + o }}
  </div> -->
  <div slot="header" class="clearfix">
    <span class="cardHeader" v-text="tableInfo.addVisible ? $t('m.header.infoUpdate') : $t('m.header.deviceAdd')"></span>
    <el-switch v-if="!tableInfo.addVisible" style="float:right;font-size:12px !important;"
      active-color="#13ce66"
      inactive-color=""
      v-model="switchStatus"
      :active-text="$t('m.header.addContinue')"
      >
    </el-switch>
  </div>
  <el-form  style="margin-left:60px;"
        class="form refuseCopy" :rules="rules" ref="addAction"
        size="small" label-position="left"
        :model="device"
        label-width="88px"
      >
        <el-form-item :label="$t('m.label.deviceName')" prop="name">
          <el-input clearable
            v-model="device.name"
            class="input_260"
            autocomplete="off"
          />
        </el-form-item>
        <el-form-item :label="$t('m.label.macAddress')" prop="macId">
          <el-input clearable
            v-model="device.macId"
            class="input_260"
            autocomplete="off"
          />
        </el-form-item>
        <el-form-item :label="$t('m.label.description')" prop="equipmentDesc">
          <el-input clearable
            v-model="device.equipmentDesc"
            class="input_260"
            autocomplete="off"
            @keydown.native.enter="addAction('addAction', device)"
          />
        </el-form-item>
        <el-form-item label="" >
          <el-button
            type="primary"
            @click="addAction('addAction', device)"
          >
            {{$t('m.button.confirm')}}
          </el-button>
          <el-button type="danger" @click="cancel">
            {{$t('m.button.cancel')}}
          </el-button>
        </el-form-item>
      </el-form>
      </el-card>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';
import labelTop from '../../components/page/label'
import tables from '../../components/table/table'
import buttonList from '../../components/table/button'
export default {
  name: 'eg1',
  components: {
    labelTop,
    tables,
    buttonList
  },
  data() {
    return {
      addVisible: true,     //添加框控制器
      updateVisible: true,  //修改框控制器
      expands: [],
      selection: [],        //选中的条目
      getRowKey(row) {      //隐藏栏id设置(不能唯一)
          return row.id;
      },
      permissionArr: [],
      tableInfo: {
        addVisible: true,     //添加框控制器
        updateVisible: true,  //修改框控制器
        currentPage: 1,
        pageSize: 20,
        // add: 'addAction',
        // update: 'updateDevice',
        // delete: 'deleteAction',
        get: 'get_DeviceList',
        parameter: {
          'name': '设备名',
          'macId': 'mac地址',
          'equipmentDesc': '设备描述'
        }
      },
      page: {
        currentPage: 1,
        pageSize: 20,
        roleName: '',
        total: 0,
        nameOrMacId: ''
      },
      switchStatus: false,
      device: {},
      rules:{
        name: { required: true, message: '设备名称为必填项', trigger: 'blur'},
        macId: { required: true, message: 'mac地址为必填项', trigger: 'blur'},
        // equipmentDesc: { required: true, message: '设备描述', trigger: 'blur'},
      },
    }
  },
  computed: {
    ...mapGetters([
      'get_DeviceList',
      'get_Userlist'
    ]),
    // get_RoleList() {
    //   return this.$store.getters.get_RoleList;
    // }
    getCatalogue() {
      return JSON.parse(localStorage.getItem('catalogue'));
    }
  },
  mounted() {
    // this.$store.dispatch('getUserListPage', this.page);
    // let arr = JSON.parse(localStorage.getItem('permissionArr'));
    // console.log(arr)
    this.$store.dispatch('getDeviceList', this.page)
    // this.$store.dispatch('getRoleList');
    this.permissionArr = this.$route.meta.arr;
    // console.log(this.$router)
  },
  methods: {
    select (selection) { // 单选
      this.selection = selection;
      console.log(this.selection);
    },
    selectAll (selection) { // 多选
      // console.log(selection)
      this.selection = selection;
      console.log(this.selection);
    },
    dblclick(row, cloumn, event) {
      // console.log(row)
      // if (this.expands.includes(row.id)) {
      //   this.expands = this.expands.filter(val => val !== row.id);
      // } else {
      //   this.expands.splice(0,1,row.id)
      // }
    },
     //翻页后序号连续
    table_index(index){
        return (this.page.currentPage-1) * this.page.pageSize + index + 1
    },
    sizeChange(val) {
      this.page.pageSize = val;
      this.tableInfo.pageSize = val;
      this.selectName()
      console.log('sizeChange: ' + val)
    },
    currentChange (val) {
      console.log('currentChage ' + val)
      this.page.currentPage = val;
      this.tableInfo.currentPage = val;
      this.selectName()
    },
    buttonEvents(operation) {
      switch(operation) {
        case 'add': this.tableInfo.addVisible = false; break;
        case 'delete': this.deleteAction(); break;
        case 'update': this.updateAction(); break;
        case 'select': this.selectName(); break;
      }
    },
    // 添加与修改
		addAction(form, device) {
			this.$refs[form].validate(valid => {
				if(valid) {
          console.log(device)
          if(this.tableInfo.updateVisible){ //账号添加、修改判别
            this.$store.dispatch('addDevice', device)
            .then(res => {
              console.log('addDeviceSuccess')
              this.device = {};
              if(!this.switchStatus) {
                this.tableInfo.addVisible = true;
              }
            })
          }else{
            this.$store.dispatch('updateDevice', device)
            .then(res => {
              this.device = {};
              this.tableInfo.updateVisible = true;
              // this.$store.dispatch('getUserListPage', this.page)
            })
          }
				}else{
					return false;
				}
			})
    },
    deleteAction() {
      if(this.selection.length) {
        console.log(this.selection)
        let arr =[];
        this.selection.forEach(v => {
          arr.push(v.id * 1);  //隐式转换
        })
        this.$store.dispatch('deleteDevice', arr)
        .then(res => {

        })
      }else{
         this.$notify.warning({
          title: '错误提示',
          message: '请至少选择一条信息'
        });
      }
    },
    updateAction() {
      if(this.selection.length == 1) {
        console.log(this.selection)
        this.tableInfo.updateVisible = false;
        // 从table列表中获取当前信息。
        this.device = JSON.parse(JSON.stringify(this.selection[0]));;
        if(this.device.tenantId) {  //后端问题,传这个参数报错
          delete this.device.tenantId;
        }
        console.log(this.selection)
        // this.device.id = this.user.id;
      }else{
         this.$notify.warning({
          title: '错误提示',
          message: '请先选择一条信息'
        });
      }
    },
    selectName() {
      this.$store.dispatch('getDeviceList', this.page);
    },
    
    cancel() {
      if(!this.tableInfo.updateVisible) {
        this.device = {};
        this.selection = [];
      }
      this.tableInfo.addVisible = true;this.tableInfo.updateVisible = true
      
    }
	}
}
</script>

<style scoped>
.eg1 {
  margin:0 8px;	
  text-align: left;

	/* background:#ffa5a5; */
}
.chest {
	width:500px;
	height:500px;
	margin:0 auto;
	position:relative;
}
.heart {
	position:absolute;
	z-index:2;
	background:linear-gradient(-90deg,#F50A45 0%,#d5093c 40%);
	animation:beat 2s ease 0s infinite normal;
}
.heart.center {
	background:linear-gradient(-45deg,#B80734 0%,#d5093c 40%);
}
.heart.top {
	z-index:3;
}
.sided {
	top:100px;
	width:220px;
	height:220px;
	border-radius:110px;
}
.center {
	width:210px;
	height:210px;
	bottom:100px;
	left:145px;
	transform:rotateZ(225deg);
}
.left {
	left:62px;
}
.right {
	right:62px;
}
@keyframes beat {
	0% {
	transform:scale(1) rotate(225deg);
	box-shadow:0 0 40px #d5093c;
}
50% {
	transform:scale(1.1) rotate(225deg);
	box-shadow:0 0 70px #d5093c;
}
100% {
	transform:scale(1) rotate(225deg);
	box-shadow:0 0 40px #d5093c;
}
}
.input_260{
  width:260px;
}
</style>