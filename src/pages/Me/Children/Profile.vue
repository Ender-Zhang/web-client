<template>
  <div class="user-detail">
    <div class="user-detail-top">Basic Information</div>
    <div class="user-detail-group">
      <div class="user-icon">
        <span>Avatar</span>
        <img :src="userInfo.user_avatar" alt="">
      </div>
      <div class="user-item">
        <span>phone</span>
        <span>{{ userInfo.user_phone || 'Not available' }}</span>
      </div>
      <div class="user-item">
        <span>Account Number</span>
        <span>{{ userInfo.user_name || 'Not available' }}</span>
      </div>
      <div class="user-item">
        <span>Nickname</span>
        <span>{{ userInfo.user_nickname || 'Not available' }}</span>
      </div>
      <div class="user-item">
        <span>Gender</span>
        <span>{{ userInfo.user_sex || 'Not available' }}</span>
      </div>
      <div class="user-item">
        <span>Place of permanent residence</span>
        <span>{{ userInfo.user_address || 'Not available' }}</span>
      </div>
      <div class="user-item">
        <span>Birthday</span>
        <span>{{ userInfo.user_birthday || 'Not available' }}</span>
      </div>
      <div class="user-item">
        <span>Personalized Signature</span>
        <span>{{ userInfo.user_sign || 'Not available' }}</span>
      </div>
    </div>
  </div>
</template>

<script>
  import {mapState} from 'vuex';
  import {mapActions} from 'vuex'

  export default {
    data() {
      return {
      }
    },
    computed: {
        ...mapState(["userInfo"])
    },
  }
</script>

<style scoped lang="stylus" ref="stylesheet/stylus">
  .user-detail
    width 70%
    height 100%
    margin 20px auto
    .user-detail-top
      width 100%
      height 60px
      line-height 60px
      padding-left 10px
      font-weight bold
    .user-detail-group
      .user-icon
        height 60px
        padding 0 10px
        background-color #fff
        border-bottom: 1px solid #e0e0e0
        display flex
        justify-content space-between
        align-items center
        img
          width 50px
          height 50px
          border-radius 50%
      .user-item
        height 50px
        padding 0 10px
        background-color #fff
        border-bottom: 1px solid #e0e0e0
        display flex
        justify-content space-between
        align-items center
    .right-title-color
      color #999
      font-size 14px
</style>

