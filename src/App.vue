<template>
  <div id="app">
    <div class="mainbox">
      <div class="top">
        <div class="title">TODO LIST</div>
        <div><input type="text" id="add-list" name="add-list" placeholder="在这里输入希望添加的内容" @keyup.enter="addTodolist()"></div>
        <button id="clear-btn" @click="clear()">全部清除</button>
      </div>
      <div class="todo">
        <div class="cont">
          <div class="content content-up">
            <div class="items">
              <div class="choosenav">
                <span>进行中</span>
              </div>
              <div class="dContent" >
                <div class="doing-item" v-for="item in todolist" :key="item.id">
                  <ul id="todolist">
                    <li  class='dlist todolist'>
                 <input type='checkbox' @change="updatedone(item.id,true)" class='choose-box'> 
                 <!-- //通过onchange事件，当复选框值有改变，调用update函数，并改变输入数据“done”属性的布尔值 -->
                 <span :id="'sp-'+item.id" @click="edit(item.id)">{{item.todo}}</span> 
                 <!-- //点击事项调用edit函数 -->
                 <input type="text"  :id="'input-' + item.id" v-model="item.todo" style='display:none' @keyup.enter="confirm(item.id)" @blur="confirm(item.id)">
                 <a @click="remove()">-</a>
                 <!-- //点击“-”，调用remove函数 -->
                 </li>
                  </ul>
                </div>
              </div>
            </div>
          </div>
      <div class="content content-down">
        <div class="items">
          <div class="choosenav">
            <span>已完成</span>
          </div>
          <div class="dContent" >
            <div class="done-item" v-for="item in todolist" :key="item.id" >
              <div v-if="item.done">
                <ul id="donelist" >
                <li  class='dlist donelist'>
                 <input type='checkbox' @change="updatedone(item.id,false)" class='choose-box' checked> 
                 <!-- //通过onchange事件，当复选框值有改变，调用update函数，并改变输入数据“done”属性的布尔值 -->
                 <span :id="'sp-' + item.id">{{item.todo}}</span> 
                 <a @click="remove()">-</a>
                 <!-- //点击“-”，调用remove函数 -->
                 </li>
                </ul>
              </div>
              
            </div>
          </div>
        </div>
      </div>


        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      todolist: []
    }
  },
    mounted() {
    this.load(); //页面加载完毕调用load函数
  },
  methods: {
    addTodolist() {
      var obj_list = {
        id: this.randomID(),
        todo: "",   //存储用户输入的数据
        done: false     
      };
      var tempTodo = document.getElementById("add-list").value.trim(); //String切两边空格
      console.log('temp');
      if (tempTodo.length === 0){ //判断添加的内容是否为空
        return;
      }

      obj_list.todo = tempTodo;
      this.todolist.push(obj_list); //将 obj_list 用 push 方式放入 todolist
      this.saveData(this.todolist); // 将 todolist 存储进本地
      document.getElementById("add-list").value = "";     //初始化输入框
      load();     //将用户输入的数据添加至dom节点
      document.getElementById("add-list").focus();
    },

    saveData(data) {
      console.log('savein');
      localStorage.setItem("West2TodoList", JSON.stringify(data)); // JS数组转换成JSON对象存进本地
      console.log(JSON.stringify(data));

    },

    load(){
  
      var todo = document.getElementById("todolist");
      var done = document.getElementById("donelist");

      var todoString = "";
      var doneString = "";

      // document.getElementById("add-list").focus();// 将光标指向输入框

      let getlist = this.loadData();//调用loadData函数将本地存储数据调用进网页

      //若本地有相应存储数据，则将其添加至dom节点，反之初始化页面。
      if (getlist.length != 0){
        console.log('getlist not null');
        this.todolist = getlist;
        console.log(getlist);
      }

      else {
        console.log('getlist null');
        this.todolist = [
          {
          id: this.randomID(),
          todo: "设置你的todo清单!",   //存储用户输入的数据
          done: false     //确认是否被checked
          },
          {
          id: this.randomID(),
          todo: "点击此处可以直接修改!",   //存储用户输入的数据
          done: false     //确认是否被checked
          }
        ]
        console.log('init todo');
      }

    },

    loadData() {
      console.log('uuuuu');
      var hisTory = localStorage.getItem("West2TodoList");
      if(hisTory !=null){
        console.log('get');
        return JSON.parse(hisTory); // JSON对象转换为JS数组
      }

      else {
        console.log('no');
        return []
      }
    },

    clear() {
      console.log('ji');
      localStorage.clear(); // 清除本地缓存
      this.load(); // 重新加载一次
    },

    confirm(i) {
      var inputId = document.getElementById('input-'+i);
      var sp = document.getElementById('sp-' + i);
      let spContent = sp.innerHTML; // 保存原值
      inputId.setSelectionRange(0, inputId.value.length); // 选中文本框内的字符串
      if (inputId.value.length === 0) { // 如果用户更改后发现被清空
        sp.innerHTML = spContent; //将原来未修改的数据回推给sp
        alert("内容不能为空， 需要清除请点击右侧'-'号。");
      }

      else {
        console.log('confirmed!');
        this.updatetodo(i, inputId.value); //通过upadate函数对todolist数组相应项进行更新，将用户输入的内容写入到todolist数组相应项的todo属性中
        inputId.style.display = 'none';
        sp.style.display = 'block';
        this.load();
      }

    },

    updatetodo(i, value) {
      var id = this.todolist.findIndex(todolist => {
        if(todolist.id == i) {
					console.log('updatetodo');
          this.todolist.todo = value// 将第 i 个元素中的 todo 属性 改为 value 值
				}
      });
      
      this.saveData(this.todolist); // 覆盖原本地缓存
      this.load(); // 重新加载一次
    },

    updatedone(i, value) {
      var id = this.todolist.findIndex(todolist => {
        if(todolist.id == i) {
					console.log('updatedone');
          this.todolist.done = value// 将第 i 个元素中的 done 属性 改为 value 值
				}
      });
      
      this.saveData(this.todolist); // 覆盖原本地缓存
      this.load(); // 重新加载一次
    },

    remove(i) {
      var id = this.todolist.findIndex(todolist => {
        if(todolist.id == i) {
								todolist.splice(id, 1); // 删除位于 id 的 1 个元素
							}
      });
      this.saveData(this.todolist); // 覆盖原本地缓存
      this.load(); // 重新加载一次
    },

    edit(i) { // 这个函数用于直接修改list里的数据
      var sp = document.getElementById('sp-' + i);
      var inputId = document.getElementById('input-'+i);
      let spContent = sp.innerHTML; // 保存原值
      sp.style.display = 'none';
      inputId.style.display = 'block';
      inputId.value = spContent;
      console.log(inputId);
      inputId.focus();

    },

    randomID() {
      var id1 = Number(Math.random().toString().substr(2,0) + Date.now()).toString(10);
      return id1
    },

  }

  

}
</script>

