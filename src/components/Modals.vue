<template>
  <div>

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
import {
  CogIcon,
  UserIcon,
  ChartSquareBarIcon,
  BellIcon,
  ViewGridIcon,
} from "@vue-hero-icons/outline";

export default {
  name: "Modals",
  components: {
    CogIcon,
    UserIcon,
    ChartSquareBarIcon,
    BellIcon,
    ViewGridIcon,
  },
};
</script>

<style lang="scss">
@import "./../assets/css/modal.scss";
</style>
