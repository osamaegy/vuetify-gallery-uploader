<template>
  <v-row
    class="black pa-10"
    style="position: relative;min-height: 600px"
    align="top"
    justify="center"
  >
    <v-col cols="10" class=" text-center">
      <v-slide-x-transition>
        <div v-if="!loading && src">
          <figure style="height: 450px">
            <img
              id="u-previewed-image"
              :src="src"
              :class="filterMode ? 'filter-mode-on' : ''"
              ref="selectedImage"
              :style="filters"
            />
          </figure>
          <div style="height:70px " class="pt-2">
            <action-buttons
              :selected="selected"
              :filter-mode="filterMode"
              :selectedImageSrc="src"
              @image-edited="imageEdited"
              @image-removed="imageRemoved"
              @reset-all="$emit('reset-all')"
              @filter="filterMode = true"
              @edit-mode="editMode = $event"
            />
          </div>
        </div>
      </v-slide-x-transition>

      <v-slide-y-transition>
        <filters
          :filterMode="filterMode"
          :selected="selected"
          @close="filterMode = false"
          @changed="filters = $event"
          @saved="$emit('filters-saved', $event)"
          v-show="filterMode"
        />
      </v-slide-y-transition>
    </v-col>
  </v-row>
</template>

<script>
import ActionButtons from "./ActionButtons";
import Filters from "./Filters";
export default {
  name: "image-editor",
  components: { Filters, ActionButtons },
  props: ["selected", "images"],
  data() {
    return {
      selectedFilter: null,
      loading: false,
      src: null,
      filters: "",
      filterMode: false,
      editMode: false
    };
  },

  mounted() {
    this.$emit("filter-mode", this.filterMode);
    let interval = setInterval(() => {
      if (this.images.length) {
        this.src = this.images[this.selected];
        clearInterval(interval);
      }
    }, 200);
    // this.initCroppper(this.images[this.selected]);
  },
  watch: {
    editMode() {
      this.$emit("edit-mode", this.editMode);
    },
    filterMode() {
      this.$emit("filter-mode", this.filterMode);
    },
    selected() {
      this.loading = true;
      setTimeout(() => {
        this.src = this.images[this.selected];

        this.loading = false;
      }, 300);
    }
  },
  methods: {
    imageEdited(image) {
      this.$emit("image-edited", image);
    },
    imageRemoved(image) {
      this.$emit("image-removed", image);
    }
  }
};
</script>
<style lang="scss" scoped>
#u-previewed-image {
  height: 450px;
  max-width: 100%;
  transform: translateX(0);
  transition: all 0.5s;
}
#u-previewed-image.filter-mode-on {
  /*max-width: 500px;*/

  background-size: cover;
  transform: translateX(-7em);
}
</style>
