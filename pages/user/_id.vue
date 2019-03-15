<template>
  <v-layout
    column
    justify-center
    align-center
  >
    <v-flex
      xs12
      sm8
      md6
    >
      <v-card v-if="user !== null">
        <v-btn
            color="primary"
            flat
            nuxt
            :to="'/'"
          >Retour</v-btn>
        <v-card-title class="headline">Détails de "{{ user.username }} {{ user.name }}" </v-card-title>
        <v-card-text>
          <p>Id: <strong>{{ user.id }}</strong></p>
          <p>Email: <strong>{{ user.email }}</strong></p>
          <p>Site web: <strong>{{ user.website }}</strong></p>
          <p>Téléphone: <strong>{{ user.phone }}</strong></p>
          <p>Adresse: <strong>{{ fullAddress }}</strong></p>
          <p>Compagnie: <strong>{{ fullCompany }}</strong></p>
        </v-card-text>
        <v-card-text>
          <div>
            <h2>Liste de ses posts</h2>
            <v-card-text v-for="singlePost in posts" :key="singlePost.id">
              <p>{{ '(' + singlePost.id + ') ' + singlePost.title }}</p>
              <p>Message: {{ singlePost.body }}</p>
              <p>Commentaires :</p>
              <ul>
                <li v-for="com in singlePost.comments">
                  <p>{{ '(' + com.id + ') ' + com.name + ' - ' + com.email }}</p>
                  <p>{{ com.body }}</p>
                </li>
              </ul>
            </v-card-text>
          </div>
          <div>
            <h2>Liste de ses todos</h2>
            <template v-for="singleTodo in todos">
              <v-list-tile :key="singleTodo.id">
                <v-list-tile-content>
                  <v-list-tile-title v-html="'(' + singleTodo.id + ') ' + singleTodo.title"></v-list-tile-title>
                </v-list-tile-content>
              </v-list-tile>
            </template>
          </div>
          <div>
            <h2>Liste de ses Albums</h2>
            <template v-for="singleAlbum in albums">
              <v-list-tile :key="singleAlbum.id">
                <v-list-tile-content>
                  <v-list-tile-title v-html="'(' + singleAlbum.id + ') ' + singleAlbum.title"></v-list-tile-title>
                </v-list-tile-content>
              </v-list-tile>
            </template>
          </div>
        </v-card-text>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import Logo from '~/components/Logo.vue'
import VuetifyLogo from '~/components/VuetifyLogo.vue'
import axios from 'axios'

export default {
  components: {
    Logo,
    VuetifyLogo
  },
  data() {
    return {
      userId: null,
      user: null,
      posts: [],
      todos: [],
      albums: []
    }
  },
  methods: {
    getUser() {
      return axios
      .get('https://jsonplaceholder.typicode.com/users?id='+this.userId)
      .then(response => {
        console.log('user', response.data);
        if(response.data.length !== 0) {
            return response.data[0];
        } else {
          throw 'User unknow';
        }
      })
    },
    getPosts() {
      return axios
      .get('https://jsonplaceholder.typicode.com/posts?userId='+this.userId)
      .then(response => {
        console.log('posts', response.data);
        const listOfPromise = [];

        response.data.map(post => {
          listOfPromise.push(this.getComments(post.id).then(comments => {
            post.comments = comments;
          }));
        });

        return Promise.all(listOfPromise).then(() => {
          console.log('post with comments', response.data)
          return response.data;
        });
      })
    },
    getComments(postId) {
      return axios
      .get('https://jsonplaceholder.typicode.com/comments?userId='+this.userId+'&postId='+postId)
      .then(response => {
        console.log('comments from post '+postId, response.data);
        return response.data;
      })
    },
    getTodos() {
      return axios
      .get('https://jsonplaceholder.typicode.com/todos?userId='+this.userId)
      .then(response => {
        console.log('todos', response.data);

        return response.data;
      })
    },
    getAlbums() {
      return axios
      .get('https://jsonplaceholder.typicode.com/albums?userId='+this.userId)
      .then(response => {
        console.log('albums', response.data);
        return response.data;
      })
    }
  },
  mounted () {
    this.userId = this.$route.params.id;

    this.getUser().then(result => {
      this.user = result;

      return this.getPosts();
    }).then(result => {
      this.posts = result;

      return this.getTodos();
    }).then(result => {
      this.todos = result;

      return this.getAlbums();
    }).then(result => {
      this.albums = result;

    }).catch(err => {
      alert(err);
    })
  },
  computed: {
    fullAddress () {
      return this.user.address.street + ', ' + this.user.address.city + ', ' + this.user.address.zipcode
    },
    fullCompany () {
      return this.user.company.name + ', ' + this.user.company.catchPhrase + ', ' + this.user.company.bs
    }
  }
}
</script>

<style>
  h2 {
    margin-top: 32px;
  }

</style>
