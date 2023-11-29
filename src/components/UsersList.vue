<script setup lang="ts">
import { ref,reactive,computed } from 'vue'
import AddModal from './AddModal.vue'
import DeleteConfirmationDialog from './DeleteConfirmationDialog.vue'

// 👉 Headers users Listing
const headers = [
  { name: 'Name' },
  { name: 'Email' },
  { name: 'Mobile'},
  { name: 'Date Of Birth'},
  { name: 'Actions'},
]

const modalActive = ref(false)
const getIndex = ref(0)
const isDeleteDialogVisible = ref(false)

// reactive data for add data in table
let usersData:any = reactive({
  data: []
})
const searchData = ref('')

// form reactive Data
const usersInfo = reactive({
name:'',
email: '',
mobile: '',
dob: ''
})

// Check if the email already exists in form
const isUniqueEmail = computed(() => {
  if (!usersInfo.email) {
    return true;
  }
  const existingEmail = usersData.data.some((user) => user.email === usersInfo.email);

  return !existingEmail;
});

//  👉 popup modal
const toggleModal = () => {
modalActive.value = !modalActive.value;
};

// reset function
const reset = (value: object, resetData: object) => {
  return Object.assign(value, resetData)
}

const resetUsersData = reactive({ ...usersInfo })


//  👉 save User
const onSave = () => {
    usersData.data.push({...usersInfo})
    toggleModal()
    reset(usersInfo, resetUsersData)  // reset Form data after add
}

//  👉  Search User
const searchFunction = computed(() => {
  const searchText = searchData.value.toLowerCase().trim();

  if (searchText.length >= 3) {
    const filteredData = usersData.data.filter((user:any) => {
      const nameMatch = user.name.toLowerCase().includes(searchText);
      const emailMatch = user.email.toLowerCase().includes(searchText);
      const mobileMatch = user.mobile.toLowerCase().includes(searchText);

      return nameMatch || emailMatch || mobileMatch;
    });

    return filteredData;
  }
  return usersData.value; // Return original data if search text length is less than 3
});

const deleteDialog = (index: number) => { 
  getIndex.value =  index
 isDeleteDialogVisible.value = !isDeleteDialogVisible.value;
};

//  👉  Delete User
const deleteUser = () => {
    usersData.data.splice(getIndex.value);
    isDeleteDialogVisible.value = !isDeleteDialogVisible.value;
}

// Debounce the search function to trigger after a certain delay
const onSearchInputChange = () => {
      setTimeout(() => {
        searchFunction;
      }, 500); // delay time in milliseconds
    };
</script>

<template>
<div class="relative overflow-x-auto shadow-md sm:rounded-lg">
    <div class="flex items-center justify-between flex-column md:flex-row flex-wrap space-y-4 md:space-y-0 py-4 bg-white dark:bg-gray-900">
        <!-- 👉  search Users -->
        <div class="relative mx-4">
            <div class="absolute inset-y-0 rtl:inset-r-0 start-0 flex items-center ps-3 pointer-events-none">
                <svg class="w-4 h-4 text-gray-500 dark:text-gray-400" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 20 20">
                    <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m19 19-4-4m0-7A7 7 0 1 1 1 8a7 7 0 0 1 14 0Z"/>
                </svg>
            </div>
            <input  v-model="searchData" type="text" id="table-search-users" class="block p-2 ps-10 text-sm text-gray-900 border border-gray-300 rounded-lg 
            w-80 bg-gray-50 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400
             dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500" placeholder="Search by name, email, or mobile" @input="onSearchInputChange">
        </div>
        <div>
          <!-- 👉   Add New Users -->
          <button type="button" class="text-white bg-blue-500 hover:bg-blue-700 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2 
          me-2 mb-1 dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none dark:focus:ring-blue-700" @click="toggleModal">
            Add New Users   
          </button>
 
        </div>
    </div>

    <!-- 👉 Listing Table -->
    <table class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
      <!-- 👉 Heading of table -->
        <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
            <tr>
                <th  v-for="(column, index) in headers"
                :id="column.name"
                :key="index" scope="col" class="px-6 py-3">
                 {{ column.name }}
                </th>
            </tr>
        </thead>
        <tbody>
     <!-- 👉 search data table -->
      <template v-if="searchFunction">
            <tr v-for="(user, index) in  searchFunction"  :key="index" class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600">
                <td class=" items-center px-6 py-4 text-gray-800 whitespace-nowrap dark:text-white">
                        <div class="text-base font-semibold">{{ user.name }}</div>  
                </td>

                <td  class=" items-center px-6 py-4 text-gray-800 whitespace-nowrap dark:text-white">
                        <div class="text-base font-semibold">{{ user.email }}</div>    
                </td>

                <td  class=" items-center px-6 py-4 text-gray-800 whitespace-nowrap dark:text-white">
                        <div class="text-base font-semibold">{{ user.mobile }}</div> 
                </td>

                <td  class=" items-center px-6 py-4 text-gray-800 whitespace-nowrap dark:text-white">
                        <div class="text-base font-semibold">{{ user.dob }}</div>
                </td>
                <td class="px-6 py-4">
                    <button type="button" class="text-white bg-blue-500 hover:bg-blue-700  font-medium 
                     text-sm px-5 me-2  dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none">
                    Edit 
                   </button>
                   <button type="button" class="text-white bg-blue-500 hover:bg-blue-700  font-medium 
                    text-sm px-5 me-2 mb-1 dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none" @click="deleteDialog(index)">
                    Delete 
                   </button>
                  </td>
            </tr>
          </template>

    <!-- 👉 List Data table -->
          <template v-else>
          <tr v-for="(list, index) in usersData.data"  :key="index" class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600">
                <td class=" items-center px-6 py-4 text-gray-800 whitespace-nowrap dark:text-white">
                        <div class="text-base font-semibold">{{ list.name }}</div>  
                </td>

                <td  class=" items-center px-6 py-4 text-gray-800 whitespace-nowrap dark:text-white">
                        <div class="text-base font-semibold">{{ list.email }}</div>    
                </td>

                <td  class=" items-center px-6 py-4 text-gray-800 whitespace-nowrap dark:text-white">
                        <div class="text-base font-semibold">{{ list.mobile }}</div> 
                </td>

                <td  class=" items-center px-6 py-4 text-gray-800 whitespace-nowrap dark:text-white">
                        <div class="text-base font-semibold">{{ list.dob }}</div>
                </td>
                <td class="px-6 py-4">
                    <button type="button" class="text-white bg-blue-500 hover:bg-blue-700  font-medium 
                     text-sm px-5 me-2  dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none">
                    Edit 
                   </button>
                   <button type="button" class="text-white bg-blue-500 hover:bg-blue-700  font-medium 
                    text-sm px-5 me-2 mb-1 dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none" @click="deleteDialog(index)">
                    Delete 
                   </button>
                  </td>
            </tr>
          </template>
        
        </tbody>
    </table>
