# 使用Bootstrap + Vue.js实现表格的动态展示、新增和删除

----
### 一、写在前面
> 
1. Bootstrap是一个由 *Twitter* 开发和维护的前端框架，目前很受欢迎，Bootstrap中文网[点击这里](http://www.bootcss.com)。
2. Vue.js 是一套构建用户界面的渐进式框架，[点这里访问官网](https://cn.vuejs.org/)。

### 二、实现效果：
![1.png-45.2kB][1]

### 三、页面引入bootstrap、vue资源
```
<link rel="stylesheet" href="//cdn.bootcss.com/font-awesome/4.7.0/css/font-awesome.css">
<link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta.2/css/bootstrap.min.css">
<script src="//cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>
<script src="//cdn.bootcss.com/popper.js/1.12.5/umd/popper.min.js"></script>
<script src="//cdn.bootcss.com/bootstrap/4.0.0-beta/js/bootstrap.min.js"></script>
<script src="//cdn.bootcss.com/vue/2.5.8/vue.min.js"></script>
```
<font style="color:blue">&emsp;这里需要注意的是，Boostrap依赖于JQuery，必须在引入Boostrap之前引入JQuery。</font>

### 四、绘制表格
1.工具栏区
```
   <div class="row mx-auto w-75">
        <div class="col-6">
            <div class="btn-group">
                <button type="button" class="btn btn-outline-info btn-sm" data-toggle="modal" data-target="#myModal">新增</button>
                <button type="button" class="btn btn-outline-primary btn-sm" @click="saveRows">保存</button>
            </div>
            <button type="button" class="btn btn-outline-warning btn-sm" @click="delRows">删除</button>
        </div>
        <div class="col-6">
            <div class="input-group">
                <input type="text" class="form-control input-group-sm" placeholder="输入设备编号进行搜索">
                <span class="input-group-btn">
                        <button class="btn btn-default" type="button"><i class="fa fa-search"></i></button>
                    </span>
            </div>
        </div>
    </div>
```
2.表格区
```
    <div class="row mx-auto w-75">
        <div class="col-12">
            <table class="table table-hover table-success">
                <thead class="thead-default">
                <tr>
                    <th><input type="checkbox"></th>
                    <th>序号</th>
                    <th>设备编号</th>
                    <th>设备名称</th>
                    <th>设备状态</th>
                    <th>采购日期</th>
                    <th>设备管理员</th>
                </tr>
                </thead>
                <tbody>
                <tr v-for="(facility,index) in facilities">
                    <td><input type="checkbox" :value="index" v-model="checkedRows"></td>
                    <td>{{index+1}}</td>
                    <td>{{facility.code}}</td>
                    <td>{{facility.name}}</td>
                    <td>{{facility.states}}</td>
                    <td>{{facility.date}}</td>
                    <td>{{facility.admin}}</td>
                </tr>
                </tbody>
            </table>
        </div>
    </div>
```
> 
这里需要说明的是：
&emsp;1.表格table的class Bootstrap3和Boostrap4有所不同；
&emsp;2. vue.js for循环，vue1与vue2有所出入，尤其是下标index的使用。
以上两点我们在使用中需要根据自己的版本做相应调整了。

至此，展示表格数据的静态页面已经完成，接下来我们使用Vue.js使表格数据成为动态的。
### 五、 创建VUE对象、初始化表格数据
1.初始化数据
```
    var datas = [
        {
            code: "A2017-001",
            name: "3800充电器",
            states: "正常",
            date: "2017-01-21",
            admin: "andy"
        },
        {
            code: "A2017-002",
            name: "Lenovo Type-c转接器",
            states: "正常",
            date: "2017-01-21",
            admin: "zero"
        }];
```
> 
Tips: 
&emsp;datas在实际的场景中应当是通过ajax的方式访问后台获取的业务数据。

2.创建vue对象
```
    new Vue({
        el: "#vueApp",
        data: {
            checkAll: false,// 是否全选
            checkedRows: [],// 选中的行标，用于删除行
            facilities: datas,// 表格数据
            newRow:{}// 新增的行数据，用于新增行
        }
    })
```
ok，我们已经完成了表格数据的动态展示，下面我们来实现删除行数据功能。
### 六、删除行
删除按钮：
```
    <button type="button" class="btn btn-outline-warning btn-sm" @click="delRows">删除</button>
```
实现删除功能：
```
    delRows:function () {
                if (this.checkedRows.length <= 0){//是否选中判断
                    alert("您未选择需要删除的数据");
                    return false;
                }
                if (!confirm("您确定要删除选择的数据吗？")){//删除确认
                    return false;
                }

                for(var i=0;i<this.checkedRows.length;i++){//循环获取选中的行标
                    var checkedRowIndex = this.checkedRows[i];
                    /**根据下标移除数组元素*/
                    this.facilities = $.grep(this.facilities,function (facility,j) {
                        return j != checkedRowIndex;
                    });
                }
                this.checkedRows = [];//清空选中行数据
            }
```
实现效果：
![TIM截图20171126144003.png-73.3kB][2]

七、新增行
1.新增按钮
```
    <button type="button" class="btn btn-outline-info btn-sm" data-toggle="modal" data-target="#myModal">新增</button>
```
2.添加模态框用于录入新增数据
```
    <div class="modal fade" id="myModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h4 class="modal-title">新增设备信息</h4>
                    <button type="button" class="close" data-dismiss="modal">&times;</button>
                </div>
                <div class="modal-body">
                    <div class="row">
                        <div class="col-3">设备编号：</div>
                        <div class="col-9">
                            <input class="form-control" placeholder="设备编号" v-model="newRow.code">
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-3">设备名称：</div>
                        <div class="col-9">
                            <input class="form-control" placeholder="设备名称" v-model="newRow.name">
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-3">设备状态：</div>
                        <div class="col-9">
                            <input class="form-control" placeholder="设备状态" v-model="newRow.states">
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-3">采购日期：</div>
                        <div class="col-9">
                            <input class="form-control" placeholder="采购日期" v-model="newRow.date">
                        </div>
                    </div>
                    <div class="row">
                        <div class="col-3">管理员：</div>
                        <div class="col-9">
                            <input class="form-control" placeholder="管理员" v-model="newRow.admin">
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-outline-primary" data-dismiss="modal" @click="addRow">确认</button>
                </div>
            </div>
        </div>
    </div>
```
3.实现新增逻辑
```
    addRow: function () {
                this.facilities.push(this.newRow);//新行数据追加至表格数据数组中
                this.newRow = {};//新行数据置空
            }
```
![TIM截图20171126144708.png-63.1kB][3]

好了，动态展示、新增和删除功能就讲完了，后边有空我们再来讨论页面上未实现的全选、快速检索等功能。

### 附1：完整js
```
<script>
    var datas = [
        {
            code: "A2017-001",
            name: "3800充电器",
            states: "正常",
            date: "2017-01-21",
            admin: "andy"
        },
        {
            code: "A2017-002",
            name: "Lenovo Type-c转接器",
            states: "正常",
            date: "2017-01-21",
            admin: "zero"
        }];

    new Vue({
        el: "#vueApp",
        data: {
            checkAll: false,
            checkedRows: [],
            facilities: datas,
            newRow:{}
        },
        methods: {
            addRow: function () {
                this.facilities.push(this.newRow);
                this.newRow = {};
            },
            saveRows:function () {//保存表格数据
                
            },
            delRows:function () {
                if (this.checkedRows.length <= 0){
                    alert("您未选择需要删除的数据");
                    return false;
                }
                if (!confirm("您确定要删除选择的数据吗？")){
                    return false;
                }

                for(var i=0;i<this.checkedRows.length;i++){
                    var checkedRowIndex = this.checkedRows[i];
                    this.facilities = $.grep(this.facilities,function (facility,j) {
                        return j != checkedRowIndex;
                    });
                }
                this.checkedRows = [];
            }
        }
    });
</script>
```

  [1]: http://static.zybuluo.com/louie-001/ehc0ds1mhp1acegsvvq2z0w7/1.png
  [2]: http://static.zybuluo.com/louie-001/zxpi99wbsbiydn41b8mtgsjk/TIM%E6%88%AA%E5%9B%BE20171126144003.png
  [3]: http://static.zybuluo.com/louie-001/3z34kztky24ao6jjcqphnclw/TIM%E6%88%AA%E5%9B%BE20171126144708.png
