<template>
  <div class="v-suggestions">
    <i class="fas fa-search" id="desktopSearch" @click="searchSwitch"></i>
    <input type="text" class="input" :class="[extendedOptions.inputClass, { expandSearch: expandSearch }]"
           v-bind="$attrs"
           v-on:keydown="onKeyDown"
           v-on:blur="hideItems"
           v-on:focus="showItems = true"
           v-model="query"
           :placeholder="extendedOptions.placeholder">
    <i class="fas fa-search" id="mobileSearch" @click="searchSwitch"></i>
    <div class="suggestions">
      <ul class="items" v-show="items.length > 0 && showItems === true">
        <li class="item"
            :key="index"
            v-for="(item, index) in items"
            @click.prevent="selectItem(index)"
            v-bind:class="{ 'is-active': index === activeItemIndex }">
          <slot name="item"
                :item="item">
            {{item}}
          </slot>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
  import debounce from 'debounce'

  export default {
    inheritAttributes: true,
    props: {
      options: {
        type: Object,
        default: {}
      },
      onInputChange: {
        type: Function,
        required: true
      },
      onItemSelected: {
        type: Function
      },
      value: {
        type: String,
        required: true
      }
    },
    data () {
      const defaultOptions = {
        debounce: 0,
        placeholder: 'Search Ticker or Keyword',
        inputClass: 'input'
      }
      const extendedOptions = Object.assign({}, defaultOptions, this.options)
      return {
        extendedOptions,
        query: this.value,
        lastSetQuery: null,
        items: [],
        activeItemIndex: -1,
        showItems: false,
        expandSearch: false,
        // hideIcons: false
      }
    },
    beforeMount () {
      if (this.extendedOptions.debounce !== 0) {
        this.onQueryChanged = debounce(this.onQueryChanged, this.extendedOptions.debounce)
      }
    },
    watch: {
      'query': function (newValue, oldValue) {
        if (newValue === this.lastSetQuery) {
          this.lastSetQuery = null
          return
        }
        this.onQueryChanged(newValue)
        this.$emit('input', newValue)
      },
      'value': function (newValue, oldValue) {
        this.setInputQuery(newValue)
      }
    },
    methods: {
      searchSwitch () {
        this.expandSearch = !this.expandSearch;
        // this.hideIcons= !this.hideIcons;
      },
      onItemSelectedDefault (item) {
        if (typeof item === 'string') {
          this.$emit('input', item)
          this.setInputQuery(item)
          this.showItems = false

        }
      },
      hideItems () {
        setTimeout(() => {
          this.showItems = false
        }, 150)
      },
      showResults () {
        this.showItems = true
      },
      setInputQuery (value) {
        this.lastSetQuery = value
        this.query = value
      },
      onKeyDown (e) {
        switch (e.keyCode) {
          case 40:
          console.log('down');
            this.highlightItem('down')
            e.preventDefault()
            break
          case 38:
            this.highlightItem('up')
            e.preventDefault()
            break
          case 13:
            this.selectItem()
            e.preventDefault()
            break
          default:
            return true
        }
      },
      selectItem (index) {
        let item = null
        if (typeof index === 'undefined') {
          if (this.activeItemIndex === -1) {
            return
          }
          item = this.items[this.activeItemIndex]
        } else {
          item = this.items[index]
        }
        if (!item) {
          return
        }
        if (this.onItemSelected) {
          this.onItemSelected(item)
        } else {
          this.onItemSelectedDefault(item)
        }
        this.hideItems()
      },
      highlightItem (direction) {
        if (this.items.length === 0) {
          return
        }
        let selectedIndex = this.items.findIndex((item, index) => {
          return index === this.activeItemIndex
        })
        if (selectedIndex === -1) {
          // nothing selected
          if (direction === 'down') {
            selectedIndex = 0
          } else {
            selectedIndex = this.items.length - 1
          }
        } else {
          this.activeIndexItem = 0
          if (direction === 'down') {
            selectedIndex++
            if (selectedIndex === this.items.length) {
              selectedIndex = 0
            }
          } else {
            selectedIndex--
            if (selectedIndex < 0) {
              selectedIndex = this.items.length - 1
            }
          }
        }
        this.activeItemIndex = selectedIndex
      },
      setItems (items) {
        this.items = items
        this.activeItemIndex = -1
        this.showItems = true
      },
      onQueryChanged (value) {
        const result = this.onInputChange(value)
        this.items = []
        if (typeof result === 'undefined' || typeof result === 'boolean' || result === null) {
          return
        }
        if (result instanceof Array) {
          this.setItems(result)
        } else if (typeof result.then === 'function') {
          result.then(items => {
            this.setItems(items)
          })
        }
      }
    }
  }
