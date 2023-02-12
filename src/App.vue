<template>
  <v-app class="app">
    <v-main class="main">
      <HelloWorld/>
    </v-main>
  </v-app>
</template>

<script>
import HelloWorld from './components/HelloWorld';

export default {
  name: 'App',

  components: {
    HelloWorld,
},

  data: () => ({ 
  }),
  
  mounted(){ 
    },
    methods:{
    }
};
</script>
<style>
* {
  margin: 0;
  padding: 0;
  outline: 0;
  box-sizing: border-box;
  font-family: "Roboto", sans-serif;
}
body {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  background: #243740;
  width:100vw;
}
.app{
  width:100vw;
  background: #243740;
}
.main{
  width:100vw;
  background: #243740;
}


</style>