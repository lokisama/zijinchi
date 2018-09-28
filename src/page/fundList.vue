<template>
    <div class="fillcontain">
        <div class="table_container">
            <el-table
                 :data="tableData"
                 border
                 highlight-current-row
                 style="width:100%">
                
                <el-table-column
                   property="status"
                   label="状态"
                   width="80"
                   align='center'>
                   <template slot-scope="scope">  
                    <span style="color:#67c23a;" v-if="scope.row.status == 'SUCCESS'">{{ format_status_list[scope.row.status] }}</span>
                    <span style="background:#CC0033;color:#fff;cursor: pointer;padding: 6px 10px;border-radius: 8px;" v-else @click='onEditMoney(scope.row)'>{{ format_status_list[scope.row.status] }}</span>
                    </template>
                </el-table-column>
                <el-table-column
                   prop="type"
                   label="类型"
                   width="100"
                   align='center'>
                   <template slot-scope="scope">  
                    <span style="color:#CC0033">{{ format_type_list[scope.row.type] }}</span>
                    </template>
                </el-table-column>
                 <el-table-column
                   property="orderId"
                   label="订单编号"
                   width="310"
                   align='center'>
                </el-table-column>
                <!-- <el-table-column
                   property="address"
                   label="注册地址"
                   width="160"
                   align='center'> 
                </el-table-column>-->
               <!-- <el-table-column
                   property="area"
                   label="注册区域"
                   width="120"
                   align='center'>
                </el-table-column> -->
                  <el-table-column
                   property="phone"
                   label="手机"
                   width="180"
                   align='center'>
                </el-table-column> 
                 <el-table-column
                   property="cName"
                   label="姓名"
                   width="80"
                   align='center'>
                </el-table-column>
                <el-table-column
                   property="price"
                   label="金额"
                   width="80"
                   align='center'>
                </el-table-column>
                <el-table-column
                   property="orderTime"
                   label="时间"
                   width="180"
                   align='center'>
                </el-table-column>
                <el-table-column
                   property="admin"
                   label="操作员"
                   width="120"
                   align='center'>
                  
                </el-table-column>
                
                
            </el-table>

           <el-row>
                <el-col :span="24">
                    <div class="pagination">
                        <el-pagination
                            v-if='paginations.total > 0'
                            :page-sizes="paginations.page_sizes"
                            :page-size="paginations.page_size"
                            :layout="paginations.layout"
                            :total="paginations.total"
                            :current-page='paginations.page_index'
                            @current-change='handleCurrentChange'
                            @size-change='handleSizeChange'>
                        </el-pagination>
                    </div>
                </el-col>
            </el-row>

            <el-dialog 
            :title="dialog.title" 
            :visible.sync="dialog.show"
            :close-on-click-modal='false'
            :close-on-press-escape='false'
            :modal-append-to-body="false">
            <div class="form">
                <el-form 
                    ref="form" 
                    :model="form"
                    :rules="form_rules"
                    :label-width="dialog.formLabelWidth" 
                    style="margin:10px;width:auto;">

                    <el-form-item label="状态类型:" >
                        

                        <el-select v-model="form.status" placeholder="收支类型">
                            <el-option label="等待" value="WAIT"></el-option>
                            <el-option label="完成" value="SUCCESS"></el-option>
                        </el-select>

                    </el-form-item>

                    <el-form-item prop='type' label="收支描述:">
                        <el-select v-model="form.type" placeholder="订单类型" disabled>
                            <el-option label="提现" value="CASH_WITHDRAW"></el-option>
                            <el-option label="存入" value="CASH_DEPOSIT"></el-option>
                            <el-option label="挂单" value="ORDER_RESTING"></el-option>
                        </el-select>
                    </el-form-item>

                    <el-form-item prop='price'  label="单位(手)" v-if="form.type == 'ORDER_RESTING'">
                        <el-input type="price" v-model.number="form.price" disabled></el-input>
                    </el-form-item>
                    <el-form-item prop='price'  label="单位(元)" v-if="form.type == 'CASH_WITHDRAW' || form.type == 'CASH_DEPOSIT'">
                        <el-input type="price" v-model.number="form.price" disabled></el-input>
                    </el-form-item>

                    <el-form-item prop='cName' label="用户:">
                        <el-input type="cName" v-model="form.cName" disabled></el-input>
                    </el-form-item>

                    <el-form-item prop='phone' label="手机:">
                        <el-input type="phone" v-model="form.phone" disabled></el-input>
                    </el-form-item>

                     <el-form-item label="备注:">
                        <el-input type="textarea" v-model="form.remarks"></el-input>
                    </el-form-item>

                    <el-form-item  class="text_right">
                        <el-button @click="dialog.show = false">取 消</el-button>
                        <el-button type="primary" @click='onSubmit("form")'>提  交</el-button>
                    </el-form-item>

                </el-form>
            </div>
        </el-dialog>

        </div>
    </div>
