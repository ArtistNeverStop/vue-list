<template>
	<div id="app">
		<img src="./assets/logo.png">
		<list tag="section" :list="[{name:'padex', age: 18}, {name:'ana', age: 17}, {name:'diego', age: 17}]"
			:initial-filters="[[['name', 'includes', 'ana'], '||', ['name', 'includes', 'diego']], '||', ['age', '>=', 18]]">
			<template slot="list" scope="parentList">
			<list tag="section" :list="[{name: 'jancho', age: 19}, {name: 'lala', age:60}]">
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
							<input v-model="parentList.filters[0][0][2]" type="text">
							<button>Able foo.bar</button>
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
