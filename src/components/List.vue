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
      filters: this.initializeFilters(this.initialFilters)
    }
  },
  render: function($createElement){
    return $createElement(this.tag, {}, this.$scopedSlots[this.$options._componentTag](this))
  },
  computed: {
    filtered: function(){
      return  this.items.filter(item => {
        let able = true
        able = this.filter(item, able, this.filters)
        return able
      })
    }
  },
  methods: {
    filter(object, able, filter){
      if(!filter.disabled){
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
      } else{
        return true
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
    },
    initializeFilters(filter){
      if(filter instanceof Array){
        Object.defineProperty(filter, 'disabled', {
          writable: true,
          enumerable: true,
          configurable: true,
          value: false,
        })
        Object.defineProperty(filter, 'disable', {
          writable: true,
          enumerable: true,
          configurable: true,
          value: function(){ 
              filter.disabled = false 
            }
        })
        Object.defineProperty(filter, 'able', {
          writable: true,
          enumerable: true,
          configurable: true,
          value: function(){ 
              filter.disabled = true
            }
        })
        Object.defineProperty(filter, 'toggle', {
          writable: true,
          enumerable: true,
          configurable: true,
          value: function(){ 
              filter.disabled = !filter.disabled
              filter.push([])
              filter.pop()
            }
        })
      }
      if(filter[0] instanceof Array){
        this.initializeFilters(filter[0])
      }
      if(filter[2] instanceof Array){
        this.initializeFilters(filter[2])
      }
      return filter
    }
  },
  mounted(){
    
  }
}
</script>