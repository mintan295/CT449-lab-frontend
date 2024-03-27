<template>
    <div v-if="contact" class="page">
        <h4>Thêm liên lạc</h4>
        <ContactForm :contact="contact" @submit:contact="addContact"  />
        <p>{{ message }}</p>
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
            contact: {},
            message: "",
        };
    },
    methods: {
        async addContact(data) {
            try {
                ContactService.create(data)
                this.message = "Liên hệ đã được tạo thành công"
            } catch (error) {
                console.log(error)
            }
        }
    }
}
</script>