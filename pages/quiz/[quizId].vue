<template>
    <header class="bg-gradient-to-r from-blue-500 to-gray-600 py-6 flex justify-between items-center text-white px-4 text-2xl shadow-lg">
        <div class="tracking-wide font-bold text-center">
            Guess the Pokemon
        </div>
        <div class="flex gap-4">
            <div class="text-red-400"> 
                Fail: {{ fail_number }}
            </div>
            <div class="text-green-400"> 
                Success: {{ success_number }}
            </div>
        </div>
    </header>
    <main class="px-4 grid grid-cols-1 md:grid-cols-2 gap-8 mt-4">
        <template v-if="data?.error">
            <div class="col-span-2 text-center text-red-500 text-xl">
                ERROR: {{ data?.error }}
            </div>
        </template>
        <template v-else>
            <div class="grid grid-cols-1 gap-4">
                <h1 class="text-4xl font-semibold leading-loose text-center">
                    {{ data?.data?.question }}
                </h1>
                <div class="grid grid-cols-1 gap-4">
                    <button
                        :disabled="!!pokemon_name_selected"
                        :class="pokemon_name_selected === pokemon_name ? '!bg-slate-300 !text-black' : ''"
                        type="button"
                        class="w-full bg-white text-2xl leading-relaxed border-2 rounded-lg border-slate-400 py-4 hover:bg-slate-300 hover:text-black first-letter:capitalize"
                        v-for="(pokemon_name, index) in data?.data?.possible_answers" :key="index"
                        @click="checkAnswer(pokemon_name)"
                    >
                        {{ pokemon_name }}
                    </button>
                </div>
                <button v-if="answer_status === 'error' || answer_status === 'success'" @click="resetAndRetrieveNewQuestion" class="mt-4 text-2xl bg-black text-white leading-relaxed border-2 rounded-lg border-slate-400 py-4 hover:bg-slate-300 hover:text-black first-letter:capitalize">Next Question</button>
            </div>
            <div class="bg-slate-200 relative flex justify-center items-center">
                <template v-if="answer_status !== 'error' && answer_status !== 'success'">
                    <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[200px] h-[200px]" :class="answer_status === 'pending' ? 'animate-pulse' : ''">
                        <div class="bg-rose-500 w-[200px] pt-[100px] rounded-t-full"></div>
                        <div class="relative">
                            <div class="absolute w-[200px] h-2 bg-slate-700"></div>
                            <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[40px] h-[40px] border-8 bg-white border-slate-700 rounded-full"></div>
                        </div>
                        <div class="bg-white w-[200px] pb-[100px] rounded-b-full"></div>
                    </div>
                </template>
                <template v-else>
                    <div class="flex flex-col items-center gap-4 mt-8">
                        <h2 class="text-4xl text-black font-semibold">{{ data?.data?.answer_data.name }}</h2>
                        <NuxtImg :src="data?.data?.answer_data.image" class="w-48 h-48 object-cover rounded-full shadow-lg"/>
                        <div class="py-10 text-center  w-full" :class="answer_status === 'success' ? 'text-green-400' : 'text-red-400'">
                            {{ answer_status === 'success' ? 'Success' : 'Wrong' }}
                        </div>
                    </div>
                </template>
            </div>
        </template>
    </main>
</template>

<script lang="ts" setup>
const route = useRoute()
const fail_number = ref(0)
const success_number = ref(0)
const { data, refresh } = await useLazyFetch(`/api/quiz/${route.params.quizId}`)

const pokemon_name_selected = ref<string | null>(null)
const answer_status = ref<"none" | "pending" | "error" | "success">("none")

const fakeTimer = () => new Promise((resolve) => setTimeout(() => resolve(""), 2000))

const checkAnswer = async (pokemon_name: string) => {
    if (pokemon_name_selected.value) return
    const question = data.value?.data

    pokemon_name_selected.value = pokemon_name
    answer_status.value = "pending"
    await fakeTimer()

    if (question?.answer_data.name === pokemon_name) {
        answer_status.value = "success"
        success_number.value = success_number.value + 1
    } else {
        answer_status.value = "error"
        fail_number.value = fail_number.value + 1
    }
}

const resetAndRetrieveNewQuestion = async () => {
    answer_status.value = 'none'
    pokemon_name_selected.value = null
    await refresh()
}
</script>