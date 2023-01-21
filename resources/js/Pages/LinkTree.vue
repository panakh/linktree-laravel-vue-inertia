<script setup>
import { Head, Link } from '@inertiajs/vue3';
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
defineProps({
    canLogin: Boolean,
    canRegister: Boolean,
    laravelVersion: String,
    phpVersion: String,
});
</script>

<template>
    <div class="container">
        <h1>@technicallyhash</h1>
        <a target="_blank" v-for="link in links" :key="link.id" v-bind:class="{'tile content-between': $page.props.auth.user,'tile content-center': !$page.props.auth.user }" :href="link.url">
            <div class="icon social-link" v-html="getSocialSvg(link.url)">
            </div>
            <p class="mr-2">{{ link.title }}</p>
            <svg v-if="$page.props.auth.user" @click.prevent="deleteLink(link.id)" class="tile-share-button" fill="none" stroke="currentColor"
                stroke-width="1.5" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
                <path stroke-linecap="round" stroke-linejoin="round" d="M18 12H6"></path>
            </svg>
        </a>

    </div>

        <div v-if="$page.props.auth.user">
            <div class="py-12 ">
                <div class="max-w-sm rounded-[25px] overflow-hidden shadow-lg border">
                    <div class="coverflow-hidden">
                        <div class="p-6 ">
                            <form @submit.prevent="submitForm" class="mb-4">
                                <label for="title" class="block text-sm font-medium mb-2">Title</label>
                                <input type="text" v-model="newLink.title" id="title"
                                    class="text-black border-2 border-gray-300 rounded-lg p-2 w-full" />
                                <label for="url" class="block text-sm font-medium mb-2">URL</label>
                                <input type="text" v-model="newLink.url" id="url"
                                    class="text-black border-2 border-gray-300 rounded-lg p-2 w-full" />
                                <button type="submit" class="bg-blue-500 text-white p-2 rounded-lg mt-2">Add
                                    Link</button>
                            </form>
                        </div>
                    </div>
                </div>
            </div>

        </div>



</template>
<script>
import axios from 'axios';