</template>

<script>
    import * as mutils from 'utils/mUtils'
    import {axios} from 'utils/'

    export default {
        data(){
            let validateData = (rule, value, callback) => {
                if(value === ''){
                    let text;
                    if(rule.field == "income"){
                        text='收入';
                    }else if(rule.field == "pay"){
                        text='支出';
                    }else{
                        text='账户现金';
                    }
                    callback(new Error(text+'不能为空~'));
                }else{
                   let numReg = /^[0-9]+.?[0-9]*$/;
                   if(!numReg.test(value)){
                      callback(new Error('请输入数字值'));
                   }else{
                      callback();
                   }
                }
            };
            return {
                host:'http://127.0.0.1:3000',
                admin: {"phone":"15093663999"},
                sortnum:0,
                tableData: [],
              //需要给分页组件传的信息
                paginations: {
                    page_index: 1,  // 当前位于哪页
                    total: 0,        // 总数
                    page_size: 20,   // 1页显示多少条
                    page_sizes: [5, 10, 15, 20],  //每页显示多少条
                    layout: "total, sizes, prev, pager, next, jumper"   // 翻页属性
                },
                format_type_list: {
                    'CASH_WITHDRAW': '出账(提现)',
                    'CASH_DEPOSIT': '入账(存入)',
                    'ORDER_RESTING': '出账(挂单)',
                },
                format_status_list: {
                    'WAIT': '等待',
                    'SUCCESS': '完成'
                },
                dialog: {
                    width:'400px',
                    show : false,
                    title: '修改订单信息',
                    formLabelWidth:'120px'
                },
                form:{
                    incomePayType:'',
                    incomePayDes: '',
                    income: '',
                    pay:'',
                    accoutCash:'',
                    remarks: '',
                    status:'WAIT',
                },
                form_rules: {
                     incomePayDes   : [
                        {required: true, message : '收支描述不能为空！',trigger : 'blur'}
                     ],
                     income   : [
                        { required: true, validator:validateData,trigger: 'blur'},
                     ],
                     pay   : [
                        { required: true, validator:validateData,trigger: 'blur'},
                     ],
                     accoutCash   : [
                          { required: true, validator:validateData,trigger: 'blur'},
                    ],
               }
            }
        },
        components: {

        },
        created(){
            
        },
        mounted(){
            this.getList({
                fun: () => {}
            });
        },
        methods: {
            getList({
                page,
                page_size,
                where,
                fun
            } = {}){
                var query = this.$route.query;
                this.paginations.page_index = page || parseInt(query.page) || 1;
                this.paginations.page_size  = page_size || parseInt(query.page_size) || this.paginations.page_size;
                var data = {
                    pageIndex: this.paginations.page_index,
                    pageSize: this.paginations.page_size
                };
                if (where) {
                   data = Object.assign(data, where || {});
                } 
                // 封装  get,path,params,fn,errfn
                // axios({
                //     type:'get',
                //     path:'/api/user/getUserInfo',
                //     data:data,
                //     fn:data=>{
                //         console.log(data);
                //         //成功之后的回调函数
                //         this.paginations.total = data.count;
                //         this.tableData = [];
                //      data.data.forEach( (item,index) => {
                //          const tableItem = {
                //                 id:  item._id,
                //                 sortnum:this.sortnum+(index+1),
                //                 username:item.username,
                //                 address:item.address,
                //                 createTime: mutils.parseToDate(JSON.stringify(item.createTime)),
                //                 updateTime: mutils.parseToDate(JSON.stringify(item.updateTime)),
                //                 ip:item.ip,
                //                 area:item.area,
                //                 region_id:item.region_id,  //地区编号
                //                 city_id:item.city_id, //城市编号
                //                 isp:item.isp, // 网络
                //          }
                //          this.tableData.push(tableItem);
                //         })
                //         fun && fun();
                //     }
                // })
                
                axios({
                  type:'post',
                  path: this.host + '/api/v2/getUserFlow',
                  data: Object.assign({"status":"ALL"} , this.admin),
                  fn:data=>{
                    data.forEach( (item,index) => {
                       var date = new Date(item.createTime);
                       var year = date.getFullYear();
                       var month = date.getMonth()+1;    //js从0开始取 
                       var date1 = date.getDate(); 
                       var hour = date.getHours(); 
                       var minutes = date.getMinutes(); 
                       var second = date.getSeconds();
                      const tableItem = {
                          id:  item.id,
                            orderId: item.outId,
                            price:item.value,
                            phone:item.phone,
                            cName:item.cName || '未实名',
                            orderTime:year+"/"+month+"/"+date1+" "+hour+":"+minutes +":"+second,
                            createTime:item.createTime,
                            status:item.status,
                            type:item.type,
                            admin:item.admin
                      }

                      this.tableData.push(tableItem);
                    });
                    fun && fun();
                  }
                });
           },

            /**
            * 改变页码和当前页时需要拼装的路径方法
            * @param {string} field 参数字段名
            * @param {string} value 参数字段值
            */
            setPath(field, value) {
                var path  = this.$route.path,
                    query = Object.assign({}, this.$route.query);
                if (typeof field === 'object') {
                    query = field;
                } else {
                    query[field] = value;
                }
                this.$router.push({
                    path,
                    query
                });
            },
            // 每页多少条切换
            handleSizeChange(page_size) {
               console.log(`每页 ${page_size} 条`);
               this.getList({
                    page_size,
                    fun: () => {
                        this.setPath('page_size', page_size);
                    }
               });
            },
            // 上下分页
            handleCurrentChange(page) {
               this.sortnum = this.paginations.page_size*(page-1);
               this.getList({
                    page,
                    fun: () => {
                        this.setPath('page', page);
                    }
                });
            },

            // 操作方法
            onEditMoney(row){
                this.editid = row.id;
                this.form = Object.assign({},row);
                
               
                this.dialog.title = '修改订单信息 [ '+row.orderId + ' ]';
                this.dialog.show  = true;
            },

            //表单提交
            onSubmit(form){
                this.$refs[form].validate((valid) => {
                    if (valid) {//表单数据验证完成之后，提交数据;
                        let formData = this[form];
                        let data = {};
                        
                        for(var i in formData){
                            data.id = this.editid;
                            data.cName = formData['cName'];
                            data.phone = formData['phone'];
                            data.orderId = formData['orderId'];
                            data.orderTime = formData['orderTime'];
                            data.price = formData['price'];
                            data.status = formData['status'];
                            data.type = formData['type'];
                        }
                        console.log(data);
                        if(this.editid != ""){
                            this.editOrder(data)
                        }else{
                            this.addOrder(data)
                        }
                       
                    }
                })
            },
            editOrder(data){
                axios({
                    type:'post',
                    path:this.host + '/api/v2/setOrderSuccess',
                    data: Object.assign( {"orderId":data.id}, this.admin),
                    fn:data=>{
                        this.$message('编辑成功'),
                        this.paginations.total = data.count;
                        this.getList({fun: () => {} }); 
                        this.dialog.show = false;
                    },
                    errFn:()=>{
                        this.$message.error('编辑失败请重试')
                    }
                })
            },
            addOrder(data){
                axios({
                    type:'post',
                    path:this.host + '/api/v2/setOrderSuccess',
                    data:Object.assign({"orderId":data.id}, this.admin),
                    fn:data=>{
                        this.$message('编辑成功'),
                        this.paginations.total = data.count;
                        this.getList({fun: () => {} }); 
                        this.dialog.show = false;
                    },
                    errFn:()=>{
                        this.$message.error('编辑失败请重试')
                    }
                })
            }
           
        },
    }
</script>
