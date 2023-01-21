<script setup>
import { ref, onBeforeMount, onBeforeUpdate, computed } from 'vue'
import axios from 'axios'

const quotes = ref([])

const props = defineProps({
    randomNumber: { type: Number, required: true },
})

onBeforeMount(() => {
    loadQuotes();
})

onBeforeUpdate(() => {
    // randomQuote.value = quote
}
)

function loadQuotes() {
    let url = 'assets/content/data/quotes.json';
    axios.get(url).then(resp => {
        quotes.value = resp.data
    }).catch(e => {
        console.log(e)
    });
}

const randomQuote = computed(() => {
    let { randomNumber } = props
    return quotes.value[Math.floor(Math.random() * quotes.value.length)]
}
)
</script>

<template>
    <div id="moveIn" class="container  mt-4 rounded">
        <blockquote class="blockquote text-center font-weight-bold font-italic">
            <i>{{ randomQuote }}</i>
        </blockquote>
    </div>
</template>
