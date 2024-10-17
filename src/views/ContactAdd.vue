<template>
  <div>
    <h4 class="text-center">Thêm mới một liên hệ</h4>
    <ContactForm :contact="contact" @submit:contact="createContact" />
  </div>
</template>

<script>
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

export default {
  components: {
    ContactForm,
  },
  data() {
    return {
      contact: {
        name: "",
        email: "",
        address: "",
        phone: "",
        favorite: false,
      },
      existingEmails: [],
      existingPhones: [],
    };
  },
  methods: {
    async getAllExitingEmails() {
      try {
        const contacts = await ContactService.getAll();
        contacts.forEach((each, index) => {
          this.existingEmails.push(each.email);
        });
      } catch (error) {
        console.log(error);
        this.$router.push({ name: "notfound" });
      }
    },
    async getAllExitingPhones() {
      try {
        const contacts = await ContactService.getAll();
        contacts.forEach((each, index) => {
          this.existingPhones.push(each.phone);
        });
      } catch (error) {
        console.log(error);
        this.$router.push({ name: "notfound" });
      }
    },
    async createContact() {
      try {
        if (this.existingEmails.includes(this.contact.email)) {
          alert("Email đã tồn tại");
        } else if (this.existingPhones.includes(this.contact.phone)) {
          alert("Số điện thoại đã tồn tại");
        } else {
          await ContactService.create(this.contact);
          alert("Bạn đã thêm một liên hệ thành công");
          this.$router.push({ name: "contactbook" });
        }
      } catch (error) {
        console.log("Error in creating new contact");
        alert("Error in creating new contact");
        this.$router.push({
          name: "notfound",
        });
      }
    },
  },
  created() {
    this.getAllExitingEmails();
    this.getAllExitingPhones();
  },
};
</script>
