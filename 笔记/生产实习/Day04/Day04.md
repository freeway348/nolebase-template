# Day04

---

### 一、修改用户信息

1. 在前端添加修改用户对话框：
```html
<!--修改用户对话框-->
			<el-dialog title="修改用户" :visible.sync="updateUserVisible">
			  <el-form :model="updateUser"> <!--初始是个空对象，在其中加入几个变量，它就存储了多少个变量-->
			    <el-form-item label="用户名" :label-width="formLabelWidth">
			      <el-input v-model="updateUser.username" autocomplete="off"></el-input>
			    </el-form-item> <!--添加属性到updateUser中-->
				<el-form-item label="密码" :label-width="formLabelWidth">
				  <el-input v-model="updateUser.password" autocomplete="off" show-password></el-input>
				</el-form-item>
				<el-form-item label="邮箱" :label-width="formLabelWidth">
				  <el-input v-model="updateUser.email" autocomplete="off"></el-input>
				</el-form-item>
				
			    <el-form-item label="性别" :label-width="formLabelWidth">
			      <el-select v-model="updateUser.sex" placeholder="请选择">
			        <el-option label="男" value="男"></el-option> <!--label是能看到的值，最终能存入数据库的就是value中的值-->
			        <el-option label="女" value="女"></el-option>
			      </el-select>
			    </el-form-item>
				
				<el-form-item label="年龄" :label-width="formLabelWidth">
				  <el-input v-model="updateUser.age" autocomplete="off"></el-input>
				</el-form-item>
				<el-form-item label="住址" :label-width="formLabelWidth">
				  <el-input v-model="updateUser.address" autocomplete="off"></el-input>
				</el-form-item>
				
				
			  </el-form>
			  <div slot="footer" class="dialog-footer">
			    <el-button @click="cancelUpdateUser">取 消</el-button>
			    <el-button type="primary" @click="modifyUser">确 定</el-button>
			 <!--当点击"取消"时，触发cancelUpdateUser的方法；当点击"确定"时触发modifyUser的方法-->
			  </div>
			</el-dialog>
```
实现效果：![](assets/Pasted%20image%2020250627155634.png)
2. 实现method方法cancelUpdateUser和modifyUser：
```html
method:{
	cancelUpdateUser(){
	// 1. 直接关闭修改用户对话框
	this.updateUserVisible=false;
	// 因为修改的是scope.row，而scope.row对应的是表单里的数据，所以该修改对表单也进行了修改，取消后表单会显示原先想要修改的内容，但刷新后会消失（因为并未载入数据库中）
	// 2. 刷新列表，解决取消后表格数据依然被修改的问题
	this.getUserList();
	},


	modifyUser(){ // 将修改的数据提交到后台，到后台去保存
		axios.post("http://localhost:8088/user/update", this.updateUser)
		.then(result=>{
			let data = result.data;
			if (data.code == 200){
				this.$message.success(data.msg);
				this.updateUserVisible=false;
			}else{
				this.$message.error(data.msg);
			
			}
		})
	}
}
```
3. 在/user/UserController中添加路径"/update"：
```Java
@PostMapping("/update")
    public JSONObject update(@RequestBody User user){
        boolean result = userService.updateById(user); // mybatis-plus的方法，在单表中mybatis-plus比mybatis方便，但在多表中这俩差不多
        // 使用预加载，将数据预先存储到redis中，比用mysql语句查询的实时性会更好，适合在实时性要求高时使用
        JSONObject obj = new JSONObject();
        if (result){
            obj.put("code", 200);
            obj.put("msg", "修改用户成功");
        }else {
            obj.put("code", 500);
            obj.put("msg", "修改用户失败");
        }

        return obj;
    }
```


### 二、删除用户信息

```
与修改用户信息的内容相差无几
```


1. 对"删除"按钮进行点击时触发事件"deleteUser(scope.row.id)"，即触发事件deleteUser，并将参数scope.row.id（该行记录的id）传入method函数
```html
<el-button type="text" size="small" @click="deleteUser(scope.row.id)">删除</el-button>
```
```html
deleteUser(id){
	// 1. 通过确认窗来确认是否要删除 ----- 防止误删除
	this.$confirm('确定要删除该条记录吗?', '提示',{
	  confirmButtonText: '确定',
	  cancelButtonText: '取消',
	  type: 'warning'
	}).then(() => { // 点击确定后实现的是.then方法里的内容
	  // 2. 当确认要删除时，发送请求，然后处理返回结果
		let param = {};
		param.id = id;
		// {params:param}：使用Get方式的形式传入，否则无法将数据传送给后台，也就无法执行删除操作
		// {params:xxxxx}：固定写法
		axios.delete("http://localhost:8088/user/delete", { params: param })
		.then(result => {
			let data = result.data;
			if (data.code == 200) {
				this.$message.success(data.msg);
				this.getUserList();
			} else {
				this.$message.error(data.msg);
			}
		});
	}).catch(() => {
		this.$message({
			type: 'info',
			message: '已取消删除'
			});
		});
	}
}
```
1. 对UserController进行内容添加：
```Java
@DeleteMapping("/delete") // 前端调用axios.delete需要改Mapping导向路径  
public JSONObject delete(Integer id){  
  
    boolean result = userService.removeById(id);  
  
    // 如果给多张表进行操作，则需要添加事务，否则可能会出现数据不一致的情况（可能一张表成功，另一张表却失败，此时需要回滚，否则无法完成任务）  
  
    JSONObject obj = new JSONObject();  
  
    if (result){  
        obj.put("code", 200);  
        obj.put("msg", "删除用户成功");  
    }else {  
        obj.put("code", 500);  
        obj.put("msg", "删除用户失败");  
    }  
  
    return obj;  
}
```


### 三、前后端分离的问题

会产生跨域访问的问题，但这是浏览器本身的问题，因为浏览器有同源策略，默认情况下只允许域名相同的url互相访问，域名不相同的不允许访问，这种情况只需要在后端实现好运行跨域访问的允许即可

 