export default {
    data() {
        return {
            links: [],
            newLink: {
                title: '',
                url: '',
            },
        };
    },
    created() {
        axios.get('/api/links')
            .then(response => {
                this.links = response.data;
            })
            .catch(error => {
                console.log(error);
            });
    },
    methods: {
        submitForm() {
            axios.post('/api/links', this.newLink)
                .then(response => {
                    this.links.push(response.data);
                    this.newLink = { title: '', url: '' };
                })
                .catch(error => {
                    console.log(error);
                });
        },
        deleteLink(id) {
            axios.delete(`/api/links/${id}`)
                .then(() => {
                    this.links = this.links.filter(link => link.id !== id);
                })
                .catch(error => {
                    console.log(error);
                });
        },
        getSocialSvg(url) {
            if (url.indexOf('facebook') !== -1) {
                return '  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 320 512" class="w-20 h-20" style="color: #1877f2;"><!--! Font Awesome Pro 6.0.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. --><path fill="currentColor" d="M279.14 288l14.22-92.66h-88.91v-60.13c0-25.35 12.42-50.06 52.24-50.06h40.42V6.26S260.43 0 225.36 0c-73.22 0-121.08 44.38-121.08 124.72v70.62H22.89V288h81.39v224h100.17V288z"/></svg>';
            } else if (url.indexOf('twitter') !== -1) {
                return '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="w-7 h-7" style="color: #1da1f2;"><!--! Font Awesome Pro 6.0.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. --><path fill="currentColor" d="M459.37 151.716c.325 4.548.325 9.097.325 13.645 0 138.72-105.583 298.558-298.558 298.558-59.452 0-114.68-17.219-161.137-47.106 8.447.974 16.568 1.299 25.34 1.299 49.055 0 94.213-16.568 130.274-44.832-46.132-.975-84.792-31.188-98.112-72.772 6.498.974 12.995 1.624 19.818 1.624 9.421 0 18.843-1.3 27.614-3.573-48.081-9.747-84.143-51.98-84.143-102.985v-1.299c13.969 7.797 30.214 12.67 47.431 13.319-28.264-18.843-46.781-51.005-46.781-87.391 0-19.492 5.197-37.36 14.294-52.954 51.655 63.675 129.3 105.258 216.365 109.807-1.624-7.797-2.599-15.918-2.599-24.04 0-57.828 46.782-104.934 104.934-104.934 30.213 0 57.502 12.67 76.67 33.137 23.715-4.548 46.456-13.32 66.599-25.34-7.798 24.366-24.366 44.833-46.132 57.827 21.117-2.273 41.584-8.122 60.426-16.243-14.292 20.791-32.161 39.308-52.628 54.253z"/></svg>';
            } else if (url.indexOf('instagram') !== -1) {
                return '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512" class="w-20 h-20" style="color: #c13584;"><!--! Font Awesome Pro 6.0.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. --><path fill="currentColor" d="M224.1 141c-63.6 0-114.9 51.3-114.9 114.9s51.3 114.9 114.9 114.9S339 319.5 339 255.9 287.7 141 224.1 141zm0 189.6c-41.1 0-74.7-33.5-74.7-74.7s33.5-74.7 74.7-74.7 74.7 33.5 74.7 74.7-33.6 74.7-74.7 74.7zm146.4-194.3c0 14.9-12 26.8-26.8 26.8-14.9 0-26.8-12-26.8-26.8s12-26.8 26.8-26.8 26.8 12 26.8 26.8zm76.1 27.2c-1.7-35.9-9.9-67.7-36.2-93.9-26.2-26.2-58-34.4-93.9-36.2-37-2.1-147.9-2.1-184.9 0-35.8 1.7-67.6 9.9-93.9 36.1s-34.4 58-36.2 93.9c-2.1 37-2.1 147.9 0 184.9 1.7 35.9 9.9 67.7 36.2 93.9s58 34.4 93.9 36.2c37 2.1 147.9 2.1 184.9 0 35.9-1.7 67.7-9.9 93.9-36.2 26.2-26.2 34.4-58 36.2-93.9 2.1-37 2.1-147.8 0-184.8zM398.8 388c-7.8 19.6-22.9 34.7-42.6 42.6-29.5 11.7-99.5 9-132.1 9s-102.7 2.6-132.1-9c-19.6-7.8-34.7-22.9-42.6-42.6-11.7-29.5-9-99.5-9-132.1s-2.6-102.7 9-132.1c7.8-19.6 22.9-34.7 42.6-42.6 29.5-11.7 99.5-9 132.1-9s102.7-2.6 132.1 9c19.6 7.8 34.7 22.9 42.6 42.6 11.7 29.5 9 99.5 9 132.1s2.7 102.7-9 132.1z"/></svg>';
            } else if (url.indexOf('linkedin') !== -1) {
                return '<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 448 512" class="w-20 h-20" style="color: #0077b5;"><!--! Font Awesome Pro 6.0.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. --><path fill="currentColor" d="M100.28 448H7.4V148.9h92.88zM53.79 108.1C24.09 108.1 0 83.5 0 53.8a53.79 53.79 0 0 1 107.58 0c0 29.7-24.1 54.3-53.79 54.3zM447.9 448h-92.68V302.4c0-34.7-.7-79.2-48.29-79.2-48.29 0-55.69 37.7-55.69 76.7V448h-92.78V148.9h89.08v40.8h1.3c12.4-23.5 42.69-48.3 87.88-48.3 94 0 111.28 61.9 111.28 142.3V448z"/></svg>'

            } else if (url.indexOf('youtube') !== -1) {
                return '  <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 576 512" class="w-20 h-20" style="color: #ff0000;"><!--! Font Awesome Pro 6.0.0 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) Copyright 2022 Fonticons, Inc. --><path fill="currentColor" d="M549.655 124.083c-6.281-23.65-24.787-42.276-48.284-48.597C458.781 64 288 64 288 64S117.22 64 74.629 75.486c-23.497 6.322-42.003 24.947-48.284 48.597-11.412 42.867-11.412 132.305-11.412 132.305s0 89.438 11.412 132.305c6.281 23.65 24.787 41.5 48.284 47.821C117.22 448 288 448 288 448s170.78 0 213.371-11.486c23.497-6.321 42.003-24.171 48.284-47.821 11.412-42.867 11.412-132.305 11.412-132.305s0-89.438-11.412-132.305zm-317.51 213.508V175.185l142.739 81.205-142.739 81.201z"/></svg>';
            }
            else {
                return '<svg class="w-20 h-20" style="color: #fff;" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 512 512" class="w-7 h-7" style="color: #fff;"><!-- Font Awesome Pro 5.15.4 by @fontawesome - https://fontawesome.com License - https://fontawesome.com/license (Commercial License) --><path fill="currentColor" d="M326.612 185.391c59.747 59.809 58.927 155.698.36 214.59-.11.12-.24.25-.36.37l-67.2 67.2c-59.27 59.27-155.699 59.262-214.96 0-59.27-59.26-59.27-155.7 0-214.96l37.106-37.106c9.84-9.84 26.786-3.3 27.294 10.606.648 17.722 3.826 35.527 9.69 52.721 1.986 5.822.567 12.262-3.783 16.612l-13.087 13.087c-28.026 28.026-28.905 73.66-1.155 101.96 28.024 28.579 74.086 28.749 102.325.51l67.2-67.19c28.191-28.191 28.073-73.757 0-101.83-3.701-3.694-7.429-6.564-10.341-8.569a16.037 16.037 0 0 1-6.947-12.606c-.396-10.567 3.348-21.456 11.698-29.806l21.054-21.055c5.521-5.521 14.182-6.199 20.584-1.731a152.482 152.482 0 0 1 20.522 17.197zM467.547 44.449c-59.261-59.262-155.69-59.27-214.96 0l-67.2 67.2c-.12.12-.25.25-.36.37-58.566 58.892-59.387 154.781.36 214.59a152.454 152.454 0 0 0 20.521 17.196c6.402 4.468 15.064 3.789 20.584-1.731l21.054-21.055c8.35-8.35 12.094-19.239 11.698-29.806a16.037 16.037 0 0 0-6.947-12.606c-2.912-2.005-6.64-4.875-10.341-8.569-28.073-28.073-28.191-73.639 0-101.83l67.2-67.19c28.239-28.239 74.3-28.069 102.325.51 27.75 28.3 26.872 73.934-1.155 101.96l-13.087 13.087c-4.35 4.35-5.769 10.79-3.783 16.612 5.864 17.194 9.042 34.999 9.69 52.721.509 13.906 17.454 20.446 27.294 10.606l37.106-37.106c59.271-59.259 59.271-155.699.001-214.959z"/></svg>';
            }
        },
    },
};
</script>

