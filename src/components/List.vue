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
  	return $createElement(this.tag, {}, this.$scopedSlots[this.$options._componentTag](this))
  },
  computed: {
  	filtered: function(){
  		return	this.items.filter(item => {
        let able = true
        for(let attr in this.filters){
          if(!attr.startsWith('!')){
            able = this.filter(item, able, this.filters[attr], attr, 0);
          }
        }
        return able
      });
    }
  },
  methods: {
    filter(object, able, values, attr, current){
      if(current == values.length){
        return able
      }
      switch (values[current]){
        case '&&':
          return able && this.filter(object, able, values, attr, current +1)
        break;
        case '||':
          return able || this.filter(object, able, values, attr, current +1)
        break;
        default:
          return this.filter(object, this.filterItem(object, values[current], attr), values, attr, current +1)
        break;
      }
    },
    filterItem(object, operation, attr){
      if(operation instanceof Array){
        return this.filter(object, true, operation, attr, 0);
      }
      let filter = operation.split(' ')
      switch (filter[0]){
        case '===':
          return _.get(object, attr) === filter[1]
        break;
        case '!==':
          return _.get(object, attr) !== filter[1]
        break;
        case '==':
          return _.get(object, attr) == filter[1]
        break;
        case '!=':
          return _.get(object, attr) != filter[1]
        break;
        case '>=':
          return _.get(object, attr) >= filter[1]
        break;
        case '<=':
          return _.get(object, attr) >= filter[1]
        break;
        case '<':
          return _.get(object, attr) < filter[1]
        break;
        case '>':
          return _.get(object, attr) > filter[1]
        break;
        case 'includes':
          return _.get(object, attr).includes(filter[1])
        break;
      }
    },
    setFilter(filterPath, value){
      _.set(this.filters, filterPath, _.get(this.filters, filterPath).split(' ').splice(0, 1) + ' ' + value)
    }
  },
  mounted(){
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>