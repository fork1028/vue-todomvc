<template>
  <div class="todomvc">
    <div class="topbar">
      <div class="toggle" @click="toggleAll">v</div>
      <input
        class="input"
        v-model="input"
        @keyup.enter="submit"
        placeholder="What needs to be done?"
      />
    </div>

    <div
      class="todo"
      @mouseover="display(index)"
      @mouseleave="undisplay(index)"
      v-for="(item, index) in filteredlist"
      :key="item.key"
    >
      <input
        class="checkbox"
        type="checkbox"
        v-model="item.checked"
        @change="updateStatus(item)"
      />
      <div v-show="item.isEditing == false">
        <span
          v-bind:class="{ done: filteredlist[index].isRemoved == true }"
          class="item"
          @dblclick="item.isEditing = true"
          >{{ item.input }}</span
        >
      </div>
      <input
        class="revise"
        v-show="item.isEditing == true"
        v-model="item.input"
        @blur="item.isEditing = false"
        @keyup.enter="item.isEditing = false"
        @keyup.esc="item.isEditing = false"
        v-myfocus
      />

      <button
        class="remove"
        v-show="filteredlist[index].isDisplayed == false"
        style="color:white"
      >
        x
      </button>
      <button
        class="remove"
        v-show="filteredlist[index].isDisplayed == true"
        @click="removeTodo(item)"
      >
        x
      </button>
    </div>

    <div class="bar" v-if="todolist.length > 0">
      <div class="numOfItems">
        {{ pluralize(todolist.length - numOfCompleted) }}
      </div>
      <button v-bind:class="{ active: isAll }" class="btn" @click="showAll">
        All
      </button>
      <button
        v-bind:class="{ active: isActive }"
        class="btn"
        @click="showActive"
      >
        Active
      </button>
      <button
        v-bind:class="{ active: isCompleted }"
        class="btn"
        @click="showCompleted"
      >
        Completed
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      input: "",
      todolist: [],
      edited: "",
      filteredlist: [],
      status: "",
      isAll: false,
      isActive: false,
      isCompleted: false,
      numOfCompleted: 0,
      key: 0,
      isDuplicate: false,
      isToggled: false,
    };
  },
  methods: {
    submit() {
      this.createTodo(this.input);
      this.input = "";
    },
    createTodo(input) {
      if (input !== "") {
        for (var i = 0; i < this.todolist.length; i++) {
          if (this.todolist[i].input == input) {
            this.isDuplicate = true;
          }
        }
        if (this.isDuplicate === false) {
          this.todolist.push({
            input: input,
            checked: false,
            isEditing: false,
            isRemoved: false,
            isDisplayed: false,
            key: this.key,
          });
        }
      }
      this.isDuplicate = false;
      this.key++;
      this.refresh();
    },
    pluralize(count) {
      if (count === 1) {
        return "1 item left";
      }
      if (count === 0) {
        return "0 item left";
      } else {
        return count + " items left";
      }
    },
    removeTodo(input) {
      var index = this.todolist.indexOf(input);
      if (this.todolist[index].isRemoved == true) {
        this.numOfCompleted--;
      }
      this.todolist.splice(index, 1);
      this.refresh();
    },
    editTodo(todo) {
      this.editedTodo = todo;
    },
    toggleAll() {
      if (this.isToggled == false) {
        for (var i = 0; i < this.todolist.length; i++) {
          this.todolist[i].checked = true;
          this.isToggled = true;
          this.todolist[i].isRemoved = true;
          this.numOfCompleted = this.todolist.length;
        }
      } else {
        for (var j = 0; j < this.todolist.length; j++) {
          this.todolist[j].checked = false;
          this.isToggled = false;
          this.todolist[j].isRemoved = false;
          this.numOfCompleted = 0;
        }
      }
    },

    showCompleted() {
      this.status = "completed";
      this.filteredlist = this.todolist.filter((item) => {
        return item.checked == true;
      });
      this.isCompleted = true;
      this.isAll = false;
      this.isActive = false;
    },
    showAll() {
      this.status = "all";
      this.filteredlist = this.todolist;
      this.isAll = true;
      this.isCompleted = false;
      this.isActive = false;
    },
    showActive() {
      this.status = "active";
      this.filteredlist = this.todolist.filter((item) => {
        return item.checked == false;
      });
      this.isActive = true;
      this.isAll = false;
      this.isCompleted = false;
    },
    updateStatus(input) {
      var index = this.todolist.indexOf(input);
      if (this.todolist[index].checked == true) {
        this.todolist[index].isRemoved = true;
        this.numOfCompleted++;
      } else {
        this.todolist[index].isRemoved = false;
        this.numOfCompleted--;
      }
      this.refresh();
    },
    refresh() {
      switch (this.status) {
        case "completed":
          this.showCompleted();
          break;
        case "all":
          this.showAll();
          break;
        case "active":
          this.showActive();
          break;
        default:
      }
    },
    display(index) {
      this.filteredlist[index].isDisplayed = true;
    },
    undisplay(index) {
      this.filteredlist[index].isDisplayed = false;
    },
  },
  directives: {
    myfocus: {
      // 当绑定元素插入到 DOM 中
      inserted: function(el) {
        // 聚焦元素
        el.focus();
      },
      update(el) {
        el.focus();
      },
    },
  },
  created() {
    this.showAll();
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.topbar {
  display: flex;
}

.toggle {
  position: fixed;
  margin: 70px;
  margin-left: 87px;
  margin-top: 77px;
  color: lightgray;
  font-size: 20px;
}

.input {
  margin: 70px;
  margin-bottom: 0px;
  height: 40px;
  width: 300px;
  border: 1px solid lightgray;
  padding-left: 40px;
}

.todo {
  display: flex;
  width: 342px;
  margin-left: 70px;
  border: 1px solid lightgray;
  height: auto;
  border-top: 0px;
  align-items: center;
  justify-content: space-between;
}

.checkbox {
  margin-left: 18px;
}

.item {
  padding-right: 50px;
  padding-left: 20px;
  margin-top: 10px;
  margin-bottom: 10px;
  width: 200px;
  display: inline-block;
  text-align: left;
  word-break: break-all;
}

.revise {
  padding-right: 50px;
  padding-left: 20px;
  width: 150px;
  display: inline-block;
  height: 30px;
}

.remove {
  border: none;
  margin: 0px;
  height: 20px;
  width: 20px;
  display: flex;
  margin-right: 20px;
  color: darkred;
  font-size: 15px;
  background-color: white;
}

.bar {
  display: flex;
  align-items: center;
}

.numOfItems {
  margin: 20px;
  margin-left: 75px;
  font-size: 14px;
}

.btn {
  border: 1px solid gray;
  margin: 15px;
  height: 20px;
  display: flex;
  background-color: white;
  color: gray;
  border: 1px solid white;
  border-radius: 3px;
}

.active {
  border: 1px solid darkred;
}

.done {
  text-decoration: line-through;
  color: lightgray;
}
</style>
