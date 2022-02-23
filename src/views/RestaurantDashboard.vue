<template>
  <div class="container py-5">
    <div>
      <h1>{{ restaurant.name }}</h1>
      <span class="badge badge-secondary mt-1 mb-3">
        {{ restaurant.categoryName }}
      </span>
    </div>

    <hr />

    <ul>
      <li>評論數： {{ restaurant.commentsLength }}</li>
      <li>瀏覽次數： {{ restaurant.viewCounts }}</li>
    </ul>

    <button type="button" class="btn btn-link" @click="$router.back()">
      回上一頁
    </button>
  </div>
</template>

<script>
import restaurantsAPI from "./../apis/restaurants";
import { Toast } from "./../utils/helpers";
export default {
  data() {
    return {
      restaurant: {
        name: "",
        categoryName: "未分類",
        commentsLength: -1,
        viewCounts: -1,
      },
    };
  },
  beforeRouteUpdate(to, from, next) {
    const { id: restaurantId } = to.params;
    this.fetchRestaurant(restaurantId);
    next();
  },
  created() {
    const { id: restaurantId } = this.$route.params;
    this.fetchFeeds(restaurantId);
  },
  methods: {
    async fetchFeeds(restaurantId) {
      try {
        const { data } = await restaurantsAPI.getRestaurant({ restaurantId });
        const { restaurant } = data;
        this.restaurant = {
          ...this.restaurant,
          name: restaurant.name,
          categoryName: restaurant.Category
            ? restaurant.Category.name
            : "未分類",
          commentsLength: restaurant.Comments ? restaurant.Comments.length : 0,
          viewCounts: restaurant.viewCounts ? restaurant.viewCounts : 0,
        };
      } catch (error) {
        Toast.fire({
          icon: "error",
          title: "無法取得餐廳資料，請稍後再試",
        });
      }
    },
  },
};
</script>