<style>
  * {
    margin: 0;
    padding: 0;
    font-family: Arial, Helvetica, sans-serif;
}

body {
    background-image: url("./assets/picture/background.png");
    z-index: -3;
}

.mainbox {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    z-index: -2;
}

.top {
    width: 92vw;
    height: 8vh;
    z-index: 0;
    display: flex;
    justify-content: center;
    align-items: center;
}

.title {
    width: 30vw;
    font-size: 50px;
    color: rgba(65, 81, 83, 0.589);
    text-align: center;
    margin-right: 4vw;
}

#add-list {
    width: 60vw;
    height: 5vh;
    margin-right: 3vw;
    border-radius: 8px;
    border-color: rgba(255, 255, 255, 0.452);
}

#clear-btn {
    z-index: 6;
    width: 6vw;
    height: 5vh;
    border-radius: 8px;
    border-color: rgba(255, 255, 255, 0.452);
}

#clear-btn:active {
    border-color: rgba(255, 255, 255, 0);
}

.todo {
    display: flex;
    z-index: 3;
    width: 92vw;
    height: 88vh;
    position: relative;
}

.todo::after {
    content: "";
    width: inherit;
    height:  inherit;
    background: inherit;
    background-color: rgba(255, 255, 255, 0.452);
    filter: blur(2px);
    z-index: 0;
    position: absolute;
    
}

.cont {
    height: 100%;
    width: 100%;
    z-index: 1;
    display: flex;
    flex-direction: column;

}

.content {
    height: 50%;
}

.content-up {
    border-bottom: 2px solid rgba(255, 255, 255, 0.815);
}

.items {
    width: inherit;
    height: 100%;
    display: flex;
    flex-direction: row;
  }

.choosenav {
    display: flex;
    width: 18vw;
    height: 100%;
    z-index: 1;
    background-color: rgba(255, 255, 255, 0.479);
    text-align: center;
    align-items: center;
    justify-content: center;
    border-right: 1px solid rgba(255, 255, 255, 0.815);
}

.choosenav>span {
    font-size: 50px;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    writing-mode: vertical-lr;
}

.dContent {
    width: 80vw;
    margin: 0 5px 0 5px;
    overflow: auto;
}

.dContent li {
    display: flex;
    text-align: center;
    align-items: center;
    font-size: 20px;
}

.dContent::-webkit-scrollbar {
    width: 0px;
}

  .dlist {
      width: inherit;
      height: 6vh;
      list-style: none;
      background-color: rgba(255, 255, 255, 0.692);
      margin-top: 1vh;
  }

  .dlist a {
    margin-left: auto;
    margin-right: 12px;
    border: 1px;
    border-radius: 50%;
    width: 16px;
    height: 16px;
    font-size: 32px;
    text-align: center;
    line-height: 12px;
    color: rgb(247, 66, 66);
    font-weight: bolder;
    cursor: pointer;
    background-color: rgba(37, 45, 46, 0.692)
  }

  .choose-box {
      width: 25px;
      height: 25px;
      margin: 0 5px 0 5px;
  }

  .donelist span {
      color: rgba(65, 81, 83, 0.589);
      text-decoration: line-through;
  }

</style>
