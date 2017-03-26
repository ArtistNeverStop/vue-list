<template>
	<div id="app">
		<img src="./assets/logo.png">
		<list tag="div" :prop-items="[{name:'padex', foo:{bar:'baz'}}, {name:'ana'}]"
			:prop-filters="{
					'name' : ['includes jancho', '||', ['includes a', '&&', 'includes p']],
					'!foo.bar' : ['||', '== baz']
				}">
			<template slot="list" scope="parentList">
			<list tag="section" :prop-items="[{name:'jancho'}, {name:'lala'}]">
				<template slot="list" scope="childList">
					<div class="flex">
						<ul>
							<h1>child</h1>
							<li v-for="(item, index) in childList.items">
								{{index}}<p>{{item.name}}</p><button @click="parentList.items.push(childList.items.splice(index, 1)[0])">pluck</button>
							</li>
						</ul>
						<ul>
							<h1>parent</h1>
							<li v-for="(item, index) in parentList.filtered">
								{{index}}<p>{{item.name}}</p><button @click="childList.items.push(parentList.items.splice(index, 1)[0])">pluck</button>
							</li>
							<input type="text" v-model="parentList.filters.name[0]">
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
