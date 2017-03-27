<script>
export default {
  name: 'list',
  props: {
    tag: String,
    list: Array,
    initialFilters: {
      type: Object,
      default: function(){
        return {}
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
          if(!attr.startsWith('!')){
            able = this.filter(item, able, this.filters[attr], attr, 0)
          }
        }
        return able
      })
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
        return this.filter(object, true, operation, attr, 0)
      }
      switch (operation.op){
        case '===':
          return _.get(object, attr) === operation.val
        break;
        case '!==':
          return _.get(object, attr) !== operation.val
        break;
        case '==':
          return _.get(object, attr) == operation.val
        break;
        case '!=':
          return _.get(object, attr) != operation.val
        break;
        case '>=':
          return _.get(object, attr) >= operation.val
        break;
        case '<=':
          return _.get(object, attr) >= operation.val
        break;
        case '<':
          return _.get(object, attr) < operation.val
        break;
        case '>':
          return _.get(object, attr) > operation.val
        break;
        case 'includes':
          return _.get(object, attr).includes(operation.val)
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