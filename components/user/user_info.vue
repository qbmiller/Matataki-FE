<template>
  <div class="info">
    <div class="cover">
      <img src="@/assets/img/article_bg.svg" alt="cover">
    </div>
    <div class="avatar-content">
      <avatar :src="userInfo.avatar" class="avatar" />
      <div v-if="iconType" :class="iconType" class="info-type">
        <svg-icon :title="$t('user.accountType')" :icon-class="iconType" class="icon-type" />
      </div>
    </div>
    <h3 class="author">
      {{ userTitle }}
      <router-link v-if="$route.name !== 'setting'" :to="{ name: 'setting' }">
        <svg-icon class="icon-edit" icon-class="edit" />
      </router-link>
    </h3>

    <p class="des">
      {{ userInfo.introduction || $t('not') }}
    </p>
    <div class="follow-fan">
      <div class="data">
        <n-link :to="{name: 'user-id-follow', params: {id: nowId}}" class="num" tag="p">
          {{ userInfo.follows || 0 }}
        </n-link>
        <p class="title">
          {{ $t('follow') }}
        </p>
      </div>
      <div class="data">
        <n-link :to="{name: 'user-id-fan', params: {id: nowId}}" class="num" tag="p">
          {{ userInfo.fans || 0 }}
        </n-link>
        <p class="title">
          {{ $t('fans') }}
        </p>
      </div>
    </div>
    <!-- 如果是设置页面不显示 -->
    <template v-if="!isSetting">
      <!-- 个人主页 -->
      <button
        v-if="!isMe($route.params.id)"
        @click="followOrUnfollowUser({
          id: $route.params.id,
          type: userInfo.followed ? 0 : 1
        })"
        class="button"
      >
        {{ userInfo.followed ? $t('unFollow') : $t('follow') }}
      </button>
    </template>
    <!-- 如果是设置页面不显示 end -->
  </div>
</template>

<script>
import { mapState, mapActions, mapGetters } from 'vuex'
import avatar from '@/components/avatar/index.vue'

export default {
  components: {
    avatar
  },
  props: {
    isSetting: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      iconType: ''
    }
  },
  computed: {
    ...mapState({
      userInfo: state => state.user.userInfo
    }),
    ...mapGetters(['isMe', 'currentUserInfo']),
    nowId() {
      if (this.isSetting) return this.currentUserInfo.id
      else return this.$route.params.id
    },
    userTitle() {
      return this.userInfo && this.userInfo.name && this.userInfo.name.slice(0, 10)
    }
  },
  watch: {
    currentUserInfo() {
      if (this.isSetting) this.refreshUser({ id: this.currentUserInfo.id })
      this.iconType = this.currentUserInfo.idProvider ? (this.currentUserInfo.idProvider).toLocaleLowerCase() : ''
    }
  },

  mounted() {
    if (!this.isSetting) this.refreshUser({ id: this.$route.params.id })
    else if (this.currentUserInfo.id) this.refreshUser({ id: this.currentUserInfo.id })
    if (this.currentUserInfo.idProvider) this.iconType = this.currentUserInfo.idProvider ? (this.currentUserInfo.idProvider).toLocaleLowerCase() : ''
  },
  methods: {
    ...mapActions('user', [
      'refreshUser',
      'followOrUnfollowUser'
    ])
  }
}
</script>

<style lang="less" scoped>
.line{
  width: 190px;
  height: 1px;
  background-color: #DBDBDB;
  margin: 0 auto;
}

.info {
  overflow: hidden;
  background-color: #fff;
  border-radius: @br10;
}
.avatar {
    width: 60px !important;
    height: 60px !important;
    background: #eee;
    z-index: 1;
    position: relative;
}
.avatar-content {
  width: 60px !important;
  height: 60px !important;
  margin: -30px auto;
  position: relative;
  .info-type {
    position: absolute;
    right: -4px;
    bottom: -2px;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    z-index: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: #eee;
    .icon-type {
      color: #fff;
      width: 60%;
      height: 60%;
    }
    &.email {
      background-color: #F7B500;
    }
    &.eos {
      background-color: #333;
    }
    &.ont {
      background-color: #4d9afd;
    }
    &.github {
      background-color: #882592;
    }
  }
}
.cover {
  width: 100%;
  height: 90px;
  overflow: hidden;
  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
}

.author {
    font-size: 20px;
    font-weight: bold;
    color: #000000;
    margin: 46px auto 0;
    text-align: center;
    overflow: hidden;
    text-overflow: ellipsis;
    position: relative;
    width: 80%;
    .icon-edit {
      position: absolute;
      margin-left: 6px;
      cursor: pointer;
      color: #B2B2B2;
  }
}
.des {
    padding: 0;
    margin: 0;
    font-size: 14px;
    font-weight: 400;
    color: #000000;
    line-height: 17px;
    width: 80%;
    text-align: center;
    margin: 10px auto;
}

.follow-fan {
    display: flex;
    justify-content: center;
    align-items: center;
    .data {
      margin: 20px 18px;
      text-align: center;
      .num {
        font-size:24px;
        font-weight:600;
        color:@purpleDark;
        line-height:33px;
        padding: 0;
        margin: 0;
        cursor: pointer;
      }
      .title {
        font-size:14px;
        font-weight:400;
        color:rgba(0,0,0,1);
        line-height:17px;
        padding: 0;
        margin: 2px 0 0;
      }
    }
}

.button {
  background-color: @black;
  border-radius:@borderRadius6;
  width: 190px;
  height: 40px;
  font-size: 16px;
  font-weight: bold;
  color: #ffffff;
  line-height: 22px;
  margin: 20px auto 40px;
  text-align: center;
  display: block;
}
</style>
