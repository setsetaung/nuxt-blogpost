<template>
  <v-container>
    <div v-if="user">
      <h1>Write Comment</h1>
      <v-textarea v-model="comment" label="Comment" />
      <v-btn color="primary" @click="add">
        Add Comment
      </v-btn>
    </div>
    <div v-else>
      <p>先ずログインしてください</p>
      <nuxt-link to="/login">
        Login
      </nuxt-link>
    </div>
  </v-container>
</template>

<script>
import { doc, addDoc, collection, onSnapshot } from '@firebase/firestore'
import { serverTimestamp } from 'firebase/firestore'
import { db } from '../plugins/firebase'
const commentsCollectionRef = collection(db, 'comments')
export default {
  name: 'CommentsPage',
  middleware: 'authenticated',
  data () {
    return {
      comments: [],
      comment: ''
    }
  },
  computed: {
    user () {
      return this.$store.state.user
    }
  },
  mounted () {
    onSnapshot(commentsCollectionRef, (querySnapshot) => {
      this.comments = querySnapshot.docs.map(doc => ({ ...doc.data(), id: doc.id }))
    })
  },
  methods: {
    add (id) {
      if (this.comment.trim()) {
        addDoc(commentsCollectionRef, {
          comment: this.comment,
          blog_id: doc(db, 'blogs', this.$route.query.id),
          created_at: serverTimestamp()
        }).then(() => {
          this.comment = ''
          this.blog_id = ''
        })
        this.$router.push('/bloglist')
      }
    }
  }
}
</script>

<style>

</style>