<style>
body {
    margin: 0;
    padding: 0;
    background-color: rgb(10, 10, 10);
    color: rgb(240, 240, 240);
    display: flex;
    align-items: center;
    flex-direction: column;
    width: 100vw;
    font-family: Verdana, Tahoma, sans-serif;
}

header {
    width: 95%;
    max-width: 788px;
    display: flex;
    justify-content: flex-end;
    padding: 12px;
    margin-top: 15px;
}

.share-button {
    width: 40px;
    height: 40px;
    border-radius: 20px;
    background-color: rgb(240, 240, 240);
}

.share-button svg {
    margin-left: 12px;
    margin-top: 10px;
    color: rgb(0, 0, 0);
}

.container {
    width: 91%;
    max-width: 680px;
    margin: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

h1 {
    font-size: 20px;
    margin-bottom: 30px;
}

a {
    @apply border-2;
    display: flex;
    align-items: center;
    text-decoration: none;
    color: rgb(240, 240, 240);
}

.tile {
    width: 100%;
    background-color: rgb(37, 37, 37);
    margin: 7px;
    border-radius: 17px;
}

.tile:hover {
    transition: cubic-bezier(.07, 1.41, .82, 1.41) 0.2s;
    transform: scale(1.02);
}

.tile-share-button {
    margin-right: 8px;
    width: 40px;
    height: 40px;
    border-radius: 20px;
    background-color: rgb(236, 19, 19);
}

.tile-share-button svg {
    margin-left: 12px;
    margin-top: 10px;
}


.image-container {
    height: 96px;
    width: 96px;
    border-radius: 48px;
    overflow: hidden;
}

.image-container img {
    height: 100%;
}

.icon {
    margin: 4px 8px;
    width: 44px;
    height: 44px;
}

.social-link {
    display: inline-flex;
    align-items: center;
}

.social-link svg {
    width: 1em;
    height: 1em;
    margin-right: 0.5em;
}
</style>