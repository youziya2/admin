<template>
	<div id="home">
		<!-- 左边侧边栏 -->
		<div class="lfte">
			<!-- 			<el-row class="tac">
				<el-col :span="12"> -->
			<el-menu default-active="1-4-1" class="el-menu-vertical-demo" @open="handleOpen" @close="handleClose" :collapse="isCollapse"
			 background-color="#545c64" text-color="#fff" active-text-color="#ffd04b" :default-openeds="openeds">
				<el-row class="pic">
					<!-- <span> -->
						<img src="../assets/logo.png">
					<!-- </span> -->
				</el-row>
				<!-- <p>智学无忧教育管理系统</p> -->
				<!-- 双重for循环 -->
				<!-- 此方法中index如果不加''+符号会报got number的错误 -->

				<el-submenu v-for="(itme,index) in content" :key="index" :index="''+index">
					<template slot="title">
						<i class="el-icon-location"></i>
						<span slot="title">{{itme.name}}</span>
					</template>
					<el-menu-item v-for="(itmeson,index) in itme.basics" :key="index" @click="clickMenu(itmeson)" :index="itmeson.routers"
					 :id="itmeson.routers">
						{{itmeson.manage}}
					</el-menu-item>
				</el-submenu>
			</el-menu>
			<!-- 			  </el-col>
</el-row>
 -->
		</div>
		<div class="content">
			<!-- 导航栏 -->
			<div class="title">
				<!-- 折叠栏 -->
				<div class="title-left">
					<el-tooltip class="tooltip" effect="dark" :content="isCollapse?'展开':'收起'" placement="right">
						<el-button @click="isCollapse=!isCollapse" :icon="isCollapse?'el-icon-s-unfold':'el-icon-s-fold'" style="padding:15px" />
					</el-tooltip>
				</div>
				<!-- 头部导航栏 -->
				<div class="title-rigth">
					<el-tabs v-model="editableTabsValue" type="card" @tab-remove="removeTab" @tab-click="handleClickTab">
						<el-tab-pane :key="item.name" v-for="(item,index) in editableTabs" :label="item.title" :name="item.name"
						 :closable="index>0">
							{{item.content}}
						</el-tab-pane>
					</el-tabs>
				</div>
				<div class="last">
					<el-col :span="12">
					<el-dropdown trigger="click">
						<span class="el-dropdown-link">王小虎</span>
						<el-dropdown-menu slot="dropdown">
							<el-dropdown-item>我的消息</el-dropdown-item>
							<el-dropdown-item>设置</el-dropdown-item>
							<el-dropdown-item @click.native="logout">退出登录</el-dropdown-item>
						</el-dropdown-menu>
					</el-dropdown>
					</el-col>
				</div>
				<div class="bod">
					<router-view />
				</div>
			</div>
		</div>
	</div>

</template>

