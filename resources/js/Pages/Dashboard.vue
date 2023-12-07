<script setup>
import { ref, onMounted } from "vue";
import AuthenticatedLayout from "@/Layouts/AuthenticatedLayout.vue";
import { Head } from "@inertiajs/vue3";
import { careers } from "../careers";
import axios from "axios";
import { usePage } from "@inertiajs/vue3";
import { toast } from "vue3-toastify";
import "vue3-toastify/dist/index.css";

const user = usePage().props.auth.user;
const show = ref(false);
const processing = ref(false);
const submitting = ref(false);
const path = ref(user.career);
const resource = ref("");
const showReview = ref(false);
const rating = ref(0);
const review = ref("");

function setRating(index) {
    rating.value = index;
}

onMounted(() => {
    if (user.career) {
        fetchResources(user.career);
    }
});

async function submit() {
    try {
        submitting.value = true;
        const data = {
            rating: rating.value,
            review: review.value,
        };
        await axios.post(route("profile.review"), data);
        submitting.value = false;
        toast.success("Review submitted successfully");
    } catch (error) {
        submitting.value = false;
        console.log(error);
    }
}

async function fetchResources(value) {
    try {
        path.value = value;
        show.value = false;
        processing.value = true;

        await axios.post(
            route("profile.career.update", { career: path.value })
        );
        const endpoint = "https://api.learnsearch.dev/resources";
        const response = await axios.post(endpoint, { role: path.value });
        resource.value = response.data;

        processing.value = false;
        showReview.value = true;
    } catch (error) {
        processing.value = false;
        path.value = "";
        console.log(error);
    }
}
</script>

<template>
    <Head title="Dashboard" />

    <AuthenticatedLayout>
        <template #header>
            <h2 class="font-semibold text-xl text-gray-800 leading-tight">
                Dashboard
            </h2>
        </template>

        <div class="py-12 justify-center flex mx-auto">
            <div class="relative inline-block text-left">
                <div>
                    <button
                        @click.prevent="show = !show"
                        type="button"
                        class="inline-flex w-full justify-center gap-x-1.5 rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                        id="menu-button"
                        aria-expanded="true"
                        aria-haspopup="true"
                    >
                        {{ path ? path : "Choose your destiny" }}
                        <svg
                            class="-mr-1 h-5 w-5 text-gray-400"
                            viewBox="0 0 20 20"
                            fill="currentColor"
                            aria-hidden="true"
                        >
                            <path
                                fill-rule="evenodd"
                                d="M5.23 7.21a.75.75 0 011.06.02L10 11.168l3.71-3.938a.75.75 0 111.08 1.04l-4.25 4.5a.75.75 0 01-1.08 0l-4.25-4.5a.75.75 0 01.02-1.06z"
                                clip-rule="evenodd"
                            />
                        </svg>
                    </button>
                </div>

                <div
                    v-if="show"
                    class="absolute right-0 z-10 mt-2 w-56 origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none"
                    role="menu"
                    aria-orientation="vertical"
                    aria-labelledby="menu-button"
                    tabindex="-1"
                >
                    <div class="py-1" role="none">
                        <!-- Active: "bg-gray-100 text-gray-900", Not Active: "text-gray-700" -->
                        <span
                            v-for="(career, index) in careers"
                            :key="index"
                            @click.prevent="fetchResources(career.name)"
                            class="hover:bg-gray-100 cursor-pointer text-gray-700 block px-4 py-2 text-sm"
                        >
                            {{ career.name }}
                        </span>
                    </div>
                </div>
            </div>
        </div>
        <div
            class="w-screen px-8 bg-[#151519a6] md:w-[700px] rounded-lg py-12 flex justify-center mx-auto text-white"
        >
            <div class="flex flex-col">
                <div v-if="processing" class="flex flex-col">
                    <span class="text-white font-thin animate-pulse"
                        >Fetching Resources for {{ path }}
                    </span>
                    <div class="mx-auto loader"></div>
                </div>
                <div v-if="!processing && resource">
                    <h1 class="font-semibold text-lg">
                        Here's a list of resouce links to get you started on
                        your choosen career path
                    </h1>
                    <br />
                </div>
                <div v-if="!processing" v-html="resource"></div>
            </div>
        </div>

        <div
            class="w-screen bg-[#151519a6] md:w-[700px] rounded-lg mt-8 p-5 justify-center mx-auto"
            v-if="showReview"
        >
            <div class="flex flex-col">
                <h1 class="text-white font-thin">Rate and leave a review</h1>
                <div class="flex py-2">
                    <svg
                        v-for="index in 5"
                        :key="index"
                        @mouseover="setRating(index)"
                        xmlns="http://www.w3.org/2000/svg"
                        viewBox="0 0 24 24"
                        :fill="rating >= index ? 'orange' : '#fff'"
                        class="w-6 h-6 cursor-pointer"
                    >
                        <path
                            fill-rule="evenodd"
                            d="M10.788 3.21c.448-1.077 1.976-1.077 2.424 0l2.082 5.007 5.404.433c1.164.093 1.636 1.545.749 2.305l-4.117 3.527 1.257 5.273c.271 1.136-.964 2.033-1.96 1.425L12 18.354 7.373 21.18c-.996.608-2.231-.29-1.96-1.425l1.257-5.273-4.117-3.527c-.887-.76-.415-2.212.749-2.305l5.404-.433 2.082-5.006z"
                            clip-rule="evenodd"
                        />
                    </svg>
                </div>
                <textarea
                    v-model="review"
                    class="rounded"
                    name=""
                    id=""
                    cols="45"
                    rows="3"
                ></textarea>
                <button
                    @click.prevent="submit"
                    :disabled="submitting"
                    class="my-4 px-3 py-1.5 rounded bg-red-500 hover:bg-red-900 text-white w-32"
                >
                    {{ submitting ? "Submitting..." : "Submit" }}
                </button>
            </div>
        </div>
    </AuthenticatedLayout>
</template>
<style>
.loader {
    width: 50px;
    aspect-ratio: 1;
    display: grid;
    border: 4px solid #0000;
    border-radius: 50%;
    border-color: #ccc #0000;
    animation: l16 1s infinite linear;
}
.loader::before,
.loader::after {
    content: "";
    grid-area: 1/1;
    margin: 2px;
    border: inherit;
    border-radius: 50%;
}
.loader::before {
    border-color: #f03355 #0000;
    animation: inherit;
    animation-duration: 0.5s;
    animation-direction: reverse;
}
.loader::after {
    margin: 8px;
}
@keyframes l16 {
    100% {
        transform: rotate(1turn);
    }
}
</style>
