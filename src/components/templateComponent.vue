<template>
	<!-- 
		定义组件的html模板 
		<router-view></router-view>		
		<componentsName></componentsName>
	-->

</template>

<script>
	// 定义局部变量
	export default {
		// 组件名
		name: 'compontName',
		props: {
			/*
			定义外部传入的数据如：

			num: {
				type: "number",   验证，可自定义函数，可缺省
				default: 100
			}

			*/
		},

		components: {
			/*子组件的引用*/
		}

		data: function{
			/*
			定义组件内部的全局变量，会绑定到this上， 与props互斥
			*/
			return {
				/*
					temp: default
				*/
			}
		}，

		methods: {
			/*
			定义组件内的函数8  
			需要一定的条件来触发（需要调用）
			定义时不要使用箭头函数
			*/
		},

		computed: {
			/*
			定义组件内的计算属性
			会在dom加载完后马上执行
			不适用箭头函数定义
			*/
		},

		watch: {
			/*
				定义监听函数
				不适用箭头函数定义
				例： 监听data中的A，然后改变data中的B
				A: function(newA, oldA) {
					this.B = A+1
					console.log(newA)
				}				
			*/
		}

		beforeCreate: function() {
			//生命周期函数，组件对象创建前被调用
		},

		created: function() {
			//生命周期函数，组件对象创建后被调用
		},

		beforeMount: function() {
			//生命周期函数，组件模板被渲染到dom前被调用
		},

		mounted: function() {
			//生命周期函数，组件模板被渲染到dom后被调用
		},

		beforeDestroy: function() {
			//生命周期函数，组件销毁前被调用
		},

		destroyed: function() {
			//生命周期函数，组件销毁后被调用
		},

		beforeUpdate: function() {
			//生命周期函数，data发生改变后，模板更新渲染前使用
		},

		updated: function() {
			//生命周期函数，data发生改变后，模板更新渲染后使用
		}



	}
</script>

<style>
	/*
	定义样式
	可通过 @import 引入样式文件
	*/

</style>