</script>

<style>
.v-suggestions {
  /* nested grid */
  display: grid;
  grid-template-columns: 1fr 2fr;
  max-width: 200px;
  justify-items: center;
  align-items: center;
}

.v-suggestions i {
  margin-left: 1em;
}

.v-suggestions .suggestions {
  position: absolute;
  top: 40px;
  height: auto;
  z-index: 100;
  background: #ffffff;
}

.v-suggestions .items {
  list-style: none;
  border: 1px solid #EEE;
  margin: 0;
  padding: 0;
  border-width: 0 1px 1px 1px;
}

.v-suggestions .item {
  border-bottom: 1px solid #eee;
  padding: .2rem;
  color: #000;
  font-size: 13px;
}

.item:hover {
  background-color: #FFCC33;
}

.input {
  background-color: transparent;
  /* border: 1px solid #fff;
  border-radius: 10px; */
  border: none;
  color: #fff;
  font-size: 13px;
  padding: 5px;
  left: 10px;

  /* padding-top: 10px;
  padding-bottom: 3px;
  margin-bottom: 10px; */
  width: 0;
  /* min-height: 22px; */
  /* display: none; */
  transition: all .5s cubic-bezier(0.000, 0.105, 0.035, 1.570);
}

.expandSearch {
  width: 155px !important;
  background-color: transparent;
  border-bottom: 1px solid #fff;
  /* border-radius: 10px; */
  margin: 0;
  z-index: 4;
}

.input:focus {
  outline: none;
}

#desktopSearch {
  display: none;
}

/* .hideIcons {
  display: none;
} */

@media (min-width: 1000px) {
   .input {
     width: 155px;
     background-color: transparent;
     border-bottom: 1px solid #fff;
     /* border-radius: 10px; */
   }

   #desktopSearch {
     display: inline;
     margin-right: 10px;
   }

   #mobileSearch {
     display: none;
   }
}

/* .v-suggestions {
  position: relative;
  -webkit-box-sizing: border-box;
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  display: none;
}

.v-suggestions .suggestions {
  position: absolute;
  left: 0;
  top: 40px;
  height: auto;
  z-index: 100;
  background: #ffffff;
}

.v-suggestions .items {
  list-style: none;
  border: 1px solid #EEE;
  margin: 0;
  padding: 0;
  border-width: 0 1px 1px 1px;
}

.v-suggestions .item {
  border-bottom: 1px solid #eee;
  padding: .2rem;
  color: #000;
  font-size: 13px;
}

.item:hover {
  background-color: #FFCC33;
}

.input {
  background-color: transparent;
  border: none;
  border-bottom: 1px solid #fff;
  color: #fff;
  font-size: 13px;
  padding-top: 10px;
  padding-bottom: 3px;
  margin-bottom: 10px;
  min-width: 160px;
  min-height: 22px;
}

.input:focus {
  outline: none;
}

.input::-webkit-input-placeholder {
  color: #e5e5e5;
}

.input:focus::-webkit-input-placeholder {
  color: #6d6d6d;
}

@media (min-width: 1000px) {
  .v-suggestions {
    display: inline;
  }
} */
</style>
