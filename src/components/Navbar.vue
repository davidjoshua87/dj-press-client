<template>
  <div>
    <nav class="navbar navbar-expand-lg navbar-light fixed-top" id="mainNav">
      <div class="container">
        <button class="navbar-collape navbar-right" type="button" data-toggle="collapse" data-target="#navbarResponsive" aria-controls="navbarResponsive"
          aria-expanded="false" aria-label="Toggle navigation">
          <router-link class="navbar-brand" :to="{ path: '/'}">
            <i class="fa fa-bars"> Dj-Press</i>
          </router-link>
        </button>
        <div class="collapse navbar-collapse" id="navbarResponsive">
          <ul class="navbar-nav ml-auto">
            <router-link :to="{path: '/'}">
              <li class="nav-item">
                <a class="nav-bar active btn">Home</a>
              </li>
            </router-link>

            <router-link :to="{path: '/'}" v-if='user.id !== "" && user.id !== null'>
              <li class="nav-item">
                <a id="show-modal" @click="showModalAdd = true" class="nav-bar active btn" data-toggle="modal" data-target="#modalAdd">Add Post</a>
              </li>
            </router-link>

            <router-link :to="{path: '/profile'}" v-if='user.id !== "" && user.id !== null'>
              <li class="nav-item">
                <a class="nav-bar active btn">Welcome: {{user.username}}</a>
              </li>
            </router-link>

            <router-link :to="{path: '/'}" v-if='user.id !== "" && user.id !== null'>
              <li @click='emitLogout' class="nav-item">
                <a class="nav-bar active btn">Logout</a>
              </li>
            </router-link>

            <router-link :to="{path: '/masuk'}" v-if='user.id === "" || user.id === null'>
              <li class="nav-item">
                <a class="nav-bar active btn">Login</a>
              </li>
            </router-link>

            <router-link :to="{path: '/daftar'}" v-if='user.id === "" || user.id === null'>
              <li class="nav-item">
                <a class="nav-bar active btn">Sign Up</a>
              </li>
            </router-link>
          </ul>
        </div>
      </div>
    </nav>

    <div v-if="showModalAdd">
      <transition name="modal">
        <div class="modal-mask">
          <div class="modal-wrapper">
            <div class="modal-dialog">
              <div class="modal-content">
                <div class="modal-header">
                  <button type="button" @click="showModalAdd=false">
                    <span aria-hidden="true">&times;</span>
                  </button>
                  <h4 class="modal-title">Add Article</h4>
                </div>
                <div class="modal-body">
                  <div class="panel-body">
                    <div class="form-group">
                      Title:
                      <input class="form-control" placeholder="Title" name="title" type="text" v-model="newPost.title">
                    </div>
                    <div class="form-group">
                      Category:
                      <input class="form-control" placeholder="Category" name="category" type="text" v-model="newPost.category">
                    </div>
                    <div class="form-group">
                      Content:
                      <textarea class="form-control" name="content" v-model="newPost.content"></textarea>
                    </div>
                    <div class="form-group">
                      Picture:
                      <input type="file" class="form-control" @change="postimageHandler">
                    </div>
                    <input class="btn btn-lg btn-success btn-block" type="submit" value="Submit" @click="emitUpload">
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  import swal from 'sweetalert2'
  import Editor from '@tinymce/tinymce-vue';
  import {
    mapState
  } from 'vuex'
  const baseURL = 'http://localhost:3000'
  export default {
    data() {
      return {
        showModalAdd: false,
        newPost: {
          title: '',
          content: '',
          category: '',
          pic: '',
        }
      }
    },
    components: {
      'editor': Editor
    },
    computed: {
      ...mapState([
        'user'
      ])
    },
    methods: {
      postimageHandler: function (event) {
        this.newPost.pic = event.target.files[0]
      },
      emitUpload: function () {
        let formData = new FormData()
        formData.append('title', this.newPost.title)
        formData.append('content', this.newPost.content)
        formData.append('category', this.newPost.category)
        formData.append('pic', this.newPost.pic)
        axios.post(`${baseURL}/articles`, formData)
          .then(response => {
            console.log('success add new post')
            this.$store.dispatch('getArticles')
            window.location.href = ('/')
          })
      },
      emitLogout: function () {
        localStorage.setItem('token', '')
        localStorage.setItem('id', '')
        localStorage.setItem('username', '')
        localStorage.setItem('email', '')
        localStorage.setItem('name', '')
        this.$store.dispatch('clearUser')
        swal(
          'Success!',
          'Your are log out now.',
          'success'
        )
      },

    }
  }
</script>

<style scoped>
  #mainNav {
    position: absolute;
    border-bottom: 1px solid #e9ecef;
    background-color: rgba(255, 255, 255, 0);
  }

  #mainNav .navbar-brand {
    font-weight: 800;
    color: #343a40;
  }

  #mainNav .navbar-toggler {
    font-size: 12px;
    font-weight: 800;
    padding: 13px;
    text-transform: uppercase;
    color: #343a40;
  }

  #mainNav .navbar-nav>li.nav-item>a {
    font-size: 12px;
    font-weight: 800;
    letter-spacing: 1px;
    text-transform: uppercase;
  }

  .modal-mask {
    position: relative;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, .5);
    display: table;
    transition: opacity .3s ease;
  }

  .modal-content {
    height: 150%;
    width: 150%;
    position: static;
  }

  .modal-wrapper {
    display: table-cell;
    vertical-align: middle;
  }
</style>