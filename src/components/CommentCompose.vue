
<template>
  <comment-form
    :isSubmitting="isSubmitting"
    :content="commentContent"
    :submitCallback="submitCallback"
  >
  </comment-form>
</template>

<script>
import CommentForm from '@/components/CommentForm.vue'
import { mapState, mapActions } from 'vuex'
export default  {
  name: 'comment-compose',
  props: {
    rootEntityType: {
      type: String,
      required: false
    },
    entityId: {
      type: String,
      required: true
    }
  },
  data () {
    return {
      isSubmitting: false,
      commentContent: ''
    }
  },
  components: {
    CommentForm
  },
  computed: {
    ...mapState({
    })
  },
  methods: {
    ...mapActions(['commentsCreate', 'commentsFetch']),
    submitCallback ({content, mentions}) {
      this.isSubmitting = true
      // First update then change back to empty to clear: this.commentContent = content
      this.commentContent = content
      this.commentsCreate({
        entityId: this.entityId,
        rootEntityType: this.rootEntityType,
        mentions,
        content
      })
        .then((response) => {
          this.isSubmitting = false
          this.commentContent = ''
          // Fetch comments
          this.commentsFetch({
            entityId: this.entityId
          })
        })
        .catch((error) => {
          this.isSubmitting = false
          this.$toasted.error(error.response.data.message)
        })

    }
  }
}
</script>
