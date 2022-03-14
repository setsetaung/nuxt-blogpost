<template>
  <div>
    <h1>
      BlogsList
      <div v-if="user">
        <v-btn to="/blogs">
          <v-icon small>
            mdi-plus
          </v-icon>
        </v-btn>
      </div>
    </h1>
    <v-container>
      <v-row>
        <v-col v-for="blog in blogs" :key="blog.id" cols="12">
          <v-card>
            <v-card-title style="font-size:2em">
              {{ blog.title }}
            </v-card-title>
            <v-card-subtitle style="font-size:1.5em">
              {{ blog.content }}
            </v-card-subtitle>
            <v-card>
              <h5>Comments</h5>
              <ul v-for="com in comments" :key="com.id">
                <div v-if="blog.id === com.blog_id.id">
                  <li style="font-size:0.875em">
                    {{ com.comment }}
                  </li>
                </div>
              </ul>
            </v-card>
            <div v-if="user">
              <v-card-actions>
                <v-btn @click="remove(blog.id)">
                  <v-icon small>
                    mdi-delete
                  </v-icon>
                </v-btn>
                <v-btn :to="{ name: 'comments', query: { id: blog.id }}">
                  <v-icon small>
                    mdi-comment
                  </v-icon>
                </v-btn>
                <v-btn :to="{ name: 'editblog', query: { id: blog.id }}">
                  <v-icon small>
                    mdi-lead-pencil
                  </v-icon>
                </v-btn>
              </v-card-actions>
            </div>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import { collection, deleteDoc, doc, onSnapshot, query, orderBy, where } from '@firebase/firestore'
import { db } from '../plugins/firebase'
const blogsCollectionRef = collection(db, 'blogs')
const commentsCollectionRef = collection(db, 'comments')
const q = query(blogsCollectionRef, orderBy('created_at', 'desc'))

export default {
  name: 'BlogsListPage',
  middleware: 'authenticated',
  data () {
    return {
      blogs: [],
      title: '',
      content: '',
      comments: [],
      comment: '',
      blog_id: ''
    }
  },
  computed: {
    user () {
      return this.$store.state.user
    }
  },
  mounted () {
    onSnapshot(q, (querySnapshot) => {
      this.blogs = querySnapshot.docs.map(doc => ({ ...doc.data(), id: doc.id }))
    })
    onSnapshot(commentsCollectionRef, (querySnapshot) => {
      this.comments = querySnapshot.docs.map(doc => ({ ...doc.data(), id: doc.id }))
    })
  },
  methods: {
    remove (id) {
      const blogsCollectionRef = doc(db, 'blogs', id)
      const q = query(commentsCollectionRef, where('blog_id', '==', blogsCollectionRef))
      onSnapshot(q, (querySnapshot) => {
        const comm = querySnapshot.docs.map(doc => ({ ...doc.data(), id: doc.id }))
        comm.forEach((element) => {
          const commentDocumentRef = doc(db, 'comments', element.id)
          deleteDoc(commentDocumentRef)
        })
      })
      deleteDoc(blogsCollectionRef)
    }
  }
}
</script>
