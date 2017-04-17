<script>
export default {
  name: 'list',
  props: {
    tag: String,
    list: Array,
    initialFilters: {
      type: Array,
      default: function(){
        return []
      }
    }
  },
  data () {
    return {
      items: this.list,
      filters: this.initialFilters
    }
  },
  render: function($createElement){
    return $createElement(this.tag, {}, this.$scopedSlots[this.$options._componentTag](this))
  },
  computed: {
    filtered: function(){
      return  this.items.filter(item => {
        let able = true
        for(let attr in this.filters){
          able = this.filter(item, able, this.filters)
        }
        return able
      })
    }
  },
  methods: {
    filter(object, able, filter){
      if(filter[0] instanceof Array){
        able = this.filter(object, able, filter[0])
      } 
      switch (filter[1]){
        case '&&':
          return able && this.filter(object, able, filter[2])
        break;
        case '||':
          return able || this.filter(object, able, filter[2])
        break;
        default:
          return this.filterItem(object, filter)
        break;
      }
    },
    filterItem(object, operation){
      switch (operation[1]){
        case '===':
          return _.get(object, operation[0]) === operation[2]
        break;
        case '!==':
          return _.get(object, operation[0]) !== operation[2]
        break;
        case '==':
          return _.get(object, operation[0]) == operation[2]
        break;
        case '!=':
          return _.get(object, operation[0]) != operation[2]
        break;
        case '>=':
          return _.get(object, operation[0]) >= operation[2]
        break;
        case '<=':
          return _.get(object, operation[0]) >= operation[2]
        break;
        case '<':
          return _.get(object, operation[0]) < operation[2]
        break;
        case '>':
          return _.get(object, operation[0]) > operation[2]
        break;
        case 'includes':
          return _.get(object, operation[0]).includes(operation[2])
        break;
      }
    },
    enableFilter(filter){
      if(!_.get(this.filters, '!'+filter))
        return
      this.filters[filter] = Object.assign([], this.filters['!' + filter])
      this.items.push({})
      this.items.pop()
      delete this.filters['!' + filter]
    },
    disableFilter(filter){
      if(_.get(this.filters, filter))
        return
      this.filters['!' + filter] = Object.assign([], this.filters[filter])
      this.items.push({})
      this.items.pop()
      delete this.filters[filter]
    },
    filterBy(filters, only, $event){
      this.enableFilters(filters.map(filter => filter.filter), only)
      if($event){
        this.setFilter(filters, $event)
      }
    },
    enableFilters(filters, only){
      if(only){
        this.disableFilters([], true)
      }
      for(let index in filters){
        this.enableFilter(filters[index])
      }
    },
    disableFilters(filters, all){
      if(all){
        for(let filterName in this.filters){
          this.disableFilter(filterName)
        }
      }else{
        for(let index in filters){
          this.disableFilter(filters[index])
        }
      }
    },
    isEnabled(filter){
      return _.get(this.filters, filter) ? '!' : '';
    },
    setFilter(filters, $event){
      filters.forEach( item => {
        _.set(this.filters[item.filter], item.field, $event.target.value)
      })
    }
  },
  created(){
    //this.initializeFilters()
  }
}
</script>