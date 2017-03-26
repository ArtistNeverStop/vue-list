<script>
export default {
  name: 'list',
  props: {
  	tag: String,
  	propItems: Array,
  	propFilters: {
  		type: Object,
  		default: function(){
  			return {}
  		}
  	}
  },
  data () {
    return {
    	items: this.propItems,
    	filters: this.propFilters
    }
	},
  render: function($createElement){
  	return $createElement(this.tag, {}, this.$scopedSlots.content(this))
  },
  computed: {
  	filtered: function(){
  		return	this.items.filter(item => {
  			let able = false;
		  	for(let attr in this.filters){
		  		able = _.get(item, attr) == this.filters[attr].split('==')[1].split('\'').join('')
		  	}
  			return true;//able
  		});
  	}
  },
  mounted(){
  	for(let attr in this.filters){
  		let disable = this.filters[attr].startsWith('(') && this.filters[attr].endsWith(')')
  		if(disable){
  			let filter = this.filters[attr].substr(1).slice(0, -1).split(' ')
  		}
	  	console.log(filter, disable)
  	}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>