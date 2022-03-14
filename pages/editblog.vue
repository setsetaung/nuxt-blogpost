<template>
  <v-container>
    <div v-if="user">
      <h1>Edit Blog</h1>
      <v-form @submit.prevent="updateBlog">
        <v-text-field v-model="blog.title" dense label="Title" />
        <v-textarea v-model="blog.content" dense label="Content" />
        <v-btn type="submit">
          Update
        </v-btn>
      </v-form>
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
import { doc, getDoc, serverTimestamp, updateDoc } from '@firebase/firestore'
import { db } from '~/plugins/firebase'

export default {
  name: 'EditPage',
  middleware: 'authenticated',
  asyncData ({ params }) {
    const id = params.id
    return { id }
  },
  data () {
    return {
      blog: {
        title: '',
        content: '',
        created_at: ''
      }
    }
  },
  computed: {
    user () {
      return this.$store.state.user
    }
  },
  async mounted () {
    await this.getBlog()
  },
  methods: {
    async getBlog () {
      const usersCollectionRef = doc(db, 'blogs', this.$route.query.id)
      const docSnap = await getDoc(usersCollectionRef)
      if (docSnap.exists()) {
        this.blog = docSnap.data()
      }
    },
    async updateBlog () {
      const usersCollectionRef = doc(db, 'blogs', this.$route.query.id)
      const docSnap = await updateDoc(usersCollectionRef, {
        title: this.blog.title,
        content: this.blog.content,
        updated_at: serverTimestamp()
      })
      this.$router.push('/')
      return docSnap
    }
  }
}
</script>

<style>

</style>
