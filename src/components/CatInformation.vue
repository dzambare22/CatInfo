<template>
  <v-app class="catInfo">
    <v-row>
      <v-col cols="3">
        <v-autocomplete
          v-model="selectedCategory"
          :items="items"
          item-text="name"
          item-value="id"
          label="Categories"
          deletable-chips
          small-chips
          dense
          @change="onCategoryChange()"
        ></v-autocomplete>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12" style="height: 400px">
        <v-img
          v-for="(image, index) in images"
          :key="index"
          :src="image.url"
          :max-height="image.height"
          :max-width="image.width"
          class="m-5"
          style="float: left"
        >
        </v-img>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <div class="text-center">
          <v-pagination
            v-model="page"
            :length="10"
            :total-visible="7"
            @input="OnPageChange()"
          ></v-pagination>
        </div>
      </v-col>
    </v-row>
  </v-app>
</template>
<script>
import apiService from "../services/fs-services";

export default {
  name: "CatInfo",
  data() {
    return {
      page: 1,
      selectedCategory: {
        id: 1,
        name: "Hats",
      },
      items: [],
      images: [],
      catNames: "",
    };
  },
  mounted() {
    this.getAllCategories();
    this.onCategoryChange(this.selectedCategory.id);
  },
  methods: {
    getAllCategories() {
      apiService.getAllCategories().then((response) => {
        response.data.forEach((ele) => {
          var obj = {
            id: ele.id,
            name: ele.name[0].toUpperCase() + ele.name.substring(1),
          };
          this.items.push(obj);
        });
      });
    },
    onCategoryChange(selectedCategory) {
      selectedCategory = selectedCategory ? selectedCategory : this.selectedCategory;
      this.images = [];
      apiService
        .getImageOnSerach(selectedCategory, this.page)
        .then((response) => {
          response.data.forEach((data) => {
            this.images.push({
              url: data.url,
              height: 150, //data.height,
              width: 250, //data.width
            });
          });
        });
    },
    OnPageChange() {
      this.onCategoryChange(this.selectedCategory.id);
    },
  },
};
</script>
<style>
.v-autocomplete__content {
  top: 60px !important;
}
</style>