<template>
  <div class="mainboard board">
    <Sidebar />
    <div class="board-panel">
      <button class="btn-new-section" @click="newSectionModal = true">
        <PlusSmIcon /> New Section
      </button>

      <div class="board-panel-content">
        <draggable class="board-list" group="sections">
          <div
            class="board-section"
            v-for="(dataList, name, index) in tasks"
            :key="index"
          >
            <div class="section-head">
              <span class="section-title">{{ name }}</span>
              <button class="btn-new-task"><PlusSmIcon /> New Task</button>
            </div>
            <draggable class="task-list" :list="dataList" group="name">
              <div v-for="(title, i) in dataList" :key="i" class="task-item">
                <div class="task-item-header">
                  <div class="task-title">{{ title }}</div>
                  <div class="task-details">
                    <span>12th Jan</span>
                    <span>Created by <a href="#">Prahlad</a></span>
                  </div>
                </div>
                <div class="task-item-content">
                  <div class="task-description">
                    Please use trello and designs in Dribbble as reference. And
                    carry on...
                  </div>
                  <img
                    class="task-img"
                    src="https://picsum.photos/id/85/300/120"
                  />
                  <div class="task-sources">
                    <span><LinkIcon /><span>docs.google.com.deneme</span></span>
                    <span
                      ><PaperClipIcon /><span
                        >docs.google.com.deneme</span
                      ></span
                    >
                  </div>
                  <div class="task-tags">
                    <span>Design</span>
                    <span>Development</span>
                  </div>
                </div>
                <div class="task-item-footer">
                  <div class="task-comments"><ChatIcon />3</div>
                  <div class="task-contributer">
                    <a class="task-contributer-item" href=""
                      ><img
                        class="task-contributer-img"
                        src="https://picsum.photos/id/82/24/24"
                    /></a>
                    <a class="task-contributer-item" href=""
                      ><img
                        class="task-contributer-img"
                        src="https://picsum.photos/id/89/24/24"
                    /></a>
                    <a class="task-contributer-item" href=""
                      ><img
                        class="task-contributer-img"
                        src="https://picsum.photos/id/87/24/24"
                    /></a>
                  </div>
                </div>
              </div>
            </draggable>
          </div>
        </draggable>
      </div>
    </div>
    <div class="modal" v-show="newSectionModal">
      <div class="modal-container modal-small">
        <div class="modal-title">Add New Section</div>
        <div class="modal-content">
          <input
            type="text"
            v-model="newSectionTitle"
            placeholder="Section Title"
          />
        </div>
        <div class="modal-actions">
          <button class="submit-btn" @click="addNewSection">Kaydet</button>
        </div>
        <button class="modal-close-btn" @click="newSectionModal = false">
          <XIcon />
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from "@/components/Sidebar.vue";
import draggable from "vuedraggable";
import {
  PlusSmIcon,
  LinkIcon,
  PaperClipIcon,
  ChatIcon,
  XIcon,
} from "@vue-hero-icons/outline";

export default {
  name: "Home",
  components: {
    Sidebar,
    PlusSmIcon,
    LinkIcon,
    PaperClipIcon,
    ChatIcon,
    XIcon,
    draggable,
  },
  data() {
    return {
      tasks: {
        ideas: ["TASK-1124"],
        todos: [
          "Make a Kanban App",
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
      newSectionTitle: "",
      newSectionModal: false,
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
    addNewSection() {
      if (this.newSectionTitle != null && this.newSectionTitle.length > 0) {
        this.tasks = {
          ...this.tasks,
          ...{ [this.newSectionTitle.trim()]: [] },
        };
        this.newSectionModal = false;
        this.newSectionTitle = "";
      }
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