</div>

<!-- 👉 Add user Popup Modal -->
<AddModal v-if="modalActive" :modalActive="modalActive">  
  <!-- 👉 Form start -->
      <form @submit.prevent="onSave">
        <h3 class="text-3xl font-bold">ADD</h3>
        <div class="mt-5">
            <div>
            <label class="flex justify-start">Name</label>
            <input v-model="usersInfo.name"
              class="shadow appearance-none border rounded  w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              type="text">
              <p v-if="!usersInfo.name.length" class="mt-2 text-sm text-red-600 dark:text-red-500 flex justify-start">name is required!</p>
          </div>
          <div>
            <label class="flex justify-start mt-3">Email</label>
            <input v-model="usersInfo.email"
              class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              type="text">
              <p v-if="!usersInfo.email.length" class="mt-2 text-sm text-red-600 dark:text-red-500 flex justify-start">email is required!</p>
              <p v-if="!isUniqueEmail" class="mt-2 text-sm text-red-600 dark:text-red-500 flex justify-start">email should be unique!</p>
          </div>
          <div>
            <label class="flex justify-start mt-3">Mobile</label>
            <input v-model="usersInfo.mobile"
              class="shadow appearance-none border rounded w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              type="text" >
              <p v-if="!usersInfo.mobile.length" class="mt-2 text-sm text-red-600 dark:text-red-500 flex justify-start">mobile is required!</p>
          </div>
          <div>
            <label class="flex justify-start mt-3">Date of Birth</label>
            <input v-model="usersInfo.dob"
              class="shadow appearance-none border rounded  w-full py-2 px-3 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
              type="text">
              <p v-if="!usersInfo.dob.length" class="mt-2 text-sm text-red-600 dark:text-red-500 flex justify-start">dob is required!</p>
          </div>
          <button
            class="bg-gray-400 mt-5 hover:bg-gray-600 text-white font-bold py-2 px-4 mx-5 rounded focus:outline-none focus:shadow-outline"
            type="button"  @click="toggleModal">
            Close
          </button>
          <button
            class="bg-blue-500 mt-5 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded focus:outline-none focus:shadow-outline"
            type="submit">
            Save
          </button>
        </div>
      </form>
    <!-- Form end -->
</AddModal>

<!-- 👉 Delete user Popup Modal -->
<DeleteConfirmationDialog  v-if="isDeleteDialogVisible" :isDeleteDialogVisible="isDeleteDialogVisible">

 <!-- 👉 Delete Modal content -->
<div class="relative p-4 text-center bg-red-200 rounded-lg shadow dark:bg-gray-800 sm:p-5">
        <svg class="text-gray-400 dark:text-gray-500 w-11 h-11 mb-3.5 mx-auto" aria-hidden="true" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" d="M9 2a1 1 0 00-.894.553L7.382 4H4a1 1 0 000 2v10a2 2 0 002 2h8a2 2 0 002-2V6a1 1 0 100-2h-3.382l-.724-1.447A1 1 0 0011 2H9zM7 8a1 1 0 012 0v6a1 1 0 11-2 0V8zm5-1a1 1 0 00-1 1v6a1 1 0 102 0V8a1 1 0 00-1-1z" clip-rule="evenodd"></path></svg>
        <p class="mb-4 text-gray-500 dark:text-gray-300">Are you sure you want to delete this item?</p>
<div class="flex justify-center items-center space-x-4">
    <button data-modal-toggle="deleteModal" type="button" class="py-2 px-3 text-sm font-medium text-gray-500 bg-white rounded-lg border border-gray-200
     hover:bg-gray-100 focus:ring-4 focus:outline-none focus:ring-primary-300 hover:text-gray-900 focus:z-10 dark:bg-gray-700 dark:text-gray-300
      dark:border-gray-500 dark:hover:text-white dark:hover:bg-gray-600 dark:focus:ring-gray-600" @click="deleteDialog(0)">
        No, cancel
    </button>
    <button type="submit" class="py-2 px-3 text-sm font-medium text-center text-white bg-red-600 rounded-lg hover:bg-red-700 focus:ring-4 focus:outline-none
     focus:ring-red-300 dark:bg-red-500 dark:hover:bg-red-600 dark:focus:ring-red-900" @click="deleteUser">
        Yes, I'm sure
    </button>
</div>
</div>
</DeleteConfirmationDialog>
</template>