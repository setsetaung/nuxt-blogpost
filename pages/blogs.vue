/* eslint-disable import/order */
/* eslint-disable import/no-duplicates */
<template>
  <v-container>
    <h1>New Blog</h1>
    <v-text-field v-model="title" label="Title" />
    <v-textarea v-model="content" label="Content" />
    <v-btn color="primary" @click="add">
      Add Blog
    </v-btn>
  </v-container>
</template>

<script>
import { addDoc, collection, onSnapshot } from '@firebase/firestore'
import { serverTimestamp } from 'firebase/firestore'
import { db } from '../plugins/firebase'
const blogsCollectionRef = collection(db, 'blogs')
export default {
  name: 'BlogsPage',
  middleware: 'authenticated',
  data () {
    return {
      blogs: [],
      title: '',
      content: ''
    }
  },
  computed: {
    user () {
      return this.$store.state.user
    }
  },
  mounted () {
    onSnapshot(blogsCollectionRef, (querySnapshot) => {
      this.blogs = querySnapshot.docs.map(doc => ({ ...doc.data(), id: doc.id })).sort((a, b) => a.created_at > b.created_at ? 1 : -1)
    })
  },
  methods: {
    add () {
      if (this.title.trim() && this.content.trim()) {
        addDoc(blogsCollectionRef, {
          title: this.title,
          content: this.content,
          created_at: serverTimestamp()
        }).then(() => {
          this.title = ''
          this.content = ''
        })
        this.$router.push('/bloglist')
      }
    }
  }
}
</script>

<style>

</style>
