<script>
import axios from 'axios'

export default {
  data() {
    return {
      host_name: '',
      data_type: 'domain',
      alert_msg: '',
      alert_show: false,
      alert_error:false,
      alert_success:false,
      show_domain_info_tbl:false,
      domain_info:[],
      show_contact_info_tbl:false,
      contact_info:[],
      is_loading:false,
    }
  },
  methods: {
    submit() {
      this.clearAlert();
      console.log(import.meta.env.VITE_SERVER_API_URL);
      if (this.host_name == '') {
        this.alert_msg = 'Domain is required!';
        this.alert_show = true;
        this.alert_error = true;
        return;
      }
      this.getDomainData();
    },
    clearAlert() {
      this.show_domain_info_tbl = false;
      this.domain_info = [];
      this.show_contact_info_tbl = false;
      this.contact_info = [];
      this.alert_msg = '';
      this.alert_show = false;
      this.alert_error = false;
      this.alert_success = false;
    },
    async getDomainData() {
      const _this = this;
      this.is_loading = true;
      this.posts = await axios.get(import.meta.env.VITE_SERVER_API_URL +'/check-site', {
        params: {
          host_name: this.host_name,
          data_type: this.data_type
        }
      }).then(function (response) {
        console.log(response);
        _this.is_loading = false;
        if (_this.data_type == 'domain') {
          _this.show_domain_info_tbl = true;
          _this.domain_info = response.data.data;
        } else {
          _this.show_contact_info_tbl = true;
          _this.contact_info = response.data.data;
        }
      }).catch(function (error) {
        _this.is_loading = false;
        _this.alert_msg = error.response.data.message;
        _this.alert_show = true;
        _this.alert_error = true;
      });
    }
  }
}
</script>

<template>
  <main >
    <div class="form-con">
      <div v-if="alert_show" class="p-4 mb-4 text-sm rounded-lg bg-red-50 dark:bg-gray-800 dark:text-red-400" :class="{'text-red-800' : alert_error, 'text-green-800' : alert_success}" role="alert">
        {{alert_msg}}
      </div>
      <form class="w-full max-w-sm">
        <div class="md:flex md:items-center mb-6">
          <div class="md:w-1/3">
            <label class="block text-gray-500 font-bold md:text-right mb-1 md:mb-0 pr-4" for="inline-full-name">
              Domain
            </label>
          </div>
          <div class="md:w-2/3">
            <input
              :disabled="is_loading" class="bg-gray-200 appearance-none border-2 border-gray-200 rounded w-full py-2 px-4 text-gray-700 leading-tight focus:outline-none focus:bg-white focus:border-purple-500"
              id="inline-full-name" type="text" value="" v-model="host_name">
          </div>
        </div>
        <div class="md:flex md:items-center mb-6">
          <div class="md:w-1/3">
            <label class="block text-gray-500 font-bold md:text-right mb-1 md:mb-0 pr-4" for="inline-full-name">
              Type
            </label>
          </div>
          <div class="md:w-2/3">
            <select
              :disabled="is_loading" class="block appearance-none w-full bg-gray-200 border border-gray-200 text-gray-700 py-3 px-4 pr-8 rounded leading-tight focus:outline-none focus:bg-white focus:border-gray-500"
              id="grid-state" v-model="data_type" >
              <option selected value="domain" >Domain Information</option>
              <option value="contact" >Contact Information</option>
            </select>
          </div>
        </div>
        <div class="md:flex md:items-center">
          <div class="md:w-1/3"></div>
          <div class="">
            <button :disabled="is_loading" class="focus:outline-none text-white bg-green-700 hover:bg-green-800 focus:ring-4 focus:ring-green-300 font-medium rounded-lg text-sm px-5 py-2.5 me-2 mb-2 dark:bg-green-600 dark:hover:bg-green-700 dark:focus:ring-green-800"
              type="button" @click="submit" >
              Go
            </button>
            <span class="float-right" v-if="is_loading" ><svg aria-hidden="true" class="w-8 h-8 text-gray-200 animate-spin dark:text-gray-600 fill-blue-600" viewBox="0 0 100 101" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M100 50.5908C100 78.2051 77.6142 100.591 50 100.591C22.3858 100.591 0 78.2051 0 50.5908C0 22.9766 22.3858 0.59082 50 0.59082C77.6142 0.59082 100 22.9766 100 50.5908ZM9.08144 50.5908C9.08144 73.1895 27.4013 91.5094 50 91.5094C72.5987 91.5094 90.9186 73.1895 90.9186 50.5908C90.9186 27.9921 72.5987 9.67226 50 9.67226C27.4013 9.67226 9.08144 27.9921 9.08144 50.5908Z" fill="currentColor"/>
                <path d="M93.9676 39.0409C96.393 38.4038 97.8624 35.9116 97.0079 33.5539C95.2932 28.8227 92.871 24.3692 89.8167 20.348C85.8452 15.1192 80.8826 10.7238 75.2124 7.41289C69.5422 4.10194 63.2754 1.94025 56.7698 1.05124C51.7666 0.367541 46.6976 0.446843 41.7345 1.27873C39.2613 1.69328 37.813 4.19778 38.4501 6.62326C39.0873 9.04874 41.5694 10.4717 44.0505 10.1071C47.8511 9.54855 51.7191 9.52689 55.5402 10.0491C60.8642 10.7766 65.9928 12.5457 70.6331 15.2552C75.2735 17.9648 79.3347 21.5619 82.5849 25.841C84.9175 28.9121 86.7997 32.2913 88.1811 35.8758C89.083 38.2158 91.5421 39.6781 93.9676 39.0409Z" fill="currentFill"/>
            </svg></span>
          </div>
        </div>
      </form>
    </div>
    <!-- domain information table -->
    <div v-if="show_domain_info_tbl" class="w-3/4 relative overflow-x-auto">
      <h6 class="text-lg font-bold dark:text-white">Domain Information</h6>
      <table class="w-3/4 text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
        <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
          <tr>
            <th scope="col" class="px-6 py-3">
              Domain Name
            </th>
            <th scope="col" class="px-6 py-3">
              Registrar
            </th>
            <th scope="col" class="px-6 py-3">
              Registration Date
            </th>
            <th scope="col" class="px-6 py-3">
              Expiration Date
            </th>
            <th scope="col" class="px-6 py-3">
              Estimated Domain Age
            </th>
            <th scope="col" class="px-6 py-3">
              Hostnames
            </th>
          </tr>
        </thead>
        <tbody>
          <tr class="bg-white border-b dark:bg-gray-800 dark:border-gray-700">
            <td v-for="info in domain_info" :key="info.domainName" class="px-6 py-4">
              {{ info }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <!-- contact information table -->
    <div v-if="show_contact_info_tbl" class="w-3/4 relative overflow-x-auto">
      <h6 class="text-lg font-bold dark:text-white">Contact Information</h6>
      <table class="w-3/4 text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
        <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
          <tr>
            <th scope="col" class="px-6 py-3">
              Registrant Name
            </th>
            <th scope="col" class="px-6 py-3">
              Technical Contact Name
            </th>
            <th scope="col" class="px-6 py-3">
              Administrative Contact Name
            </th>
            <th scope="col" class="px-6 py-3">
              Contact Email
            </th>
          </tr>
        </thead>
        <tbody>
          <tr class="bg-white border-b dark:bg-gray-800 dark:border-gray-700">
            <td v-for="info in contact_info" :key="info.registrant_name" class="px-6 py-4">
              {{ info }}
            </td>
          </tr>
        </tbody>
      </table>
    </div>

  </main>
</template>