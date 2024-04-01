```bash
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <style>
    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 90vh;
    }
    .todo-list {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      width: 100%;
    }
  </style>
  <body>
    <div class="container">
      <h1>Todo List</h1>
      <add-todo></add-todo>
      <div class="todo-list" id="todo-list"></div>
    </div>
    <script>
      const template = document.createElement("template");
      template.innerHTML = `
  <style>
    input {
      padding: 10px;
      margin: 10px;
      width: 200px;
    }
    button {
      padding: 10px;
      margin: 10px;
      width: 100px;
      background-color: black;
      color: white;
    }
  </style>

  <div>
    
  </div>
  <input type="text" />
  <button>Add</button>
  
`;

      class AddTodo extends HTMLElement {
        constructor() {
          super();
          this.attachShadow({ mode: "open" });
          this.shadowRoot.appendChild(template.content.cloneNode(true));

          const addButton = this.shadowRoot.querySelector("button");
          addButton.addEventListener("click", this.handleAddTodo.bind(this));
        }

        handleAddTodo() {
          const input = this.shadowRoot.querySelector("input");
          const value = input.value;

          // Create a new div for the todo item
          const todoItem = document.createElement("div");

          // Add styles to the todo item
          todoItem.style.padding = "20px";
          todoItem.style.marginBottom = "10px";
          todoItem.style.backgroundColor = "#f9f9f9";
          todoItem.style.borderRadius = "5px";
          todoItem.style.display = "flex";
          todoItem.style.justifyContent = "space-between";
          todoItem.style.alignItems = "center";
          todoItem.style.width = "300px";

          // Create a span for the todo text
          const todoText = document.createElement("span");
          todoText.textContent = value;
          todoItem.appendChild(todoText);

          // Create a button for marking the todo as completed
          const completedButton = document.createElement("button");
          completedButton.textContent = "Completed";
          completedButton.addEventListener("click", function () {
            todoText.style.textDecoration = "line-through";
          });
          todoItem.appendChild(completedButton);

          // Create a button for deleting the todo
          const deleteButton = document.createElement("button");
          deleteButton.textContent = "Delete";
          deleteButton.addEventListener("click", function () {
            todoItem.remove();
          });
          todoItem.appendChild(deleteButton);

          // Add the new todo item to the todo list
          const todoList = document.querySelector("#todo-list");
          todoList.appendChild(todoItem);

          // Clear the input field
          input.value = "";
          // Create a "Delete All" button
        }
      }

      customElements.define("add-todo", AddTodo);
    </script>
  </body>
</html>
```
