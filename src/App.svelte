<script lang="ts">
  import axios from "./helpers/axios";
  let createInput = "";
  let isError = "";
  let toDoS = [];
  let activeInputs = {};
  let titleInput = "";
  let titleId = "";
  function handleInput(event) {
    createInput = event.target.value;
  }

  function handleInputTitle(event) {
    titleInput = event.target.value;
    titleId = event.target.id;
  }
  function isChanged(event, item) {
    if (event.target.value !== item) {
      event.target.value = item;
    }
  }
  async function handelSubmit(event) {
    event.preventDefault();
    const data = {
      title: createInput,
      description: "",
    };
    if (createInput) {
      try {
        await axios.post("/todos", data);
        createInput = "";
      } catch (error) {
        isError = error;
      }
    }
    fetchToDos();
  }

  async function getToDos() {
    const res = await axios.get("/todos");
    return res.data;
  }

  async function fetchToDos() {
    try {
      const todos = await getToDos();
      toDoS = todos;
    } catch (error) {
      isError = error;
    }
  }

  async function changeTitle(event) {
    event.preventDefault();
    try {
      await axios.patch(`/todos/${titleId}`, {
        title: titleInput,
      });
    } catch (error) {
      isError = error;
    }
    fetchToDos();
  }

  fetchToDos();
</script>

<main class="container">
  <form class="content" on:submit={handelSubmit}>
    <input
      on:input={handleInput}
      type="text"
      placeholder="Создать задачу"
      class="inputTask"
    />
    <button type="submit" class="btnSubmit">
      <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
        <rect width="24" height="24" fill="white"></rect>
        <path
          d="M12 6V18"
          stroke="#000000"
          stroke-linecap="round"
          stroke-linejoin="round"
        ></path>
        <path
          d="M6 12H18"
          stroke="#000000"
          stroke-linecap="round"
          stroke-linejoin="round"
        ></path>
      </svg>
    </button>
  </form>

  <div class="list">
    {#if toDoS.length > 0}
      {#each toDoS as item, index}
        <label for=""> Название задачи</label>
        <form
          class="listInner"
          on:submit={(e) => {
            changeTitle(e);
          }}
        >
          <input
            type="text"
            placeholder="Введите Название"
            value={item.title}
            id={item.id}
            on:input={(e) => {
              handleInputTitle(e);
            }}
            on:focus={() => (activeInputs[index] = true)}
            on:blur={(event) => (
              (activeInputs[index] = false), isChanged(event, item.title)
            )}
          />
          {#if activeInputs[index]}
            <button
              class="btnSubmit titleBtn"
              type="submit"
              on:click={(e) => {
                changeTitle(e);
              }}
            >
              <svg
                viewBox="0 0 24 24"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
                class="edit-icon"
              >
                <rect width="24" height="24" fill="white"></rect>
                <path
                  d="M12 6V18"
                  stroke="#000000"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                ></path>
                <path
                  d="M6 12H18"
                  stroke="#000000"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                ></path>
              </svg>
            </button>
          {/if}
        </form>
      {/each}
    {/if}
  </div>
</main>

<style>
  .container {
    max-width: 1200px;
    margin-left: auto;
    margin-right: auto;
    display: flex;
    flex-direction: column;
  }

  .content {
    margin-left: auto;
    margin-right: auto;
    max-width: 700px;
    display: flex;
    margin-top: 30px;
  }

  .inputTask {
    border: 1px solid #ccc;
    border-right: none;
    padding: 10px;
    flex: 1;
  }

  .inputTask:focus-visible {
    outline: none;
  }

  .btnSubmit {
    background-color: #fff;
    border: 1px solid #ccc;
    border-left: none;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 0 10px;
    cursor: pointer;
    margin-left: -2px;
    border-radius: 0;
  }

  .btnSubmit:active {
    background-color: #fff;
  }

  svg {
    width: 24px;
    height: 24px;
  }

  .list {
    list-style: none;
    display: flex;
    flex-direction: column;
    margin-left: auto;
    margin-right: auto;
  }

  .list input {
    width: 245px;
    margin-bottom: 10px;
    background-color: #fff;
    padding: 8px;
    border: 0;
  }

  .list input:focus-visible {
    outline: 0;
    margin-right: 0;
  }

  .listInner {
    display: flex;
	position: relative;
  }

  .disabled {
    opacity: 0;
    pointer-events: none;
  }

  .enabled {
    opacity: 1;
    pointer-events: all;
  }

  .titleBtn {
    max-height: 35.6px;
    border: 0;
    margin-right: 0;
	position: absolute;
	right: 0;
  }

  .edit-icon {
    margin-left: 0;
  }
</style>
