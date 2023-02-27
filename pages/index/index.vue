<template>
	<view class="content">
		<input class="todo-input" focus placeholder="添加 Todo" :value="inputValue" @confirm="addTodo"/>
		<checkbox-group class="todo-item-container" @change="checkboxgroupChangeHandler">
			<label class="todo-item" v-for="(todo, index) in todos" :key="todo.time">
				<checkbox class="todo-item-checkbox" :checked="todo.isdone" :value="todo.time"></checkbox>
				<text class="todo-content">{{todo.content}}</text>
			</label>
		</checkbox-group>
	</view>
</template>

<script>
	export default {
		// @FIXME: input 输入框，数据录入后，清空 input。当 inputValue 非空，无问题；当 inputValue 为空，就无法清空 input。
		data() {
			return {
				todos:[
				],
				inputValue:''
			}
		},
		onLoad() {
			this.todos = (uni.getStorageSync('todos') || []).map(todo => {
		        return {
		          time: todo.time,
		          content: todo.content,
		          isdone: todo.isdone
		        }
		      });
		},
		methods: {
			addTodo(e) {
				console.log('addTodo', e);
				
				const todos = uni.getStorageSync('todos') || [];
				todos.push({
					time: (new Date()).getTime(),
					content: e.detail.value,
					isdone: false
				});
			
				const sortedDodos = this.sortTodos(todos);
				uni.setStorageSync('todos', sortedDodos);
			
				this.inputValue = '';
				this.todos = sortedDodos;
			},
			checkboxgroupChangeHandler(e) {
			    const checkeds = e.detail.value;
			    console.log('checkboxgroupChangeHandler', e, checkeds);
			
			    const todos = uni.getStorageSync('todos') || [];
			    for (let i = (todos.length - 1); i >= 0 ; i--) {
			      const todo = todos[i];
			      todo.isdone = false;
			      for (let j = (checkeds.length - 1); j >= 0 ; j--) {
			        const checked = checkeds[j];
			        if (todo.time == checked) {
			          todo.isdone = true;
			          break;
			        }
			      }
			    }
			    const sortedDodos = this.sortTodos(todos);
			    uni.setStorageSync('todos', sortedDodos);
			
				this.todos = sortedDodos;
			  },
			sortTodos(todos){
			    // sort 内的函数返回值小于 0， firstElement 排在 secondElement 前面；
			    // 返回值等于 0， firstElement，secondElement 相等顺序无关要紧； 
			    // 返回值大于 0， firstElement 排在 secondElement 后面；
			    return todos.sort(function(a, b){
			      if (a.isdone && !b.isdone) {
			        return 1;
			      }
			      if (!a.isdone && b.isdone) {
			        return -1;
			      }
			      return a.time - b.time;
			    });
			  }
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}
	.todo-input{
		border:1px solid #cccccc;
		width:100%;
		height:45px;
	}
	.todo-item-container {
	  display: flex;
	  flex-direction: column;
	  padding: 40rpx;
	}
	
	.todo-item-checkbox {
	    margin-right:10px;
	}
	
	.todo-item {
	    margin-bottom:10px;
	}
</style>
