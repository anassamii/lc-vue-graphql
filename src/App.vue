<template>
  <div id="nav">
    <router-link to="/">Home</router-link>
    <router-link to="/about">About</router-link>
    <router-link v-if="loggedIn" to="/create">Create</router-link>
    <router-link v-if="loggedIn" to="/me">Me</router-link>
    <router-link v-if="loggedIn && me?.is_admin" to="/admin">Admin</router-link>
    <router-link v-if="!loggedIn" to="/register">Register</router-link>
    <router-link v-if="!loggedIn" to="/login">Login</router-link>
    <a href="#" v-if="loggedIn" @click.prevent="logout">Logout</a>
  </div>
  <router-view />
</template>

<script>
import gql from 'graphql-tag'

export default {
  computed: {
    loggedIn() {
      return localStorage.getItem('apollo-token')
    },
  },
  apollo: {
    me: {
      query: gql`
        query {
          me {
            is_admin
          }
        }
      `,
    },
  },
  methods: {
    logout() {
      this.$apollo
        .mutate({
          mutation: gql`
            mutation logout {
              logout {
                message
              }
            }
          `,
        })
        .then(data => {
          console.log(data)
          localStorage.removeItem('apollo-token')
          // this.$router.push({ name: 'Home' })
          window.location.href = '/'
        })
        .catch(error => {
          console.log(error.graphQLErrors)
        })
    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}

ul {
  padding-left: 0;
}

ul > li {
  list-style-type: none;
}

#nav a {
  margin-right: 20px;
}
</style>
