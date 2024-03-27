<template>
    <div class="page row">
        <div class="col-md-10">
            <InputSearch v-model="searchText" />
        </div>
        <div class="mt-3 col-md-6">
            <h4>
                Danh bạ
                <i class="fas fa-address-book"></i>
            </h4>
            <ContactList v-if="fillteredContactsCount > 0" :contacts="fillteredContacts"
                v-model:activeIndex="activeIndex" />
            <p v-else> Không có liên hệ nào.</p>

            <div class="mt-3 row justify-content-around align-items-center">
                <button class="btn btn-sm btn-primary" @click="goToAddContact">
                    <i class="fas fa-plus"></i> Thêm mới
                </button>

                <button class="btn btn-sm btn-danger" @click="removeAllContacts">
                    <i class="fas fa-trash"></i> Xóa tất cả
                </button>
            </div>
        </div>
        <div class="mt-3 col-md-6">
            <div v-if="activeContact">
                <h4>
                    Liên hệ chi tiết
                    <i class="fas fa-address-card"></i>
                </h4>
                <ContactCard :contact="activeContact" />
            </div>
        </div>
    </div>
</template>

<script>
    import ContactCard from '@/components/ContactCard.vue';
    import InputSearch from '@/components/InputSearch.vue';
    import ContactList from '@/components/ContactList.vue';
    import contactService from '@/services/contact.service';

    export default {
        components: {
            ContactCard,
            InputSearch,
            ContactList,
        },
        data() {
            return {
                contacts: [],
                activeIndex: -1,
                searchText: ""
            }
        },
        watch: {
            // Giám sát các thay đổi của biến searchText
            // Bỏ chọn phần tử đang được chọn trong danh sách
            searchText() {
                this.activeIndex = -1
            },
        },
        computed: {
            // Chuyển các đối tượng contact thành chuỗi để tiện cho việc tìm kiếm
            contactStrings() {
                return  this.contacts.map((contact) => {
                    const {name, email, adress, phone} = contact
                    return [name, email, adress, phone].join("")
                })
            },
            // Trả về các contact có chứa thông tin cần tìm kiếm
            fillteredContacts() {
                if(!this.searchText) return this.contacts
                return this.contacts.filter((_contact, index) => 
                    this.contactStrings[index].includes(this.searchText)
                )
            },
            activeContact() {
                if( this.activeIndex < 0) return null;
                return this.fillteredContacts[this.activeIndex]
            },
            fillteredContactsCount() {
                return this.fillteredContacts.length
            }
        },
        methods: {
            async retrieveContacts() {
                try{
                    this.contacts = await contactService.getAll()
                } catch(error) {
                    console.log(error)
                }
            },

            refreshList() {
                this.retrieveContacts()
                this.activeIndex = -1
            },

            async removeAllContacts() {
                if( confirm("Bạn muốn xóa tất cả liên hệ")) {
                    try {
                        await contactService.deleteAll()
                        this.refreshList()
                    } catch(error) {
                        console.log(error)
                    }
                }
            },

            goToAddContact() {
                this.$router.push({name: "contact.add"})
            }
        },
        mounted() {
            this.refreshList()
        }
    }
</script>

<style scoped>
.page {
    text-align: left;
    max-width: 750px;
}
</style>