<template>
  <div id="wrapper">
    <nav class="navbar is-dark">
      <div class="navbar-brand">
        <router-link to="/" class="navbar-item">
          <strong>Djackets</strong>
        </router-link>

        <a @click="showMobileMenu = !showMobileMenu"
          href="#" aria-label="menu" aria-expanded="false"
          data-target="navbar-menu" class="navbar-burger"
        >
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>

      <div class="navbar-menu" id="navbar-menu" :class="{'is-active': showMobileMenu}">
        <div class="navbar-start">
          <div class="navbar-item">
            <form action="/search">
              <div class="field has-addons">

                <div class="control">
                  <input class="input" type="text" placeholder="What are you looking for?" name="query">
                </div>

                <div class="control">
                  <button class="button is-success">
                    <span class="icon">
                      <i class="fas fa-search"></i>
                    </span>
                  </button>
                </div>

              </div>
            </form>
          </div>
        </div>
        <div class="navbar-end">
          <div class="navbar-item">
            <CategoryMenu :category="category" />

            <div class="buttons">
            <template v-if="$store.state.isAuthenticated">
                <router-link to="/my-account" class="button is-light">My account</router-link>
            </template>
            <template v-else>
                <router-link to="/log-in" class="button is-light">Log in</router-link>
            </template>
              <router-link to="/cart" class="button is-success">
                <span class="icon"><i class="fas fa-shopping-cart"></i></span>
                <span>Cart ({{ cartTotalLength }})</span>
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </nav>

    <div
      :class="{'is-loading': $store.state.isLoading }"
      class="is-loading-bar has-text-centered"
    >
      <div class="lds-dual-ring"></div>
    </div>
    <section class="section">
      <router-view/>
    </section>

    <footer class="footer">
      <p class="has-text-centered">Copyright (c) 2021</p>
      <p class="has-text-centered mt-3">This Project is demostration VueJs and Django REST Framework</p>
      <p class="has-text-centered mt-3">
        Power by <a :href="codeWizzUrl" class="has-text-weight-semibold" target="_blank">CodeWizz</a>
      </p>
    </footer>
  </div>
</template>

<script>
import axios from 'axios'
import CategoryMenu from '@/components/CategoryMenu'
import { mapActions, mapState } from 'vuex'

export default {
  name: 'App',
  components: {
    CategoryMenu
  },
  data() {
    return {
      showMobileMenu: false,
      cart: {
        items: []
      },
      codeWizzUrl: 'https://codewizz.org/about/'
    }
  },
  beforeCreate() {
    this.$store.commit('initializeStore')
  },
  created(){
    this.initializeToken()
  },
  mounted() {
    this.cart = this.$store.state.cart
    this.fetchCategory()
  },
  methods: {
    initializeToken() {
      const token = this.$store.state.token
      if (token) {
        axios.defaults.headers.common['Authorization'] = "Token " + token
      } else {
        axios.defaults.headers.common['Authorization'] = ""
      }
    },
    ...mapActions(['fetchCategory'])
  },
  computed: {
    cartTotalLength() {
          let totalLength = 0
          for (let i = 0; i < this.cart.items.length; i++) {
              totalLength += parseInt(this.cart.items[i].quantity)
          }
          return totalLength
    },
    ...mapState(['category'])
  }
}
</script>

<style lang="scss">
@import '../node_modules/bulma';

.lds-dual-ring {
  display: inline-block;
  width: 80px;
  height: 80px;
}
.lds-dual-ring:after {
  content: " ";
  display: block;
  width: 64px;
  height: 64px;
  margin: 8px;
  border-radius: 50%;
  border: 6px solid #ccc;
  border-color: #ccc transparent #ccc transparent;
  animation: lds-dual-ring 1.2s linear infinite;
}
@keyframes lds-dual-ring {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
.is-loading-bar {
  height: 0;
  overflow: hidden;
  -webkit-transition: all 0.3s;
  transition: all 0.3s;
  &.is-loading {
    height: 80px;
  }
}
</style>

