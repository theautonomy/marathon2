<script setup>
import { ref, onBeforeMount } from 'vue'
import axios from 'axios'
import quote from './quote.vue'
import gsap from 'gsap';

const marathons = ref([])
const currentMarathon = ref([])
const totalPages = ref(8)
const numberPerPage = ref(10)
const showResult = ref(true)

onBeforeMount( () => {
    let randomPage = Math.floor(Math.random() * totalPages.value + 1);
    let randomMarathon = (randomPage - 1) * numberPerPage.value + Math.floor(Math.random() * numberPerPage.value + 1);
    loadMarathons(randomPage);
    loadMarathon(randomMarathon);
})

function loadMarathons(page) {
    let url = 'assets/content/data/marathons' + (page > 0 ? page : 1) + '.json';
    axios.get(url).then(response => {
        marathons.value = response.data
    });
}

function loadAll() {
    let a = [];
    for (let i = 1; i < 11; i++) {
        let url = 'assets/content/data/marathons' + i + '.json';
        axios.get(url).then(response => {
            if (i == 1) {
                a = response.data;
                a.hasPrevious = true;
                a.hasNext = false;
                loadMarathon(1);
            } else {
                a.data = a.data.concat(response.data.data);
            }
            marathons.value = a;
        });
    }
}

function loadMarathon(id) {
    showResult.value = false;
    let url = 'assets/content/data/marathon' + id.toString() + '.json';
    axios.get(url).then(response => {
        currentMarathon.value = response.data
    });
    setTimeout(() => showResult.value = true, 500);
}

function loadPage(page) {
    loadMarathons(page);
    loadMarathon((page - 1) * numberPerPage.value + 1);
}

function onBeforeEnter(el) {
  gsap.set(el, {
    scale: 0.5,
    autoAlpha: 0,
  })
};

function onEnter(el, done) {
  gsap.to(el, {
    delay: 0.05,
    duration: 0.75,
    scale: 1,
    autoAlpha: 1,
    ease: "power2.out",
    onComplete: done
  });
}
</script>


<template>
    <div class="container rounded bg-white mt-1 p-4" style="border:1px solid black">
        <div class="container rounded m p-4" style="border:1px solid green">
            <div class="row">
                <div class="col-md-6 ">
                    <div>
                        <nav aria-label="Page navigation" v-if="marathons.totalPages > 1">
                            <ul class="pagination">
                                <li class="page-item" :class="{ disabled: !marathons.hasPrevious }">
                                    <a class="page-link" href="#" @click="loadPage(1)">10</a>
                                </li>
                                <li class="page-item" :class="{ disabled: !marathons.hasPrevious }">
                                    <a class="page-link" href="#"
                                        @click="loadPage(marathons.pageNumber - 1)">Previous</a>
                                </li>
                                <li class="page-item" :class="{ disabled: !marathons.hasNext }">
                                    <a class="page-link" href="#" @click="loadPage(marathons.pageNumber + 1)">Next</a>
                                </li>
                                <li class="page-item" :class="{ disabled: !marathons.hasNext }">
                                    <a class="page-link" href="#" @click="loadPage(marathons.totalPages)">100</a>
                                </li>
                                <li class="page-item">
                                    <a class="page-link" href="#" @click="loadAll()">All</a>
                                </li>
                            </ul>
                        </nav>
                    </div>

                    <div>
                        <table class="table table-hover table-borderless">
                            <tr v-for="marathon in marathons.data">
                                <td>
                                    <span class="fa fa-star-o font-italic"><i
                                            style="font-size:18px;color:blue">{{ marathon.id }}</i>
                                    </span>
                                    - <a href="#" @click="loadMarathon(marathon.id)"
                                        :class="{ 'bg-info': marathon.id == currentMarathon.id }">{{ marathon.name }}</a>
                                </td>
                            </tr>
                        </table>
                    </div>
                </div>
                <div class="col-md-6">
                    <!-- <transition name="result" mode="out-in"> -->
                    <transition @before-enter="onBeforeEnter" @enter="onEnter" :css="false" appear>
                        <div v-if="showResult && currentMarathon.id > 0">
                            <h5>{{ currentMarathon.name }}</h5>
                            <ul class="list-unstyled list-group w-75 mt-4">
                                <li list-group-item><i class="fa-solid fa-calendar-days"></i><span
                                        class="mx-4 mb-2 badge bg-secondary ">{{ currentMarathon.date }}</span></li>
                                <li list-group-item><i class="fa-solid fa-location-pin"></i><span
                                        class="mx-4 mb-2 badge bg-secondary ">{{ currentMarathon.location }}</span></li>
                                <li list-group-item><i class="fa-solid fa-person-running"></i><span
                                        class="mx-4 mb-2 badge bg-secondary ">{{ currentMarathon.distance }}</span></li>
                                <li list-group-item><i class="fa-regular fa-clock"></i><span
                                        class="mx-4 mb-2 badge bg-secondary ">{{ currentMarathon.time }}</span></li>
                                <li list-group-item><i class="fa-solid fa-bolt"></i><span
                                        class="mx-4 mb-2 badge bg-secondary ">{{ currentMarathon.pace }}</span></li>
                            </ul>
                            <div class="mt-4">
                                <img class="rounded" v-bind:src="'assets/content/img/' + currentMarathon.picture"
                                    v-bind:alt="currentMarathon.name" height="200">
                            </div>

                        </div>
                    </transition>
                </div>
            </div>

            <quote :random-number="Math.random()"></quote>
        </div>
    </div>

</template>

<style>
.result-enter-from {
    opacity: 0;
    transform: translateX(300px);
}

.result-enter-active {
    transition: all 0.3s ease-out;
}

.result-leave-to {
    opacity: 0;
    transform: translateX(-100px);
}

.result-leave-active {
    transition: all 0.3s ease-in;
}
</style>
