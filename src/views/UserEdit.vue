<template>
  <div class="container py-5">
    <form @submit.stop.prevent="handleSubmit">
      <div class="form-group">
        <label for="name">Name</label>
        <input
          v-model="profile.name"
          id="name"
          type="text"
          name="name"
          class="form-control"
          placeholder="Enter Name"
          required
        />
      </div>

      <div class="form-group">
        <label for="image">Image</label>
        <img
          v-if="profile.image"
          :src="profile.image"
          class="d-block img-thumbnail mb-3"
          width="200"
          height="200"
        />
        <input
          id="image"
          type="file"
          name="image"
          accept="image/*"
          class="form-control-file"
          @change="handleFileChange"
        />
      </div>

      <button type="submit" class="btn btn-primary" :disabled="isProcessing">
        Submit
      </button>
    </form>
  </div>
</template>

<script>
import { mapState } from "vuex";
import usersAPI from "./../apis/users";
import { Toast } from "./../utils/helpers";
export default {
  data() {
    return {
      profile: {
        name: "",
        image: "",
      },
      isProcessing: false,
    };
  },
  watch: {
    currentUser(newValue) {
      this.setUser(newValue);
    },
  },
  created() {
    this.setUser(this.currentUser);
  },
  beforeRouteUpdate(to, from, next) {
    const { id: userId } = to.params;
    if (Number(userId) !== this.currentUser.id) {
      this.$router.replace({ name: "not-found" });
    } else {
      next();
    }
  },
  computed: {
    ...mapState(["currentUser", "isAuthenticated"]),
  },
  methods: {
    setUser(currentUser) {
      const { id } = this.$route.params;
      if (Number(id) !== currentUser.id) {
        this.$router.replace({ name: "not-found" });
        return;
      }
      this.profile = {
        name: currentUser.name,
        image: currentUser.image,
      };
    },
    handleFileChange(e) {
      const { files } = e.target;

      if (files.length === 0) {
        // 使用者沒有選擇上傳的檔案
        this.profile.image = "";
      } else {
        // 否則產生預覽圖
        const imageURL = window.URL.createObjectURL(files[0]);
        this.profile.image = imageURL;
      }
    },
    async handleSubmit(e) {
      try {
        this.isProcessing = true;
        const form = e.target; // <form></form>
        const formData = new FormData(form);
        const { data } = await usersAPI.update({
          userId: this.currentUser.id,
          formData,
        });
        if (data.status !== "success") {
          throw new Error(data.message);
        }
        this.isProcessing = false;
        this.$router.push({
          name: "user",
          params: { id: this.currentUser.id },
        });
      } catch (error) {
        Toast.fire({
          icon: "error",
          title: "無法取消追蹤，請稍後再試",
        });
      }
    },
  },
};
</script>