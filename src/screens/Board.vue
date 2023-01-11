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
              <div
                class="task-draggable"
                v-for="(task, i) in dataList"
                :key="i"
              >
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
      <div class="modal-container modal-big">
        <h4 class="modal-title">
          {{ selectedTaskDetail.title }}
          <small>MAT-{{ selectedTaskDetail.id }}</small>
        </h4>
        <div class="modal-content">
          <div class="modal-content-row">
            <div class="modal-content-scrolling">
              <div v-if="selectedTaskDetail.description">
                <h5 class="modal-section-title">Description</h5>
                <p>{{ selectedTaskDetail.description }}</p>
              </div>
              <img
                class="task-img"
                :src="selectedTaskDetail.coverImg"
                v-if="selectedTaskDetail.coverImg"
              />
              <div
                class="task-sources"
                v-if="selectedTaskDetail.linkUrl || selectedTaskDetail.clipUrl"
              >
                <span v-if="selectedTaskDetail.linkUrl"
                  ><a :href="selectedTaskDetail.linkUrl"
                    ><LinkIcon /><span>{{
                      selectedTaskDetail.linkUrl
                    }}</span></a
                  ></span
                >
                <span v-if="selectedTaskDetail.clipUrl"
                  ><a :href="selectedTaskDetail.clipUrl"
                    ><PaperClipIcon /><span>{{
                      selectedTaskDetail.clipUrl
                    }}</span></a
                  ></span
                >
              </div>
              <div class="task-tags" v-if="selectedTaskDetail.tags?.length > 0">
                <span
                  v-for="(tag, index) in selectedTaskDetail.tags"
                  :key="index"
                  >{{ tag }}</span
                >
              </div>

              <div
                class="task-detail-comments"
                v-if="selectedTaskDetail.comments?.length > 0"
              >
                <h5 class="modal-section-title">Comments</h5>
                <div class="task-detail-comments-list">
                  <div
                    class="task-detail-comments-item"
                    v-for="(comment, index) in selectedTaskDetail.comments"
                    :key="index"
                  >
                    {{ comment }}
                  </div>
                </div>
              </div>
            </div>
            <div class="modal-content-fixed">
              <div
                class="task-detail-contributors"
                v-if="selectedTaskDetail.contributers?.length > 0"
              >
                <div class="task-detail-contributors-list">
                  <div class="task-detail-contributors-item">
                    Created by {{ selectedTaskDetail.createdBy }}
                  </div>

                  <h5 class="modal-section-title">Contributors</h5>
                  <div
                    class="task-detail-contributors-item"
                    href=""
                    v-for="(
                      contributer, index
                    ) in selectedTaskDetail.contributers"
                    :key="index"
                  >
                    <div
                      class="task-detail-contributors-img"
                      v-html="contributerAvatar(contributer)"
                    ></div>
                  </div>
                </div>
              </div>

              Created at
              {{ new Date(selectedTaskDetail.createdAt).getDate() }}/{{
                new Date(selectedTaskDetail.createdAt).getMonth() + 1
              }}/{{ new Date(selectedTaskDetail.createdAt).getFullYear() }}
            </div>
          </div>
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
    contributerAvatar(e) {
      var contributerDetail = [];
      this.contributers
        .filter((x) => x.id == e)
        .map((x) => (contributerDetail = x));

      if (contributerDetail) {
        return `<img
            src="${contributerDetail.avatar}"
            alt="${contributerDetail.name}"
            title="${contributerDetail.name}"
          /> <span>${contributerDetail.name}</span>`;
      }
    },
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
      this.tasksFilter = this.tasks;
      if (this.selectedContributerFilter > 0) {
        this.contributerFilter(this.selectedContributerFilter);
      }
      this.activeContributersData();
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

      this.tasksFilter = this.tasks;
      if (this.selectedContributerFilter > 0) {
        this.contributerFilter(this.selectedContributerFilter);
      }

      this.activeContributersData();
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
