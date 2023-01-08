<template>
  <div class="task-item">
    <div class="task-item-header">
      <div class="task-title" v-if="taskDetail.title">
        {{ taskDetail.title }}
      </div>
      <div class="task-details">
        <span>{{ taskCreatedAtFormatter(taskDetail.createdAt) }}</span>
        <span
          >Created by <a href="#">{{ taskDetail.createdBy }}</a></span
        >
      </div>
    </div>
    <div
      class="task-item-content"
      v-if="
        taskDetail.description ||
        taskDetail.coverImg ||
        taskDetail.linkUrl ||
        taskDetail.clipUrl ||
        taskDetail.tags.length > 0
      "
    >
      <div class="task-description" v-if="taskDetail.description">
        {{ taskDetail.description }}
      </div>
      <img
        class="task-img"
        :src="taskDetail.coverImg"
        v-if="taskDetail.coverImg"
      />
      <div class="task-sources" v-if="taskDetail.linkUrl || taskDetail.clipUrl">
        <span v-if="taskDetail.linkUrl"
          ><a :href="taskDetail.linkUrl"
            ><LinkIcon /><span>{{ taskDetail.linkUrl }}</span></a
          ></span
        >
        <span v-if="taskDetail.clipUrl"
          ><a :href="taskDetail.clipUrl"
            ><PaperClipIcon /><span>{{ taskDetail.clipUrl }}</span></a
          ></span
        >
      </div>
      <div class="task-tags" v-if="taskDetail.tags.length > 0">
        <span v-for="(tag, index) in taskDetail.tags" :key="index">{{
          tag
        }}</span>
      </div>
    </div>
    <div
      class="task-item-footer"
      v-if="
        taskDetail.comments.length > 0 || taskDetail.contributers.length > 0
      "
    >
      <div class="task-comments" v-if="taskDetail.comments.length > 0">
        <ChatIcon />{{ taskDetail.comments.length }}
      </div>
      <div class="task-contributer" v-if="taskDetail.contributers.length > 0">
        <a
          class="task-contributer-item"
          href=""
          v-for="(contributer, index) in taskDetail.contributers"
          :key="index"
          ><div
            class="task-contributer-img"
            v-html="contributerAvatar(contributer)"
          ></div
        ></a>
      </div>
    </div>
  </div>
</template>

<script>
import {
  CogIcon,
  UserIcon,
  ChartSquareBarIcon,
  BellIcon,
  ViewGridIcon,
  LinkIcon,
  PaperClipIcon,
  ChatIcon,
} from "@vue-hero-icons/outline";

export default {
  name: "Task",
  props: {
    taskDetail: Object,
    contributerData: Array,
  },
  data() {
    return {
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
  components: {
    CogIcon,
    UserIcon,
    ChartSquareBarIcon,
    BellIcon,
    ViewGridIcon,
    LinkIcon,
    PaperClipIcon,
    ChatIcon,
  },

  methods: {
    contributerAvatar(e) {
      var contributerDetail = [];
      this.contributerData
        .filter((x) => x.id == e)
        .map((x) => (contributerDetail = x));

      if (contributerDetail) {
        return `<img
            src="${contributerDetail.avatar}"
            alt="${contributerDetail.name}"
            title="${contributerDetail.name}"
          />`;
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
};
</script>

<style lang="scss">
@import "./../assets/css/task.scss";
</style>
