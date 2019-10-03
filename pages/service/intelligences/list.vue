<template>
   <main>
        <div class="container-wrapper">
            <header data-role="app-title">커뮤니티게시물<br /><strong>특이동향 리스트</strong></header>
            <div class="grid grid-half" v-if="getCount.reduce((a, b) => a + b) !== 0">
              <div data-role="intDescription" class="uk-card uk-card-default uk-card-body" v-if='!chkError'>
                  <h2 class="uk-card-title uk-text-primary" style="font-size: 3rem; text-shadow: 3px 3px 10px rgba(0,0,0,.15);"><i class="fas fa-file-invoice"></i></h2>
                  <span class="uk-label uk-label-danger uk-position-small uk-position-top-right">등록된 게시물</span>
                  <hr>
                  <p class="uk-text-meta uk-margin-small-bottom">등록된 게시물</p>
                  <div class="count uk-margin-bottom-small uk-flex">
                    <h3 class="uk-width-2-3 uk-text-lead uk-text-bold">특이동향</h3>
                    <div class="num uk-width-1-3 uk-text-lead uk-text-right"><strong>{{ getCount[0] }}</strong></div>
                  </div>
                  <div class="count uk-margin-small uk-flex">
                    <h3 class="uk-width-2-3 uk-text-lead uk-text-bold">법규위반의심</h3>
                    <div class="num uk-width-1-3 uk-text-lead uk-text-right"><strong>{{ getCount[1] }}</strong></div>
                  </div>
              </div>
              <div data-role="intDescription" class="uk-card uk-card-default uk-card-body" v-if='!chkError'>
                  <h2 class="uk-card-title uk-text-primary" style="font-size: 3rem; text-shadow: 3px 3px 10px rgba(0,0,0,.15);"><i class="fas fa-file-invoice"></i></h2>
                  <span class="uk-label uk-label-danger uk-position-small uk-position-top-right">게시물 특성 분류</span>
                  <hr>
                  <p class="uk-text-meta uk-margin-small-bottom">게시물 출처</p>
                  <div class="count uk-margin-bottom-small uk-flex">
                    <h3 class="uk-width-2-3 uk-text-lead uk-text-bold">SNS</h3>
                    <div class="num uk-width-1-3 uk-text-lead uk-text-right"><strong>{{ getCntTypes['social'] }}</strong></div>
                  </div>
                  <div class="count uk-margin-small uk-flex">
                    <h3 class="uk-width-2-3 uk-text-lead uk-text-bold">커뮤니티</h3>
                    <div class="num uk-width-1-3 uk-text-lead uk-text-right"><strong>{{ getCntTypes['comm'] }}</strong></div>
                  </div>
              </div>
            </div>
            <Message icon="fas fa-exclamation-triangle" message="데이터가 없습니다." alert="uk-alert-danger" v-else :class="{ 'valign-center': true, 'full-width': true }" />
            <Message icon="fas fa-exclamation-triangle" message="데이터베이스 연결에 실패하였습니다." alert="uk-alert-danger" v-show="chkError" :class="{ 'valign-center': true, 'full-width': true }" />
            <script v-if='chkError'>swal({ type: 'error', title: '데이터베이스 연결에 실패하였습니다.', width: 750, confirmButtonText: `<i class='fas fa-check-circle'></i> 확인`, footer: '<p>데이터베이스 정상 동작 여부를 확인하시기 바랍니다.</p>' })</script>
            <Intelligences v-else />
        </div>
    </main>
</template>

<script>
import Message from '~/components/Message.vue'
import Intelligences from '~/components/Intelligences.vue'
export default {
  layout: 'service',
  middleware: 'auth',
  components: {
    Message, Intelligences
  },
  computed: {
    getCount () {
      return this.$store.getters['intelligences/getCount']
    },
    chkError () {
      return this.$store.getters['intelligences/chkError']
    },
    getCntTypes () {
      return this.$store.getters['intelligences/getCntTypes']
    }
  },
  head () {
    return {
      title: '커뮤니티 게시물 특이동향 관리 :: 커뮤니티게시물모니터링포털'
    }
  },
  data () {
    return {
      items: [],
      notice: '데이터 수신 중입니다. 잠시만 기다려주세요.'
    }
  },
  created () {
    this.$store.dispatch('intelligences/getData', { account: this.$store.getters['getUserEmail'], mode: this.$store.getters['getUserMode'] })
  },
  mounted () {
    this.notice = ''
  }
}
</script>

<style scoped lang="scss">
  @import "~assets/css/components/main";
  @import "~assets/css/layout/grid";
</style>
