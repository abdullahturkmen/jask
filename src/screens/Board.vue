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
            v-for="(contributer, index) in activeContributers"
            :key="index"
            @click="contributerFilter(contributer.id)"
            :class="{ active: contributer.id == selectedContributerFilter }"
          >
            <img
              class="contributer-list-img"
              :src="contributer.avatar"
              :alt="contributer.name"
              :title="contributer.name"
            />
          </div>
          <button
            class="contributer-list-filter-delete"
            v-if="selectedContributerFilter > 0"
            @click="removeContributerFilter"
          >
            <XIcon />
          </button>
        </div>
      </div>

      <div class="board-panel-content">
        <draggable class="board-list" group="sections">
          <div
            class="board-section"
            v-for="(dataList, name, index) in tasksFilter"
            :key="index"
          >
            <div class="section-head">
              <div class="section-title">
                <span class="editable-title" contenteditable="true">{{
                  name
                }}</span>
                <span v-if="selectedContributerFilter > 0"
                  >{{ tasksFilter[name].length }} / </span
                >{{ tasks[name].length }}
              </div>
              <button class="btn-new-task" @click="openTaskModal(name)">
                <PlusSmIcon /> New Task
              </button>
            </div>
            <draggable class="task-list" :list="dataList" group="name">
              <div v-for="(task, i) in dataList" :key="i">
                <Task
                  :taskDetail="task"
                  :contributerData="contributers"
                  @clickedViewTaskDetails="viewTaskDetails"
                />
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
    <div class="modal" v-show="selectedTaskDetail.length !== 0">
      <div class="modal-container modal-medium">
        <div class="modal-title">
          {{ selectedTaskDetail.title }}
          <small>MAT-{{ selectedTaskDetail.id }}</small>
        </div>
        <div class="modal-content">
          <div class="task-item-header">
            <div class="task-details">
              <span>20 Jan 22</span
              ><span>Created by <a href="#">AbdullahTurkmen</a></span>
            </div>
          </div>
          <div v-if="selectedTaskDetail.description">
            {{ selectedTaskDetail.description }}
          </div>
          <img
            class="task-img"
            :src="selectedTaskDetail.coverImg"
            v-if="selectedTaskDetail.coverImg"
          />
        </div>
        <div class="modal-actions">
          <button class="submit-btn" @click="selectedTaskDetail = []">
            Close
          </button>
        </div>
        <button class="modal-close-btn" @click="selectedTaskDetail = []">
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
      tasksFilter: dummyTaskData,
      contributers: dummyContributerData,
      activeContributers: [],
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
      selectedTaskDetail: [],
      selectedContributerFilter: 0,
    };
  },
  created() {
    this.activeContributersData();
  },

  methods: {
    activeContributersData() {
      this.activeContributers = [];
      Object.values(this.tasks).map((e) => {
        e.map((x) => {
          x.contributers.map((z) => {
            Object.values(this.contributers)
              .filter((ex) => ex.id == z)
              .map((ex) => {
                if (
                  this.activeContributers.filter((exc) => exc.id == ex.id) == 0
                ) {
                  this.activeContributers = [
                    ...this.activeContributers,
                    { ...ex },
                  ];
                }
              });
          });
        });
      });
    },
    contributerFilter(id) {
      this.selectedContributerFilter = id;
      this.tasksFilter = [];
      Object.entries(this.tasks).map((e) => {
        e[1].map((x) => {
          if (x.contributers.includes(id)) {
            if (this.tasksFilter[e[0]] === undefined) {
              this.tasksFilter = {
                ...this.tasksFilter,
                ...{ [e[0]]: [] },
              };
            }

            this.tasksFilter[e[0]] = [...this.tasksFilter[e[0]], { ...x }];
          }
        });
      });
    },
    removeContributerFilter() {
      this.selectedContributerFilter = 0;
      this.tasksFilter = this.tasks;
    },
    viewTaskDetails(id) {
      this.selectedTaskDetail = [];

      Object.values(this.tasks).map((e) => {
        e.filter((x) => x.id == id).map((x) => (this.selectedTaskDetail = x));
      });
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
      if (this.selectedContributerFilter > 0) {
        this.contributerFilter(this.selectedContributerFilter);
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
      if (this.newTaskTitle != null && this.newTaskTitle.length > 0) {
        this.tasks[this.selectedSection] = [
          ...this.tasks[this.selectedSection],
          {
            id: new Date().valueOf(),
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
      if (this.selectedContributerFilter > 0) {
        this.contributerFilter(this.selectedContributerFilter);
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

    tasks: function (newQuestion) {
      this.tasksFilter = this.tasks
      // this will be run immediately on component creation.
    },
    // force eager callback execution
  },
};
</script>