<script>
	export default {
		name: 'home',
		data() {
			return {
				openeds: ['0', '1'], //默认展开侧边栏
				isCollapse: false, //默认侧边栏展开状态
				Srray: [], //空数组接收
				editableTabsValue: '/', //默认加载页面
				editableTabs: [{
					title: '首页',
					name: '/',
					content: ''
				}],
				tabIndex: 1,
				// 双重v-for循环格式
				content: [{
						name: '基础信息',
						basics: [{
								manage: '首页',
								routers: "/"
							}, {
								manage: '学生管理',
								routers: "student"
							},
							{
								manage: '教师管理',
								routers: "teacher"
							},
							{
								manage: '班级管理',
								routers: "grade"
							},
							{
								manage: '修改密码',
								routers: "amend"
							}
						],
					},
					{
						name: '考试系统',
						basics: [{
								manage: '布置试卷',
								routers: "paper"
							},
							{
								manage: '安排考试',
								routers: "exam"
							},
							{
								manage: '批阅试卷',
								routers: "read"
							},
							{
								manage: '查看成绩',
								routers: "examine"
							},
							{
								manage: '维护试卷',
								routers: "maintain"
							}
						],
					}
				],
			}
		},
		//此处用created（创建后） dom并没有加载
		mounted() {
			setTimeout(_ => {
				let that = this;
				// forEach循环遍历数组
				that.content.forEach(item => {
					item.basics.forEach(item => {
						that.Srray.push(item);
					});
				});
				let EditableTabs = JSON.parse(sessionStorage.getItem("editableTabs")); //用变量接收得到存储的tab内容
				let TabName = sessionStorage.getItem("TabName"); //得用变量接收得到存储的tab的name
				// console.log(TabName)
				//如果存在sessionStorage数据,改变其结果
				if (EditableTabs && TabName) {
					that.editableTabs = EditableTabs;
					that.editableTabsValue = TabName;
					for (var m = 0; m < that.content.length; m++) {
						for (var n = 0; n < that.content[m].basics.length; n++) {
							var ser = that.content[m].basics;
							// console.log(ser[n].routers)
							document.getElementById(ser[n].routers).style.color = "#fff" //所有样式全部变成白色
							// var a = document.getElementById(ser[n].routers)
							// console.log(a)
						}
					}
					document.getElementById(TabName).style.color = "rgb(255, 208, 75)" //当前样式重新覆盖
				}

			}, );
		},
		methods: {
			/**
			 * 退出登录
			 */
			logout: async function() {
				var that = this;
				this.$confirm('确认退出吗?', '提示', {
					//type: 'warning'
				}).then(() => {
					sessionStorage.clear()
					that.$router.push('/login');
				}).catch(() => {})
			},
			handleOpen(key, keyPath) {
				console.log(key, keyPath);
			},
			handleClose(key, keyPath) {
				console.log(key, keyPath);
			},
			/**
			 * 点击导航栏跳路由
			 */
			handleClickTab(name) {
				// console.log(name)
				for (var m = 0; m < this.content.length; m++) {
					for (var n = 0; n < this.content[m].basics.length; n++) {
						var ser = this.content[m].basics;
						// console.log(ser[n].manage)
						if (ser[n].manage == name.label) {
							console.log(ser[n])
							this.clickMenu(ser[n]); //调用菜单点击方法达到颜色变化监听效果
							sessionStorage.setItem("TabName", ser[n].routers);
							this.$router.push(ser[n].routers)
						}
					}
				}
			},
				/**
			 * 菜单打开页面
			 */
			clickMenu(menu) {
				for (var m = 0; m < this.content.length; m++) {
					for (var n = 0; n < this.content[m].basics.length; n++) {
						var ser = this.content[m].basics;
						// console.log(ser[n].routers)
						document.getElementById(ser[n].routers).style.color = "#fff" //所有样式全部变成白色
					}
				}
				document.getElementById(menu.routers).style.color = "rgb(255, 208, 75)" //当前样式重新覆盖
				console.log(menu)
				var exist = false;
				//判断名字是否相同	
				for (var i = 0; i < this.editableTabs.length; i++) {
					if (menu.manage == this.editableTabs[i].title) {
						exist = true;
						break;
					}
				}
				//如果名字相同就跳路由回去
				if (exist == true) {
					this.editableTabsValue = menu.routers;
					this.$router.push(menu.routers)
					sessionStorage.setItem(
						"editableTabs",
						JSON.stringify(this.editableTabs)
					); //添加存储用户操作的tab内容
					sessionStorage.setItem("TabName", menu.routers); //存储menu.routers,这里需要的是editableTabs数组中name
					return;
				}
				this.editableTabs.push({ //把新窗口的信息push进editableTabs数组
					title: menu.manage,
					name: menu.routers
				});
				this.editableTabsValue = menu.routers;
				this.$router.push(menu.routers) //跳路由
				sessionStorage.setItem(
					"editableTabs",
					JSON.stringify(this.editableTabs)
				); //添加存储用户操作的tab内容
				sessionStorage.setItem("TabName", menu.routers); //存储menu.routers,这里需要的是editableTabs数组中name
			},
					/**
			* 删除tabs
			*/
			removeTab(targetName) {
				console.log(targetName)
				let tabs = this.editableTabs
				let activeName = this.editableTabsValue
				if (activeName === targetName) {
					tabs.forEach((tab, index) => {
						if (tab.name === targetName) {
							let nextTab = tabs[index + 1] || tabs[index - 1]
							for (var m = 0; m < this.content.length; m++) {
								for (var n = 0; n < this.content[m].basics.length; n++) {
									var ser = this.content[m].basics;
									// console.log(ser[n].routers)
									document.getElementById(ser[n].routers).style.color = "#fff" //所有样式全部变成白色
								}
							}
							document.getElementById(nextTab.name).style.color = "rgb(255, 208, 75)" //当前样式重新覆盖
							console.log(nextTab) //查看页面名称
							if (nextTab) {
								activeName = nextTab.name
							}
						}
					})
				}
				this.$router.push(activeName)
				this.editableTabsValue = activeName
				this.editableTabs = tabs.filter(tab => tab.name !== targetName)
				sessionStorage.setItem("editableTabs", JSON.stringify(this.editableTabs));
				sessionStorage.setItem("TabName", activeName);
			}
		}
	}
</script>
<style scoped="scoped" lang="less">
	.el-dropdown-menu__item{
		width: 80px !important;
	}
	.pic {
		position: relative;
		height: 60px;
		}
		span {
			width: 60px;
			display: inline-block;
		}
	
		img {
		width: 50px !important;
		max-width: 50px !important;
	}
	.el-col-4 {
		width: 100%;
	}
	.el-menu-vertical-demo:not(.el-menu--collapse) {
		width: 200px;
		min-height: 100vh;
	}
	/deep/.el-tabs__item {
		height: 44px !important;
	}
	.el-icon-s-unfold,
	.el-icon-s-fold {
		width: 50px;
		height: 50px;
		font-size: 50px
	}
	.last {
		float: right;
		width: 9%;
		height: 56px;
		line-height: 56px;
	}
	@media only screen and (max-width: 1200px) {
		.last {
			display: none
		}
	}
	.title-left {
		height: 56px;
		width: 46px;
		float: left;
	}
	.title-rigth {
		width: 85%;
		float: left;
	}
	.title {
		width: 100%;
		float: right;
	}
	/* .el-tabs{float: right;} */
	.el-col-12 {
		width: 100%;
	}
	.basics,.test {
		cursor: pointer;
	}
	ul,li {
		padding: 0px;
		margin: 0px;
	}
	ul li {
		list-style: none;
	}
	.lfte {
		float: left;
		height: 98vh;
	}
	.el-menu-vertical-demo {
		height: 100%;
		overflow-x: hidden;
	}
	.content {
		display: flex;
	}
	.bod {
		width: 100%;
		float: left;
	}
	h5 {
		text-align: center;
	}
	/deep/.el-radio-group {
		text-align: left;
	}
	.el-menu-item:hover {
		background: #fff !important;
		color: #000000 !important;
	}
</style>
