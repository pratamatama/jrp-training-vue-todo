<script setup>
import { ref, reactive, computed } from "vue";
import TodoCard from "@/components/TodoCard.vue";
import FormInput from "@/components/Form/Input.vue";
import { useForm } from "vee-validate";
import { object, string } from "yup";

const todos = ref([]);

const { handleSubmit, handleReset } = useForm({
  validationSchema: object().shape({
    todo: string()
      .required("Kolom ini harus diisi")
      .test("unique", "${path} sudah pernah diinput", (value) => {
        return !todos.value.map((t) => t.text).includes(value);
      }),
  }),
});

const incompleteTodos = computed(() => {
  return todos.value.filter((x) => x.completed === false);
});

const completedTodos = computed(() => {
  return todos.value.filter((x) => x.completed === true);
});

let id = 0;
const onSubmit = handleSubmit((values) => {
  const newTodo = {
    id: ++id,
    text: values.todo,
    completed: false,
  };
  todos.value = [newTodo, ...todos.value];
  handleReset();
});

const remove = (todoItem) => {
  todos.value = todos.value.filter((t) => t.id !== todoItem.id);
};
</script>

<template>
  <main class="h-screen flex items-center justify-center">
    <div>
      <form class="w-[400px] mb-4" @submit.prevent="onSubmit" novalidate>
        <FormInput name="todo" placeholder="Mau apa hari ini?" />
      </form>

      <div class="grid grid-cols-12 gap-4">
        <div class="col-span-6">
          <TodoCard
            v-for="todoItem in incompleteTodos"
            :key="todoItem.id"
            :todo="todoItem"
            @remove="remove"
          />
        </div>
        <div class="col-span-6">
          <TodoCard
            v-for="todoItem in completedTodos"
            :key="todoItem.id"
            :todo="todoItem"
            @remove="remove"
          />
        </div>
      </div>
    </div>
  </main>
</template>
