<template>
  <div class="mainboard board">
    <Sidebar taskCount="countTask()"/>
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
              <span class="section-title"
                >{{ name }} {{ tasks[name].length }}</span
              >
              <button class="btn-new-task" @click="openTaskModal(name)">
                <PlusSmIcon /> New Task
              </button>
            </div>
            <draggable class="task-list" :list="dataList" group="name">
              <div v-for="(task, i) in dataList" :key="i" class="task-item">
                <div class="task-item-header">
                  <div class="task-title" v-if="task.title">
                    {{ task.title }}
                  </div>
                  <div class="task-details">
                    <span>{{ taskCreatedAtFormatter(task.createdAt) }}</span>
                    <span
                      >Created by <a href="#">{{ task.createdBy }}</a></span
                    >
                  </div>
                </div>
                <div
                  class="task-item-content"
                  v-if="
                    task.description ||
                    task.coverImg ||
                    task.linkUrl ||
                    task.clipUrl ||
                    task.tags.length > 0
                  "
                >
                  <div class="task-description" v-if="task.description">
                    {{ task.description }}
                  </div>
                  <img
                    class="task-img"
                    :src="task.coverImg"
                    v-if="task.coverImg"
                  />
                  <div class="task-sources" v-if="task.linkUrl || task.clipUrl">
                    <span v-if="task.linkUrl"
                      ><a :href="task.linkUrl"
                        ><LinkIcon /><span>{{ task.linkUrl }}</span></a
                      ></span
                    >
                    <span v-if="task.clipUrl"
                      ><a :href="task.clipUrl"
                        ><PaperClipIcon /><span>{{ task.clipUrl }}</span></a
                      ></span
                    >
                  </div>
                  <div class="task-tags" v-if="task.tags.length > 0">
                    <span v-for="(tag, index) in task.tags" :key="index">{{
                      tag
                    }}</span>
                  </div>
                </div>
                <div
                  class="task-item-footer"
                  v-if="
                    task.comments.length > 0 || task.contributers.length > 0
                  "
                >
                  <div class="task-comments" v-if="task.comments.length > 0">
                    <ChatIcon />{{ task.comments.length }}
                  </div>
                  <div
                    class="task-contributer"
                    v-if="task.contributers.length > 0"
                  >
                    <a
                      class="task-contributer-item"
                      href=""
                      v-for="(contributer, index) in task.contributers"
                      :key="index"
                      ><img
                        class="task-contributer-img"
                        :src="contributer.avatar"
                        :alt="contributer.name"
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
          <button class="submit-btn" @click="addNewSection">Save</button>
        </div>
        <button class="modal-close-btn" @click="newSectionModal = false">
          <XIcon />
        </button>
      </div>
    </div>
    <div class="modal" v-show="newTaskModal">
      <div class="modal-container modal-big">
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
            @keyup="tag"
            v-model="inputTag"
            placeholder="Tag 1, tag 2, tag 3, ..."
          />
          <div class="tag" v-for="(tag, index) in newTaskTags" :key="index">
            {{ tag }} <button v-on:click="deleteTag(index)"><XIcon /></button>
          </div>
        </div>
        <div class="modal-actions">
          <button class="submit-btn" @click="addNewTask">Save</button>
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
import draggable from "vuedraggable";
import fakeData from "./../data/fakeData.js";
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
      tasks: fakeData,
      newSectionTitle: "",
      newSectionModal: false,
      newTaskTitle: "",
      newTaskDescription: "",
      newTaskCoverImg: "",
      newTaskLinkUrl: "",
      newTaskClipUrl: "",
      newTaskTags: [],
      inputTag: "",
      newTaskContributers: [],
      newTaskModal: false,
      selectedSection: "",
      months: [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sept",
        "Oct",
        "Nov",
        "Dec",
      ],
    };
  },
  created() {
    console.log("toplam task : ", this.countTask());
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
    openTaskModal(e) {
      console.log("tasks", Object.keys(this.tasks).length);

      //reset variables
      this.selectedSection = e;
      this.newTaskModal = true;
    },
    countTask() {
      let taskCounter = 0;
      Object.entries(this.tasks).map((e, i, a) => {
        /*console.log("e:", e[1].length);*/
        taskCounter += e[1].length;
      });
      return taskCounter;
    },
    addNewTask() {
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
            contributers: [],
          },
        ];

        console.log("yeni task : ", this.tasks);
        //reset variables
        this.newTaskModal = false;
        this.newTaskTitle = "";
        this.newTaskDescription = "";
        this.newTaskCoverImg = "";
        this.newTaskLinkUrl = "";
        this.newTaskClipUrl = "";
        this.newTaskTags = [];
      }
    },
    deleteTag(id) {
      this.newTaskTags.splice(id, 1);
    },
    tag: function (e) {
      if (e.keyCode === 13 || e.keyCode == 188) {
        let tag = e.target.value;
        tag = tag.replaceAll(",", "");
        if (tag.length > 1) {
          this.newTaskTags.push(tag);
          this.inputTag = "";
        }
      }
    },
    taskCreatedAtFormatter(e) {
      if (new Date(e).getFullYear() == new Date().getFullYear()) {
        return `${new Date(e).getDate()} ${
          this.months[new Date(e).getMonth()]
        }`;
      }
      return `${new Date(e).getDate()} ${
        this.months[new Date(e).getMonth()]
      } ${new Date(e).getFullYear().toString().substr(-2)}`;
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
