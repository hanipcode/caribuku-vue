<template>
  <div id="app">
    <h1>
      CariBuku
    </h1>
    
    <p>
      <span> Learning project by hanipcode me@muhanif.id developed using vue js</span>
      <span> 
        You can view or download the project repository
        <a href="http://github.com/hanipcode/caribuku-vue">here</a>
      </span>
    </p>
    
    <search></search>
  </div>
</template>

<script>
import Search from 'components/Search';

export default {
  name: 'app',
  components: {
    Search,
  },
};
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  font-size: 1em;

}
h1 {
  font-size: 3.5em;
  margin: 20px 0;
  font-weight: bold;
  letter-spacing: 1px;
  text-align: center;
}
span {
  display: block;
  font-size: 0.85em;
  line-height: 2em;
  
  a{
    color: #000;
    font-weight: bold;
    text-decoration-style:dotted;
  }
}
</style>
