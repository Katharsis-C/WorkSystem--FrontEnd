<template>
  <div class="search-work">
    <div class="detail">
      <div class="detail__title">
        <h2>查询条件</h2>
      </div><!-- / detail__title -->
      <div class="detail__content">
        <label for="begin">起始日期</label>
        <input v-model="beginDate" type="date" id="begin">
        <label for="end">结束日期</label>
        <input v-model="endDate" type="date" id="end">

        <cv-button label="查询" :click="submit"></cv-button>
      </div><!-- / detail__content -->
    </div><!-- / detail -->

    <div class="detail day-detail" :class="{ active:isActive }">
      <div class="detail__title">
        <h2>{{ beginDate + '到' + endDate + '的值班情况'}}</h2>
      </div><!-- / detail__title -->
      <div class="detail__content">
        <p>当天共有{{ overview.done + overview.undone + overview.reported + overview.tomorrow }}单</p>
        <p>已解决{{ overview.done }}单</p>
        <p>未解决{{ overview.undone }}单</p>
        <p>推迟{{ overview.reported }}单</p>
        <p>上报{{ overview.tomorrow }}单</p>
      </div><!-- / detail__content -->
    </div><!-- / detail -->
  </div><!-- / search-work -->
</template>

<script>
  import cvButton from '@/components/cv-button.vue'

  export default {
    components: {
      cvButton
    },
    data () {
      return {
        isActive: false,
        beginDate: new Date().getFullYear() + '-' +
        (new Date().getMonth() + 1 < 10 ? '0' + (new Date().getMonth() + 1) : new Date().getMonth() + 1) + '-' +
        (new Date().getDate() < 10 ? '0' : '') + new Date().getDate(),
        endDate: new Date().getFullYear() + '-' +
        (new Date().getMonth() + 1 < 10 ? '0' + (new Date().getMonth() + 1) : new Date().getMonth() + 1) + '-' +
        (new Date().getDate() < 10 ? '0' : '') + new Date().getDate(),
        overview: {
          done: 0,
          undone: 0,
          reported: 0,
          tomorrow: 0
        }
      }
    },
    methods: {
      submit () {
        let postData = JSON.stringify({
          model: 2,
          start_date: this.beginDate,
          end_date: this.endDate
        })
        this.$store.dispatch('POST_SEARCH', postData).then(data => {
          if (data.success) {
            this.$set(this.overview, data)
            this.isActive = true
          } else {
            this.$store.dispatch('openMsg', '服务器出问题了')
          }

          this.$store.dispatch('endLoading')
        })
      }
    }
  }
</script>

<style scoped>
  .day-detail {
    opacity: 0;
    transition: opacity .5s linear;
  }

  .day-detail.active {
    opacity: 1;
  }

  .cv-button {
    width: 100%;
  }
</style>
