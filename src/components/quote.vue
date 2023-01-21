<script setup>
import { ref, onBeforeMount, onBeforeUpdate, computed } from 'vue'
import axios from 'axios'

import q from "../../assets/content/data/quotes.json"

const props = defineProps({
    randomNumber: { type: Number, required: true },
})

onBeforeMount(() => {
    // console.log(q)
    // console.log(props.randomNumber);
})

onBeforeUpdate(() => {
}
)

const randomQuote = computed(() => {
    let { randomNumber } = props
    // console.log(props.randomNumber);
    // let n = Math.random();
    return q[Math.floor(randomNumber * q.length)]
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
