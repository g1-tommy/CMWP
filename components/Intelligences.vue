<template>
    <div class="result uk-overflow-auto uk-margin-top" data-role="communities-results">
      <table class="uk-table uk-table-divider uk-table-striped uk-table-middle uk-table-responsive uk-table-small uk-table-hover" v-if='items.length > 0'>
        <thead>
          <tr>
            <th class="uk-width-small uk-table-shrink">유형</th>
            <th class="uk-width-small">사이트</th>
            <th class="uk-width-small">출처구분</th>
            <th class="uk-table-expand">제목</th>
            <th class="uk-width-small">닉네임</th>
            <th class="uk-width-small">저장일시</th>
            <th>작업</th>
          </tr>
        </thead>
        <tbody>
          <tr :key="item.identifier" v-for="item in items">
            <td>{{ int_type(item.item.type) }}</td>
            <td>{{ item.item.meta.site }}</td>
            <td>{{ item.item.meta.int_type === 'INTCOMM'? '커뮤니티' : 'SNS' }}</td>
            <td><a target="_blank" :href="item.item.link">{{ item.item.title.replace(/(^\s*)|(\s*$)/gi, '') }}</a></td>
            <td>{{ item.item.author }}</td>
            <td>{{ new Date(item.item.saved_at).toISOString().split('T')[0] }}</td>
            <td>
              <button @click="screenshotPreview(item.screenshot)" class="uk-button uk-button-secondary uk-text-nowrap">
                <i class="fas fa-images"></i>
                <span class="btn-desc">페이지 캡처 보기</span>
              </button>
              <button class="uk-button uk-button-primary uk-text-nowrap" :onclick="getViewer(item._id)">
                <i class="fas fa-file-pdf"></i>
                <span class="btn-desc">보고서 (PDF)</span>
              </button>
              <button class="uk-button uk-button-danger uk-text-nowrap" @click='deleteInt(item._id)'>
                <i class="fas fa-trash"></i>
                <span class="btn-desc">삭제</span>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
</template>

<script>
import axios from 'axios'

export default {
  computed: {
    items () {
      return this.$store.getters['intelligences/getData']
    }
  },
  methods: {
    screenshotPreview(base64) {
      swal({
        width: '90vw',
        title: '스크린샷 미리보기',
        heightAuto: false,
        confirmButtonText: `<i class='fas fa-check-circle'></i> 확인`,
        html: `<hr /><img src='${base64}' alt='Screenshot' />`
      })
    },
    int_type (type) {
      if (type === 'illegality') return '위법의심'
      else if (type === 'trend') return '특이동향'
    },
    getViewer (link) {
      return `javascript:location.href = '/service/intelligences/report/view?id=${link}'`
    },
    deleteInt (id) {
      swal({
        title: '삭제할까요?',
        text: '삭제 후 복원할 수 없습니다.',
        type: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#0054A6',
        cancelButtonColor: '#ed1b2f',
        confirmButtonText: '<i class="far fa-trash-alt"></i> 삭제',
        cancelButtonText: '<i class="fas fa-ban"></i> 취소',
        allowOutsideClick: false,
        showLoaderOnConfirm: true,
      }).then((result) => {
        if (result.dismiss === Swal.DismissReason.cancel) {}
        else {
          axios.delete(`/api/v2/intelligences/${id}`).then(response => {
            if (response.data.status.code === 200) {
              this.$store.dispatch('intelligences/removeItem', id)
              swal('삭제 완료', '삭제되었습니다.', 'success')
            } else {
              throw Error(response)
            }
          })
        }
      }).catch(response => {
        swal('에러 발생', response, 'error')
      })
    }
  }
}
</script>

<style lang="scss">
table {
  font-weight: 600;
  & a {
    color: #0054a6;
    &:hover {
      text-decoration: none;
    }
  }
}
</style>
