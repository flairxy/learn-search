<script setup>
import ApplicationLogo from "@/Components/ApplicationLogo.vue";
import { Link } from "@inertiajs/vue3";
</script>

<template>
    <div
        style="background-image: url('/bg5.jpg')"
        class="min-h-screen flex flex-col sm:justify-center items-center pt-6 sm:pt-0 bg-no-repeat bg-cover bg-blend-multiply bg-center bg-blue-950"
    >
        <div>
            <Link href="/">
                <div class="flex py-3">
                    <img class="w-14 h-14" src="/logoWhite.png" alt="">
                    <div class="text-xl font-bold text-white mt-4 uppercase">
                        Learn Search
                    </div>
                </div>
            </Link>
        </div>

        <div
            class="w-full sm:max-w-md mt-6 px-6 py-4 bg-white shadow-md overflow-hidden sm:rounded-lg"
        >
            <slot />
        </div>
    </div>
</template>
