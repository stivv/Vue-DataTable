<template>
<div>
  <shown-items :shown_items="shownItems" :perPage="per_page" @perPage="perPage" />
  <table :class="options.table_class">
    <thead>
      <tr>
        <th :class="options.th_class" v-for="tc in tableColumns" :key="tc" >
          <span>{{ tc }}</span>
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(td, index) in tableData" :key="td[index]">
        <td :class="options.tb_class" v-for="col in tableColumns" :key="col">{{ td[col] }}</td>
      </tr>
    </tbody>
  </table>
  <div>
    <span>{{shown_info}}</span>
  </div>
  <div v-if="total_pages > 1">
    <ul :class="options.pagination_wrapper">
      
      <pagination-btn btn_text="First" :page="1" :show_btn="show_first_btn" :options="options" @goToPage="goToPage" />
      <pagination-btn btn_text="Prev" :page="current_page - 1" :show_btn="show_prev_btn" :options="options" @goToPage="goToPage" />
      
      <li  v-for="page in pages" :key="page" :class="current_page === page ? options.pagination_active_element : options.pagination_element">
        <a href="#" @click.prevent="goToPage(page)">{{page}}</a>
      </li>

      <pagination-btn btn_text="Next" :page="current_page + 1" :show_btn="show_next_btn" :options="options" @goToPage="goToPage" />
      <pagination-btn btn_text="Last" :page="total_pages" :show_btn="show_next_btn" :options="options" @goToPage="goToPage" />
  
    </ul>
  </div>
</div>
</template>

<script>
import PaginationBtn from './components/PaginationBtn'
import ShownItems from './components/ShownItems'
import paginateMixin from './mixins/paginate'
export default {
  name: 'DataTable',
  props: {
    options: {
      type: Object,
      default: () => ({
        table_class: 'table w-full justify-center border-collapse border-2 border-gray-500',
        th_class: 'border border-gray-400 px-4 py-2 text-gray-800 text-left capitalize',
        tb_class: 'border border-gray-400 px-4 py-2',
        pagination_wrapper: 'relative z-0 inline-flex rounded-md shadow-sm -space-x-px',
        pagination_element: 'bg-blue-50 border-gray-300 text-gray-800 hover:bg-gray-50 relative inline-flex items-center px-4 py-2 border text-sm font-medium',
        pagination_active_element: 'z-10 bg-blue-500 border-blue-500 text-blue-100 relative inline-flex items-center px-4 py-2 border text-sm font-medium',
        pagination_disabled_element: 'disabled bg-blue-50 border-gray-300 text-gray-500 hover:bg-gray-50 relative inline-flex items-center px-4 py-2 border text-sm font-medium',
        max_pages: 2,
        shown_items: [ 5, 10, 25, 50, 75, 100]
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
      columns: ['id', 'name', 'desc'],
      table_data: [
        {id: 1, name: 'name1', desc: 'desc1'},
        {id: 2, name: 'name2', desc: 'desc2'},
        {id: 3, name: 'name3', desc: 'desc3'},
        {id: 1, name: 'name1', desc: 'desc1'},
        {id: 2, name: 'name2', desc: 'desc2'},
        {id: 3, name: 'name3', desc: 'desc3'},
        {id: 1, name: 'name1', desc: 'desc1'},
        {id: 2, name: 'name2', desc: 'desc2'},
        {id: 3, name: 'name3', desc: 'desc3'},
        {id: 1, name: 'name1', desc: 'desc1'},
        {id: 2, name: 'name2', desc: 'desc2'},
        {id: 3, name: 'name3', desc: 'desc3'},
      ],
      filtered_data: [],
      per_page: 5,
      current_page: 1,
      pages: [],
      shown_info: '',
      total_pages: 1
    }
  },
  computed: {
    tableColumns(){
      return this.columns
    },
    tableData(){
      return this.filtered_data
    },
    shownItems(){
      return this.options.shown_items
    },
    maxPages(){
      return this.options.max_pages
    },
    tpo(){
      return this.maxPages < this.total_pages
    },
    show_first_btn(){
      return this.tpo  && this.current_page > 1
    },
    show_prev_btn(){
      return this.tpo  && this.current_page !== 1
    },
    show_next_btn(){
      return this.tpo  && this.current_page >= 1 && this.current_page !== this.total_pages
    }
  },
  components: {
    ShownItems,
    PaginationBtn,
  },
  methods: {
    filteredData(){
      let paginated = this.paginate(this.table_data.length, this.current_page, this.per_page, this.maxPages)
      console.log(paginated);
      let { startIndex, endIndex, pages, totalPages } = paginated
      this.filtered_data = this.table_data.slice(startIndex, endIndex+1)
      this.pages = pages
      this.total_pages = totalPages
      this.shown_info = `Show ${startIndex+1} to ${endIndex+1} of ${this.table_data.length} entries.`
    },
    perPage(per_page){
      this.per_page = per_page
      this.filteredData()
    },
    goToPage(page){
      this.current_page = page
      this.filteredData()
    }
  },
  created() {
    this.filteredData()
  },
  mixins: [paginateMixin]
}
</script>

<style scoped>
@import "https://unpkg.com/tailwindcss@^1.0/dist/tailwind.min.css";
</style>