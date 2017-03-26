<template>
  <div id="app">
    <img src="./assets/logo.png">
    <list tag="div" :prop-items="[{name:{first:'pancho',last:'ramiro'}}, {name:'ana'}]" :prop-filters="{'name.last' : '(|| == ramiro)'}">
      <template slot="content" scope="parentList">
      <list tag="section" :prop-items="[{name:'jancho'}, {name:'lala'}]">
        <template slot="content" scope="childList">
          <div class="flex">
            <ul>
              <li v-for="(item, index) in childList.items">
                {{index}}<p>{{item.name}}</p><button @click="parentList.items.push(childList.items.splice(index, 1)[0])">pluck</button>
              </li>
            </ul>
            <ul>
              <li v-for="(item, index) in parentList.filtered">
                {{index}}<p>{{item.name}}</p><button @click="childList.items.push(parentList.items.splice(index, 1)[0])">pluck</button>
              </li>
            </ul>
          </div>
          </template>
        </list>
      </template>
    </list>
    <!-- <hello></hello> -->
  </div>
</template>

<script>
import Hello from './components/Hello'
import List from './components/List'

export default {
  name: 'app',
  components: {
    Hello,
    List
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.flex{
  display: flex;
  justify-content: center;
}
</style>
