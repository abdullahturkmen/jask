<template>
  <div class="board-sidebar" :class="{ open: sidebarOpen }">
    <button class="board-sidebar-open" @click="sidebarOpen = !sidebarOpen">
      <MenuIcon />
    </button>

    <div class="board-sidebar-content">
      
      <div class="sidebar-item logo">
        <img src="./../assets/img/jask-logo.png" alt="">
      </div>
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
      <button class="board-sidebar-close" @click="sidebarOpen = !sidebarOpen">
      <XIcon />
    </button>
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
  HashtagIcon,
  MenuIcon,
  XIcon,
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
    MenuIcon,
    XIcon,
  },
  data() {
    return {
      sidebarOpen: false,
    };
  },
};
</script>

<style lang="scss">
@import "./../assets/css/sidebar.scss";
</style>
