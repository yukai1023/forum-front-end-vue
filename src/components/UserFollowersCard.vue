<template>
  <div class="card">
    <div class="card-header">
      <strong>{{ followers.length }}</strong> followers (追隨者)
    </div>
    <div class="card-body">
      <router-link
        v-for="user in followers"
        :key="user.id"
        :to="{ name: 'user', params: { id: user.id } }"
      >
        <img :src="user.image" class="avatar" width="60" height="60" />
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    followers: {
      type: Array,
      required: true,
    },
  },
};
</script>