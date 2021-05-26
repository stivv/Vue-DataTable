<template>
<div>
  <shown-items :shown_items="dt__shownItems" :perPage="dt__per_page" @perPage="dt__perPage" />
  <search-items :options="options" @keyUp="dt__searchInput($event)" />
  <template v-if="dt__tableData.length">
    <table :class="options.dt__table_class">
      <thead>
        <tr>
          <th :class="options.dt__th_class" v-for="tc in dt__tableColumns" :key="tc" >
            <span>{{ tc }}</span>
            <a href="#" @click.prevent="dt__sortByColumn(tc)"> || sort {{dt_sorted_cols[tc]}}</a>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td :class="options.dt__tb_class" v-for="col in dt__tableColumns" :key="col">
            <input type="text" v-model="dt__filter_by_col[col]" :class="options.dt__filter_by_col_class" @keyup="dt__filterByCol(col)" :placeholder="`Filter by ${col}`" />
          </td>
        </tr>
        <tr v-for="(td, index) in dt__tableData" :key="td[index]">
          <td :class="options.dt__tb_class" v-for="col in dt__tableColumns" :key="col">{{ td[col] }}</td>
        </tr>
      </tbody>
    </table>
    <div>
      <span>{{dt__shown_info}}</span>
    </div>
    <div v-if="dt__total_pages > 1">
      <ul :class="options.dt__pagination_wrapper">
        
        <pagination-btn btn_text="First" :page="1" :show_btn="dt__show_first_btn" :options="options" @goToPage="dt__goToPage" />
        <pagination-btn btn_text="Prev" :page="dt__current_page - 1" :show_btn="dt__show_prev_btn" :options="options" @goToPage="dt__goToPage" />
        
        <li  v-for="page in dt__pages" :key="page" :class="dt__current_page === page ? options.dt__pagination_active_element : options.dt__pagination_element">
          <a href="#" @click.prevent="dt__goToPage(page)">{{page}}</a>
        </li>

        <pagination-btn btn_text="Next" :page="dt__current_page + 1" :show_btn="dt__show_next_btn" :options="options" @goToPage="dt__goToPage" />
        <pagination-btn btn_text="Last" :page="dt__total_pages" :show_btn="dt__show_next_btn" :options="options" @goToPage="dt__goToPage" />
    
      </ul>
    </div>
  </template>
  <div v-else :class="options.dt__no_items_class">
    {{options.dt_table_empty_info}}
  </div>
</div>
</template>

