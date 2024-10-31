<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button
          type="button"
          class="todoapp__toggle-all"
          data-cy="ToggleAllButton"
          onClick="{onHandleToggleAllTodos}"
        ></button>

        <form>
          <input
            data-cy="NewTodoField"
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            ref="{inputRef}"
            disabled="{isSubmiting}"
            onKeyDown="{onHandleAddingTodo}"
            onChange="{handleInputOnChange}"
            value="{value}"
          />
        </form>
      </header>

      <section class="todoapp__main" data-cy="TodoList">
        <div data-cy="Todo" class="todo">
          <label class="todo__status-label">
            <input
              data-cy="TodoStatus"
              type="checkbox"
              class="todo__status"
              onChange="{handleTodoChecked}"
              checked="{todo.completed}"
            />
          </label>

          <form onSubmit="{handleSubmitTodo}">
            <input
              data-cy="TodoTitleField"
              type="text"
              class="todo__title-field"
              placeholder="Empty todo will be deleted"
              value="{editTodoValue}"
              onBlur="{handleBlurTodo}"
              onKeyUp="{handleEscapeEdit}"
              ref="{titleInput}"
            />
          </form>
          <div data-cy="TodoLoader" class="modal overlay">
            <div class="modal-background has-background-white-ter" />
            <div class="loader" ></div>
          </div>
        </div>
      </section>

      <footer class="todoapp__footer" data-cy="Footer">
        <span class="todo-count" data-cy="TodosCounter">
          {activeListLength} items left
        </span>

        <nav class="filter" data-cy="Filter">
          <a
            key="{filterKey}"
            href="#/"
            class="filter__link"
            data-cy="{filterDataAtributes[filterValue]}"
            onClick="{handleFilter}"
          >
            All
          </a>
          <a
            key="{filterKey}"
            href="#/"
            class="filter__link"
            data-cy="{filterDataAtributes[filterValue]}"
            onClick="{handleFilter}"
          >
            Active
          </a>
          <a
            key="{filterKey}"
            href="#/"
            class="filter__link"
            data-cy="{filterDataAtributes[filterValue]}"
            onClick="{handleFilter}"
          >
            Completed
          </a>
        </nav>

        <button
          type="button"
          class="todoapp__clear-completed"
          data-cy="ClearCompletedButton"
          onClick="{handleDeleteComplitedTodo}"
        >
          Clear completed
        </button>
      </footer>
    </div>
    <ErrorNotification error="{error}" onHandleHideError="{handleHideError}" />
  </div>
</template>
