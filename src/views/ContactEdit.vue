<script>
import ContactForm from "../components/ContactForm.vue";
import contactService from "@/services/contact.service";

export default {
  components: {
    ContactForm,
  },
  props: {
    id: { type: String, required: true },
  },
  data() {
    return {
      contact: null,
      message: "",
    };
  },
  methods: {
    async getContact(id) {
      try {
        this.contact = await contactService.get(id);
      } catch (error) {
        console.log(error);
        // Chuyen sang trang Not Found dong thoi giu cho URL khong doi
        this.$router.push({
          name: "notFound",
          params: {
            pathMatch: this.$router.path.split("/").slide(1),
          },
          query: this.$router.query,
          hash: this.$router.hash,
        });
      }
    },

    async updateContact(data) {
      try {
        await contactService.update(this.contact._id, data);
        alert("Lien he duoc cap nhat thanh cong.");
        this.$router.push({ name: "contactbook" });
      } catch (error) {
        console.log(error);
      }
    },

    async deleteContact() {
      if (confirm("Ban muon xoa lien he nay?")) {
        try {
          await contactService.delete(this.contact._id);
          this.$router.push({ name: "contactbook" });
        } catch (error) {
          console.log(error);
          alert("Xoa lien he that bai.");
        }
      }
    },
  },
  created() {
    this.getContact(this.id);
    this.message = "";
  },
};
</script>

<template>
  <div v-if="contact" class="page">
    <h4>Hieu chinh lien he</h4>
    <ContactForm
      :contact="contact"
      @submit:contact="updateContact"
      @delete:contact="deleteContact"
    />
    <p>{{ message }}</p>
  </div>
</template>
