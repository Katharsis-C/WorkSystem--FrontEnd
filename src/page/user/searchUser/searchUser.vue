<template>
  <div class="search-user">
    <div class="detail">
      <div class="detail__title">
        <h2>查询条件</h2>
      </div><!-- / detail__title -->
      <div class="detail__content">
        <p>用户联系电话</p>
        <cv-text-input v-model="userPhone" type="text" placeholder="联系电话"></cv-text-input>

        <cv-button label="查询" :click="submit"></cv-button>
      </div><!-- / detail__content -->
    </div><!-- / detail -->

    <template v-for="task in search">
      <task :task="task" v-on:dosth="watchTask"></task>
    </template>

    <div class="watchsth" :class="{ 'open': isOpen }">
      <div class="detail">
        <div class="detail__title">{{ '#' + currentTask }}</div><!-- / detail__title -->
        <div class="detail__content">
          <template v-for="task in search" v-if="task.id === currentTask">
            <template v-for="record in task.history">
              <div class="record">
                <p>维修日期：{{ record.time.slice(0, 10) + ' ' + record.time.slice(11, 16) }}</p>
                <p>维修人员：{{ record.name + ' ' + record.work_number }}</p>
                <p>
                  维修状态：{{ record.record[record.record.indexOf('status=') + 7] === '0' ? '未解决'
                  : record.record[record.record.indexOf('status=') + 7] === '1' ? '已解决'
                    : record.record[record.record.indexOf('status=') + 7] === '2' ? '推迟' : '已上报'
                  }}</p>
                <p>
                  维修简述：{{ record.record[0] === 'a' ?
                  record.record.slice(record.record.indexOf('introduction=') + 13, record.record.indexOf('operator='))
                  : record.record.slice(record.record.indexOf('introduction') + 13)
                  }}</p>
              </div><!-- / record -->
            </template>
          </template>
        </div><!-- / detail-content -->
      </div><!-- / detail -->
    </div><!-- / watchsth -->
  </div><!-- / search-user -->
</template>

<script>
  import task from '@/components/task.vue'
  import cvTextInput from '@/components/cv-textInput.vue'
  import cvButton from '@/components/cv-button.vue'
  import {mapState} from 'vuex'

  export default {
    components: {
      task,
      cvTextInput,
      cvButton
    },
    data () {
      return {
        currentTask: '',
        userPhone: '',
        search: []
      }
    },
    computed: {
      ...mapState({
        isOpen: state => state.base.isOpen
      })
    },
    methods: {
      watchTask (id) {
        this.currentTask = id
        this.$store.dispatch('openSth')
      },
      submit () {
        if (!(this.userPhone.search(/\d{11}/) !== -1 && this.userPhone.length === 11)) {
          this.$store.dispatch('openMsg', '手机号码输入错误')
          return
        }

        let postData = JSON.stringify({
          model: 3,
          telephone_number: this.userPhone
        })
        this.$store.dispatch('POST_SEARCH', postData).then(data => {
          if (data.success) {
            this.search = data.return
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
  .record {
    padding: .5rem 0;
    border-bottom: 1px solid #e6e6e6;
  }

  .record p {
    margin: .2rem !important;
  }

  .cv-button {
    width: 100%;
    margin-top: 10px;
  }
</style>
