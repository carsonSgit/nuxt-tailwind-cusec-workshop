<template>
    <header class="bg-gradient-to-r from-blue-500 to-purple-600 py-6 flex justify-between items-center text-white px-4 text-2xl shadow-lg">
        <div class="tracking-wide font-bold text-center">
            Guess the Pokemon
        </div>
    </header>
    <div class="mx-auto max-w-2xl py-32">
        <div class="text-center">
            <h1 class="text-6xl font-extrabold tracking-tight text-gray-900">Pokemon Guessor!</h1>
            <p class="mt-6 text-lg font-medium text-gray-600 sm:text-xl">Show me your knowledge about Pokemons.</p>
            <button type="button" @click="createQuiz" class="mt-8 bg-gradient-to-r from-gray-800 to-gray-900 text-2xl border-2 rounded-md border-transparent px-8 py-4 text-white hover:bg-green-500 transition-all duration-300 ease-in-out transform hover:scale-105 capitalize shadow-lg">Play a Game</button>
            <div v-if="error_api" class="mt-10 text-red-500">
                {{ error_api }}
            </div>
        </div>
    </div>
</template>

<script setup lang="ts">
const error_api = ref<string | null>(null)

const createQuiz = async () => {
    try {
        const { data: quiz, error } = await $fetch('/api/quiz', {
            method: "POST"
        })
        if (!quiz || error) return error_api.value = error?.message ?? "error when creating the quiz"
        
        const router = useRouter()
        router.push(`/quiz/${quiz.id}`)

    } catch(e) {
        error_api.value = e as string
    }
}
</script>