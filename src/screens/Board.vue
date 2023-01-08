<template>
  <div class="mainboard board">
    <Sidebar :taskCount="countTask()" />
    <div class="board-panel">
      <div class="board-panel-header">
        <button class="btn-new-section" @click="newSectionModal = true">
          <PlusSmIcon /> New Section
        </button>

        <div class="contributer-list">
          <div
            class="contributer-list-item"
            v-for="(contributer, index) in contributers"
            :key="index"
          >
            <img
              class="contributer-list-img"
              :src="contributer.avatar"
              :alt="contributer.name"
              :title="contributer.name"
            />
          </div>
        </div>
      </div>

      <div class="board-panel-content">
        <draggable class="board-list" group="sections">
          <div
            class="board-section"
            v-for="(dataList, name, index) in tasks"
            :key="index"
          >
            <div class="section-head">
              <div class="section-title">
                <span contenteditable="true">{{ name }}</span>
                {{ tasks[name].length }}
              </div>
              <button class="btn-new-task" @click="openTaskModal(name)">
                <PlusSmIcon /> New Task
              </button>
            </div>
            <draggable class="task-list" :list="dataList" group="name">
              <div
                v-for="(task, i) in dataList"
                :key="i"
                @click="viewTaskDetails"
              >
                <Task :taskDetail="task" :contributerData="contributers" />
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
          <button
            class="submit-btn"
            @click="addNewSection"
            :disabled="newSectionTitle.length == 0"
          >
            Save
          </button>
        </div>
        <button class="modal-close-btn" @click="newSectionModal = false">
          <XIcon />
        </button>
      </div>
    </div>
    <div class="modal" v-show="newTaskModal">
      <div class="modal-container modal-medium">
        <div class="modal-title">Add New Task</div>
        <div class="modal-content">
          <input type="text" v-model="newTaskTitle" placeholder="Task Title" />
          <input
            type="text"
            v-model="newTaskDescription"
            placeholder="Task Description"
          />
          <input
            type="text"
            v-model="newTaskCoverImg"
            placeholder="Task Cover Img Url"
          />
          <input
            type="text"
            v-model="newTaskLinkUrl"
            placeholder="Task Link Url"
          />
          <input
            type="text"
            v-model="newTaskClipUrl"
            placeholder="Task Clip Url"
          />

          <input
            type="text"
            @keyup="addTaskTag"
            v-model="tagInput"
            placeholder="Tag 1, tag 2, tag 3, ..."
          />
          <div class="input-tag-list" v-if="newTaskTags.length > 0">
            <div class="tag" v-for="(tag, index) in newTaskTags" :key="index">
              {{ tag }}
              <XIcon @click="deleteTaskTag(index)" />
            </div>
          </div>
          <select v-model="newTaskContributers" multiple>
            <option disabled="disabled" selected="selected">Assignment</option>
            <option
              v-for="(contributer, index) in contributers"
              :key="index"
              :value="contributer.id"
            >
              {{ contributer.name }}
            </option>
          </select>
        </div>
        <div class="modal-actions">
          <button
            class="submit-btn"
            @click="addNewTask"
            :disabled="newTaskTitle.length == 0"
          >
            Save
          </button>
        </div>
        <button class="modal-close-btn" @click="newTaskModal = false">
          <XIcon />
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from "@/components/Sidebar.vue";
import Task from "@/components/Task.vue";
import draggable from "vuedraggable";
import { dummyTaskData, dummyContributerData } from "./../data/dummyData.js";
import {
  PlusSmIcon,
  LinkIcon,
  PaperClipIcon,
  ChatIcon,
  XIcon,
} from "@vue-hero-icons/outline";

export default {
  name: "Board",
  components: {
    Sidebar,
    Task,
    PlusSmIcon,
    LinkIcon,
    PaperClipIcon,
    ChatIcon,
    XIcon,
    draggable,
  },
  data() {
    return {
      tasks: dummyTaskData,
      contributers: dummyContributerData,
      newSectionTitle: "",
      newSectionModal: false,
      newTaskTitle: "",
      newTaskDescription: "",
      newTaskCoverImg: "",
      newTaskLinkUrl: "",
      newTaskClipUrl: "",
      newTaskTags: [],
      tagInput: "",
      newTaskContributers: [],
      newTaskModal: false,
      selectedSection: "",
      totalTaskCount: 0,
    };
  },

  methods: {
    viewTaskDetails() {
      console.log("taska bastın");
    },
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

        //reset variables
        this.newSectionModal = false;
        this.newSectionTitle = "";
      }
    },
    openTaskModal(e) {
      this.selectedSection = e;
      this.newTaskModal = true;
    },
    countTask() {
      let taskCounter = 0;
      Object.entries(this.tasks).map((e) => {
        taskCounter += e[1].length;
      });
      return (this.totalTaskCount = taskCounter);
    },
    addNewTask() {
      console.log("this.newTaskContributers : ", this.newTaskContributers);
      if (this.newTaskTitle != null && this.newTaskTitle.length > 0) {
        this.tasks[this.selectedSection] = [
          ...this.tasks[this.selectedSection],
          {
            title: this.newTaskTitle,
            createdAt: new Date(),
            createdBy: "AbdullahTurkmen",
            description: this.newTaskDescription,
            coverImg: this.newTaskCoverImg,
            linkUrl: this.newTaskLinkUrl,
            clipUrl: this.newTaskClipUrl,
            tags: this.newTaskTags,
            comments: [],
            contributers: this.newTaskContributers,
          },
        ];

        console.log("new task added : ", this.tasks);

        //reset variables
        this.newTaskModal = false;
        this.newTaskTitle = "";
        this.newTaskDescription = "";
        this.newTaskCoverImg = "";
        this.newTaskLinkUrl = "";
        this.newTaskClipUrl = "";
        this.newTaskTags = [];
        this.newTaskContributers = [];
      }
    },
    deleteTaskTag(id) {
      this.newTaskTags.splice(id, 1);
    },
    addTaskTag(e) {
      if (e.keyCode === 13 || e.keyCode == 188) {
        let tag = e.target.value;
        tag = tag.replaceAll(",", "");
        if (tag.length > 1) {
          this.newTaskTags.push(tag);
          this.tagInput = "";
        }
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
