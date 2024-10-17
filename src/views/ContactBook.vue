<template>
  <div class="container-fluid">
    <div class="page row">
      <div class="col-md-12">
        <InputSearch v-model="searchText"></InputSearch>
        <hr />
      </div>

      <div class="mt-3 col-md-6">
        <h5 class="text-center">Danh bạ <i class="fas fa-address-book"></i></h5>
        <hr />
        <ContactList
          v-if="filteredContactCount > 0"
          v-model:activeIndex="activeIndex"
          :contacts="filteredContacts"
        ></ContactList>
        <p v-else>Không có liên hệ nào</p>
        <div class="mt-3 row justify-content-around align-items-center">
          <button class="btn btn-sm btn-primary" @click="refreshList()">
            <i class="fas fa-redo"></i> Làm mới
          </button>
          <button class="btn btn-sm btn-success" @click="goToAddContact">
            <i class="fas fa-plus"></i> Thêm mới
          </button>
          <button class="btn btn-sm btn-danger" @click="removeAllContacts">
            <i class="fas fa-trash"></i> Xóa tất cả
          </button>
        </div>
      </div>
      <div v-if="activeContact" class="col-md-6 mt-3">
        <h5 class="text-center">
          Chi tiết liên hệ <i class="fa-regular fa-id-card"></i>
        </h5>
        <hr />
        <ContactCard :contact="activeContact"> </ContactCard>
        <RouterLink
          :to="{
            name: 'contact.edit',
            params: { id: activeContact._id },
          }"
          ><span class="mt-2 badge badge-warning">
            <i class="fas fa-edit"></i> Hiệu chỉnh</span
          ></RouterLink
        >
      </div>
    </div>
  </div>
</template>

<script>
import ContactList from "@/components/ContactList.vue";
import InputSearch from "@/components/InputSearch.vue";
import ContactService from "@/services/contact.service";
import ContactCard from "@/components/ContactCard.vue";

export default {
  components: {
    InputSearch,
    ContactList,
    ContactCard,
  },
  data() {
    return {
      contacts: [],
      searchText: "",
      activeIndex: -1,
    };
  },
  watch: {
    // Khi biến searchText thay đổi thì chạy hàm này
    searchText() {
      this.activeIndex = -1;
    },
  },
  computed: {
    contactString() {
      return this.contacts.map((contact) => {
        const { name, email, address, phone } = contact;

        return [name, email, address, phone].join("");
      });
    },
    filteredContacts() {
      if (!this.searchText) return this.contacts;
      return this.contacts.filter((_contact, index) => {
        return this.contactString[index].includes(this.searchText);
      });
    },
    filteredContactCount() {
      return this.contacts.length;
    },
    activeContact() {
      if (this.activeIndex === -1) return null;
      return this.filteredContacts[this.activeIndex];
    },
  },
  methods: {
    async retrieveContacts() {
      try {
        this.contacts = await ContactService.getAll();
      } catch (error) {
        console.log(error);
      }
    },
    refreshList() {
      this.retrieveContacts();
      this.activeIndex = -1;
      this.searchText = "";
    },
    async removeAllContacts() {
      if (confirm("Bạn có muốn xóa tất cả liên hệ không")) {
        try {
          await ContactService.deleteAll();
          this.refreshList();
        } catch (error) {
          console.log(error);
        }
      }
    },
    goToAddContact() {
      this.$router.push({ name: "contact.add" });
    },
  },
  mounted() {
    this.refreshList();
  },
};
</script>
