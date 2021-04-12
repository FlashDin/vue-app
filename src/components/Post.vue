<template xmlns:v-slot="http://www.w3.org/1999/XSL/Transform">
    <table style="overflow-x: auto;">
        <thead>
        <tr>
            <th class="">
                <button @click="openModalAdd('')">Add
                </button>
            </th>
            <th></th>
        </tr>
        <tr>
            <th colspan="3">
                <input class="square-input-field"
                       name="keyword"
                       type="text"
                       placeholder="Pencarian..."/>
                <button>Cari
                </button>
            </th>
        </tr>
        <tr>
            <th>Title</th>
            <th>Body</th>
            <th>Action</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="post in posts" :key="post.id">
            <td>{{post.title}}</td>
            <td>{{post.body}}</td>
            <td>
                <button @click="openModalAdd(post)">Edit
                </button>
                <button @click="deleteData(post.id)">Delete
                </button>
            </td>
        </tr>
        </tbody>
    </table>
    <snackbar :message="snackbarMessage" :updateSnack="openSnackbar" @resetSnack="openSnackbar = false"/>
    <modal :handleClose="modalShow" :modalShow="modalShow = false"
           width="50%">
        <template v-slot:modalHeader>
            <div v-html="modalHeader"></div>
        </template>
        <template v-slot:modalContent>
            <component v-bind:is="modalContent" v-bind="{
            data: this.post,
            toastNotif: this.toastNotif,
            modalShow: this.handleBooleanState,
            getData: this.getData
            }"/>
        </template>
        <template v-slot:modalFooter>
            <div v-html="modalFooter"></div>
        </template>
    </modal>
</template>
<script>
    import Snackbar from "./snackbar/Snackbar.vue";
    import Modal from "./modal/Modal";
    import PostForm from "./form/PostForm";

    export default {
        name: 'Post',
        components: {
            Modal,
            Snackbar,
            PostForm
        },
        data() {
            return {
                posts: [],
                post: {},
                modalShow: false,
                modalHeader: null,
                modalContent: null,
                modalFooter: null,
                openSnackbar: false,
                snackbarMessage: '',
            }
        },
        mounted() {
            this.getData()
        },
        methods: {
            getData() {
                fetch('http://localhost:3002/posts', {
                    method: 'GET'
                }).then(res => res.json())
                    .then(json => this.posts = json);
            },
            deleteData(id) {
                fetch('http://localhost:3002/posts/' + id, {
                    method: 'DELETE'
                })
                    .then(res => {
                        if (res.ok) {
                            this.getData();
                        }
                        this.toastNotif("Delete " + res.statusText);
                    });
            },
            toastNotif(msg) {
                this.openSnackbar = true;
                this.snackbarMessage = msg;
            },
            handleBooleanState(name) {
                this.$data[name] = !this[name];
                console.log('HHEHE', this.modalShow);
            },
            openModalAdd(post) {
                const target = {
                    userId: 1,
                    id: '',
                    title: '',
                    body: '',
                };
                this.post = post === '' ? target : post;
                this.modalShow = true;
                this.modalHeader = 'Add Post';
                this.modalContent = 'PostForm';
                this.modalFooter = '* Silahkan isi semua field';
            }
        }
    }
</script>
