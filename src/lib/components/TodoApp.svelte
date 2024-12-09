<script lang="ts">
  type Todo = {
    text: string;
    done: boolean;
  };
  type Filters = "all" | "active" | "completed";

  let todos = $state<Todo[]>([]);
  let filter = $state<Filters>("all");
  let filteredTodos = $derived(filterTodos(todos));

  $effect(() => {
    console.log(todos);
    console.log(filter);
  });

  function addTodo(event: KeyboardEvent) {
    if (event.key != "Enter") return;

    const todoEl = event.target as HTMLInputElement;
    const text = todoEl.value;
    const done = false;

    todos = [...todos, { text, done }];

    todoEl.value = "";
  }

  function editTodo(event: Event) {
    const inputEl = event.target as HTMLInputElement;
    const index = +inputEl.dataset.index!;
    todos[index].text = inputEl.value;
  }

  function toggleTodo(event: Event) {
    const inputEl = event.target as HTMLInputElement;
    const index = +inputEl.dataset.index!;
    todos[index].done = !todos[index].done;
  }

  function setFilter(newFilter: Filters) {
    filter = newFilter;
  }

  function filterTodos() {
    switch (filter) {
      case "all":
        return todos;

      case "active":
        return todos.filter((todo) => !todo.done);

      case "completed":
        return todos.filter((todo) => todo.done);
    }
  }

  function remaining() {
    return todos.filter(todo => !todo.done).length
  }
</script>
<h1 class="text-3xl text-white font-bold mb-10 m-0">Todo App</h1>
<input
  class="bg-white w-24 h-10 text-black font-semibold rounded-md"
  onkeydown={addTodo}
  placeholder="Add Todo"
  type="text1"
/>

<div class="todos">
  {#each filteredTodos as todo, i}
    <div class:completed={todo.done} class="todo border border-black">
      <input oninput={editTodo} data-index={i} value={todo.text} type="text" />
      <input 
        onchange={toggleTodo}
        data-index={i}
        value={todo.done}
        type="checkbox"
      />
    </div>
  {/each}
</div>

<div class="filters">
  <button
    class="bg-white w-12 h-10 text-black font-semibold rounded-md"
    onclick={() => setFilter("all")}>All</button
  >
  <button
    class="bg-white w-16 h-10 text-black font-semibold rounded-md"
    onclick={() => setFilter("active")}>Active</button
  >
  <button
    class="bg-white w-24 h-10 text-black font-semibold rounded-md"
    onclick={() => setFilter("completed")}>Completed</button
  >
</div>

<p>{remaining()} remaining</p> 

<style>
  .todos {
    
    display: grid;
    gap: 1rem;
    margin-block-start: 1rem;
  }
  .todo {
    position: relative;
    transition: opacity 0.3s;
  }
  .completed {
    opacity: 0.4;
  }
  input[type="text"] {
    width: 100%;
    padding: 1rem;
    height: 45px;
    border-radius: 0.125rem;
  }

  input[type="checkbox"] {
    position: absolute;
    right: 4%;
    top: 50%;
    translate: 0% -50%;
  }
  input[type="text1"] {
    width: 50%;
    padding: 1rem;
  }
  .filters {
    margin-block: 1rem;
  }
</style>
