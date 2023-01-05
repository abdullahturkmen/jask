<template>
  <div class="mainboard board">
    <Sidebar />
    <div class="board-panel">
      <button class="btn-new-section"><PlusSmIcon /> Add New Section</button>

      <draggable class="board-list" group="sections">
        <div class="board-section">
          <span class="section-title">To do</span>
          <draggable class="task-list" :list="tasks.ideas" group="tasks">
            <div v-for="(idea, i) in tasks.ideas" :key="i" class="task-item">
              <p>{{ idea }}</p>
            </div>
          </draggable>
        </div>
        <div class="board-section">
          <span class="section-title">Doing</span>
          <draggable class="task-list" :list="tasks.todos" group="tasks">
            <div v-for="(todo, i) in tasks.todos" :key="i" class="task-item">
              <p>{{ todo }}</p>
            </div>
          </draggable>
        </div>
        <div class="board-section">
          <span class="section-title">inProgress</span>
          <draggable class="task-list" :list="tasks.inProgress" group="tasks">
            <div
              v-for="(progress, i) in tasks.inProgress"
              :key="i"
              class="task-item"
            >
              <p>{{ progress }}</p>
            </div>
          </draggable>
        </div>
        <div class="board-section">
          <span class="section-title">To do</span>
          <draggable class="task-list" :list="tasks.ideas" group="tasks">
            <div v-for="(idea, i) in tasks.ideas" :key="i" class="task-item">
              <p>{{ idea }}</p>
            </div>
          </draggable>
        </div>
        <div class="board-section">
          <span class="section-title">Doing</span>
          <draggable class="task-list" :list="tasks.todos" group="tasks">
            <div v-for="(todo, i) in tasks.todos" :key="i" class="task-item">
              <p>{{ todo }}</p>
            </div>
          </draggable>
        </div>
        <div class="board-section">
          <span class="section-title">inProgress</span>
          <draggable class="task-list" :list="tasks.inProgress" group="tasks">
            <div
              v-for="(progress, i) in tasks.inProgress"
              :key="i"
              class="task-item"
            >
              <p>{{ progress }}</p>
            </div>
          </draggable>
        </div>
      </draggable>
    </div>
  </div>
</template>

<script>
import Sidebar from "@/components/Sidebar.vue";
import draggable from "vuedraggable";
import { PlusSmIcon } from "@vue-hero-icons/outline";

export default {
  name: "Home",
  components: {
    Sidebar,
    PlusSmIcon,
    draggable,
  },
  data() {
    return {
      sections: ["sectionOne", "sectionTwo", "sectionThree"],
      tasks: {
        ideas: ["TASK-1124"],
        todos: [
          "TASK-1120",
          "TASK-1036",
          "TASK-1123",
          "TASK-1032",
          "TASK-1001",
          "TASK-1002",
          "TASK-1125",
          "TASK-1030",
          "TASK-1620",
          "TASK-950",
          "TASK-997",
          "TASK-1011",
        ],
        inProgress: ["TASK-1129", "TASK-1038"],
      },
    };
  },
  methods: {
    onMove({ relatedContext, draggedContext }) {
      const relatedElement = relatedContext.element;
      const draggedElement = draggedContext.element;
      return (
        (!relatedElement || !relatedElement.fixed) && !draggedElement.fixed
      );
    },
  },
  computed: {
    dragOptions() {
      return {
        animation: 0,
        group: "description",
        disabled: !this.editable,
        ghostClass: "ghost",
      };
    },
  },
  watch: {
    isDragging(newValue) {
      if (newValue) {
        this.delayedDragging = true;
        return;
      }
      this.$nextTick(() => {
        this.delayedDragging = false;
      });
    },
  },
};
</script>
