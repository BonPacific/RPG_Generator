<template>
  <div id="app" v-bind:class="{dark_mode: dark_mode}">
    <div id="background"></div>
    <Header></Header>
    <Main></Main>
  </div>
</template>

<script>
import Header from './components/Header.vue'
import Main from './components/Main.vue'

export default {
  name: 'app',
  components: {
    Header,
    Main
  },
  created: function () {
		var self = this;
		window.eventBus.$on('view_mode_change', function(data) { 
      self.dark_mode = data; 
    });
  },
  data () {
    return {
      dark_mode: false
    }
  }
}
</script>

<style>
html {
  min-height:100%;
  position:relative;
}
body {
  height:100%;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  height:100%;
}
#background{
    position:absolute;
    top:0;
    bottom:0;
    left:0;
    right:0;
    overflow:hidden;
    z-index:-1; /* Remove this line if it's not going to be a background! */
    background-color:#eee;
}


h1, h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #B71C1C;
}


#app .form-control {
	/* width:auto; */
	border-radius:0;
}

.nav-tabs {
  border:none;
}

.btn {
	border-radius:0;
}

label {
  font-weight:600;
}

.header {
  background-color:white;
}

.card {
  border-radius:0;
}

/* Dark Mode */
.dark_mode .header,
.dark_mode #background{
  background-color:#000;
}

#app.dark_mode {
  color:#fff;
}

.dark_mode .header {
  border-bottom-color:steelblue;
}

.dark_mode .header .logo svg{
  fill:steelblue;
}

.dark_mode .card-body,
.dark_mode .controls {
  background-color:#2c3e50;
}

.dark_mode a {
  color: steelblue;
}

.dark_mode .text-muted {
  color:#ccc !important;
}

.dark_mode .btn-outline-dark,
.dark_mode .btn-dark {
  /* border-color:#aaa; */
  color:#ccc;
  background-color:#555;
}

.dark_mode .btn-outline-dark:hover,
.dark_mode .btn-dark:hover {
  /* background-color:#ddd; */
  color:white;
  background-color:#333;
  border-color:white;
}

.dark_mode .btn-dark {
  border-color:#eee;
}

.dark_mode a.nav-link.active {
  background-color:#2c3e50;
  color:#ccc;
  border-color:transparent;
}

.dark_mode a.nav-link:hover {
  border-color:#2c3e50;
}

.dark_mode input,
.dark_mode input:focus {
  background-color:#333;
  border:0;
  color:white;
}

.dark_mode .card {
  border:0;
}
</style>
