<script setup>
import ApplicationLogo from '@/Components/ApplicationLogo.vue';
import { Link } from '@inertiajs/vue3';
</script>

<template>
    <div class="min-h-screen flex flex-col sm:justify-center items-center pt-6 sm:pt-0 bg-black">
        <div>
            <Link href="/">
                <div class="flex py-3">
                    <div class="text-xl font-bold text-white mr-1 uppercase">Learn Search</div>
                    <ApplicationLogo class="w-10 h-10 -mt-2 fill-current text-gray-500" />
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
