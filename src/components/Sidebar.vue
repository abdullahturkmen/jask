<template>
  <div class="board-sidebar">
    <div class="sidebar-item"><HashtagIcon /><span>Company</span></div>
    <div class="sidebar-item active">
      <ViewGridIcon /><span>Tasks</span
      ><span class="item-count">{{ this.taskCount }}</span>
    </div>
    <div class="sidebar-item">
      <BellIcon /><span>Notifications</span><span class="item-count">1</span>
    </div>
    <div class="sidebar-item">
      <ChartSquareBarIcon /><span>Analytics</span
      ><span class="item-count">12</span>
    </div>
    <div class="sidebar-item"><UserIcon /><span>Teams</span></div>
    <div class="sidebar-item align-end"><CogIcon /><span>Setting</span></div>
  </div>
</template>

<script>
import {
  CogIcon,
  UserIcon,
  ChartSquareBarIcon,
  BellIcon,
  ViewGridIcon,
  HashtagIcon,
} from "@vue-hero-icons/outline";

export default {
  name: "Sidebar",
  props: {
    taskCount: Number,
  },
  components: {
    CogIcon,
    UserIcon,
    ChartSquareBarIcon,
    BellIcon,
    ViewGridIcon,
    HashtagIcon,
  },
};
</script>

<style lang="scss">
@import "./../assets/css/sidebar.scss";
</style>
