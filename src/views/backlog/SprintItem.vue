<template>
  <div>
    {{ sprint.title }}
    <button @click="removeSprint(sprint.id)">
      <el-icon><Delete /></el-icon>
    </button>
  </div>
  <div>
    <ul>
      <li v-for="issue in issues" :key="issue.id">
        {{ issue.title }}
        <button @click="removeIssue(issue.id)">
          <el-icon><Delete /></el-icon>
        </button>
      </li>
    </ul>
  </div>
  <form @submit.prevent="createIssue">
    <input v-model="issueTitle" />
    <button type="submit">
      <el-icon><Plus /></el-icon>
    </button>
  </form>
</template>

<script lang="ts" setup>
import axios from "axios";
import { ref } from "vue";
import type { Issue } from "./issue.model";
import type { Sprint } from "./sprint.model";

let props = defineProps<{
  sprint: Sprint;
  issues: Issue[];
}>();

let emit = defineEmits(["remove", "update:issues"]);

let issueTitle = ref("");

async function createIssue() {
  let issue = { title: issueTitle.value, sprint: { id: props.sprint.id } };
  let { data } = await axios.post("http://localhost:3000/issues", issue);
  emit("update:issues", [...props.issues, data]);
}

function removeSprint(id: number) {
  emit("remove", id);
}

async function removeIssue(id: number) {
  await axios.delete("http://localhost:3000/issues/" + id);
  let issues = props.issues.filter((i) => i.id !== id);
  emit("update:issues", issues);
}
</script>