<script>
import PaginationBtn from './components/PaginationBtn'
import ShownItems from './components/ShownItems'
import SearchItems from './components/SearchItems'
import paginateMixin from './mixins/paginate'
export default {
  name: 'DataTable',
  props: {
    options: {
      type: Object,
      default: () => ({
        dt__table_class: 'table w-full justify-center border-collapse border border-gray-400',
        dt__th_class: 'border border-gray-400 px-4 py-2 text-gray-800 text-left capitalize',
        dt__tb_class: 'border border-gray-400 px-4 py-2',
        dt__pagination_wrapper: 'relative z-0 inline-flex rounded-md shadow-sm -space-x-px',
        dt__pagination_element: 'bg-blue-50 border-gray-300 text-gray-800 hover:bg-gray-50 relative inline-flex items-center px-4 py-2 border text-sm font-medium',
        dt__pagination_active_element: 'z-10 bg-blue-500 border-blue-500 text-blue-100 relative inline-flex items-center px-4 py-2 border text-sm font-medium',
        dt__pagination_disabled_element: 'disabled bg-blue-50 border-gray-300 text-gray-500 hover:bg-gray-50 relative inline-flex items-center px-4 py-2 border text-sm font-medium',
        dt__max_pages: 2,
        dt__shown_items: [ 5, 10, 25, 50, 75, 100],
        dt__search_input: 'border-2 border-gray-300 my-1',
        dt__filter_by_col_class: 'border-2 border-gray-300 my-1',
        dt__no_items_class: '',
        dt_table_empty_info: 'No items found.'
      })
    },    
    // columns: {
    //   type: Array,
    //   required: true
    // },
    // table_data: {
    //   type: Array,
    //   required: true
    // }
  },
  data(){
    return {
      dt__columns: ['id', 'name', 'desc'],
      dt__table_data: [
        {id: 2, name: 'name1', desc: 'desc1'},
        {id: 2, name: 'name2', desc: 'desc2'},
        {id: 3, name: 'name3', desc: 'desc3'},
        {id: 4, name: 'name1', desc: 'desc1'},
        {id: 5, name: 'name1', desc: 'desc1'},
        {id: 6, name: 'name1', desc: 'desc1'},
        {id: 7, name: 'name2', desc: 'desc2'},
        {id: 8, name: 'name3', desc: 'desc3'},
        {id: 9, name: 'name1', desc: 'desc1'},
        {id: 10, name: 'name2', desc: 'desc2'},
        {id: 11, name: 'name3', desc: 'desc2'},
        {id: 12, name: 'name1', desc: 'desc1'},
        {id: 13, name: 'name2', desc: 'desc2'},
        {id: 14, name: 'name3', desc: 'desc3'},
      ],
      dt__filtered_data: [],
      dt__per_page: 5,
      dt__current_page: 1,
      dt__pages: [],
      dt__shown_info: '',
      dt__total_pages: 1,
      dt__search_input: '',
      dt__searched_data: [],
      dt_sorted_cols: {},
      dt__filter_by_col: {},
      dt__search_by_col_data: []
    }
  },
  computed: {
    dt__tableColumns(){
      return this.dt__columns
    },
    dt__tableData(){
      return this.dt__filtered_data
    },
    dt__shownItems(){
      return this.options.dt__shown_items
    },
    dt__maxPages(){
      return this.options.dt__max_pages
    },
    dt__tpo(){
      return this.dt__maxPages < this.dt__total_pages
    },
    dt__show_first_btn(){
      return this.dt__tpo  && this.dt__current_page > 1
    },
    dt__show_prev_btn(){
      return this.dt__tpo  && this.dt__current_page !== 1
    },
    dt__show_next_btn(){
      return this.dt__tpo  && this.dt__current_page >= 1 && this.dt__current_page !== this.dt__total_pages
    },
  },
  components: {
    ShownItems,
    PaginationBtn,
    SearchItems
  },
  methods: {
    dt__filteredData(){
      let data = (this.dt__searched_data.length || this.dt__search_input.length ? this.dt__searched_data : this.dt__table_data)
      let paginated = this.dt__paginate(data.length, this.dt__current_page, this.dt__per_page, this.dt__maxPages)
      // console.log(paginated);
      let { startIndex, endIndex, pages, totalPages } = paginated
      this.dt__filtered_data = data.slice(startIndex, endIndex+1)
      this.dt__pages = pages
      this.dt__total_pages = totalPages
      this.dt__shown_info = `Show ${startIndex < 0 ? 0 : startIndex+1} to ${endIndex+1} of ${data.length} results.`
    },
    dt__perPage(per_page){
      this.dt__per_page = per_page
      this.dt__filteredData()
    },
    dt__goToPage(page){
      this.dt__current_page = page
      this.dt__filteredData()
    },
    dt__searchInput(data, col = false){
      this.dt__search_input = data
      this.dt__current_page = 1
      this.dt__searched_data = this.dt__table_data.map(i => {
        if(col){
          if(i[col].toString().toLowerCase().includes(data.toLowerCase()))
            return i
        }else{
          for(let s in i){
            if(i[s].toString().toLowerCase().includes(data.toLowerCase()))
              return i
          }
        }
      }).filter(n => n !== undefined)
      this.dt__filteredData()
    },
    dt__sortByColumn(col){
      
      let data = (this.dt__searched_data.length || this.dt__search_input.length ? this.dt__searched_data : this.dt__table_data)
      
      this.dt_sorted_cols[col] = this.dt_sorted_cols[col] === undefined ? 'asc' : (this.dt_sorted_cols[col] === 'asc' ? 'desc' :'asc')
      
      for (const key of Object.keys(this.dt_sorted_cols)) {
        if(key !== col) this.dt_sorted_cols[key] = undefined
      }

      this.dt__searched_data = data.sort((a, b) => {
        if(typeof a[col] === 'number'){
          if(this.dt_sorted_cols[col] === 'asc'){
            return a[col] - b[col];
          }else{
            this.dt_sorted_cols[col] = 'desc'
            return b[col] - a[col];
          }
        }else{
          let nameA = a[col].toString().toUpperCase(); // ignore upper and lowercase
          let nameB = b[col].toString().toUpperCase(); // ignore upper and lowercase
          if(this.dt_sorted_cols[col] === 'asc'){
            return (nameA > nameB) ? 1 : ((nameA < nameB) ? -1 : 0)
          }else{
            return (nameA < nameB) ? 1 : ((nameA > nameB) ? -1 : 0)
          }
        }
      })
      this.dt__filteredData()
    },
    dt__filterByCol(col){
      this.dt__searchInput(this.dt__filter_by_col[col], col)
    }
  },
  created() {
    this.dt__filteredData()
  },
  mixins: [paginateMixin]
}
</script>

<style scoped>
@import "https://unpkg.com/tailwindcss@^1.0/dist/tailwind.min.css";
</style>