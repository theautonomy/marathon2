<script>
import  axios  from 'axios'

export default {
    data: () => ({
            marathons: [],
            currentMarathon: [],
            quotes: [],
            randomQuote: "",
            totalPages: 8,
            numberPerPage: 10,
            showResult: true
    }),

    created: function () {
        this.loadQuotes();
        let randomPage = Math.floor(Math.random() * this.totalPages + 1);
        let randomMarathon = (randomPage - 1) * this.numberPerPage + Math.floor(Math.random() * this.numberPerPage + 1);
        this.loadMarathons(randomPage);
        this.loadMarathon(randomMarathon);
    },

    methods: {
        loadQuotes() {
            let url = '/assets/content/data/quotes.json';
            axios.get(url).then(resp => {
                this.quotes = resp.data
                this.randomQuote = this.quotes[Math.floor(Math.random() * this.quotes.length)];
            }).catch(e => {
                console.log(e)
            });
        },

        loadMarathons(page) {
            // let p = page > 0 ? page : 1; 
            let url = '/assets/content/data/marathons' + (page > 0 ? page : 1) + '.json';
            axios.get(url).then(response => {
                this.marathons = response.data
            });
        },

        loadAll() {
            let a = [];
            let self = this;
            for (let i = 1; i < 11; i++) {
                let url = '/assets/content/data/marathons' + i + '.json';
                axios.get(url).then( response => {
                        if (i == 1) {
                            a = response.data;
                            a.hasPrevious = true;
                            a.hasNext = false;
                            self.loadMarathon(1);
                        } else {
                            a.data = a.data.concat(response.data.data);
                        }
                    this.marathons = a;
                });
            }
        },

        loadMarathon(id) {
            this.showResult = false;
            let url = '/assets/content/data/marathon' + id.toString() + '.json';
            axios.get(url).then(response => {
                this.currentMarathon = response.data
            });
            this.randomQuote = this.quotes[Math.floor(Math.random() * this.quotes.length)];
            setTimeout(() => this.showResult = true, 500);
        },

        loadPage(page) {
            this.loadMarathons(page);
            this.loadMarathon((page - 1) * this.numberPerPage + 1);
        },
    }
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
                                    <a class="page-link" href="#" @click="loadPage(marathons.pageNumber - 1)">Previous</a>
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
                                    <span class="fa fa-star-o font-italic"><i style="font-size:18px;color:blue">{{marathon.id}}</i>
                                    </span>
                                    - <a href="#" @click="loadMarathon(marathon.id)" :class="{'bg-info' : marathon.id == currentMarathon.id}">{{ marathon.name }}</a>
                                </td>
                            </tr>
                        </table>
                    </div>
                </div>
                <div class="col-md-6" >
                    <transition name="result" mode="out-in">
                    <div v-if="showResult && currentMarathon.id > 0 " >
                    <h5>{{currentMarathon.name}}</h5>
                    <ul class="list-unstyled list-group w-75 mt-4">
                        <li list-group-item><i class="fa-solid fa-calendar-days"></i><span class="mx-4 mb-2 badge bg-secondary ">{{currentMarathon.date}}</span></li>
                        <li list-group-item><i class="fa-solid fa-location-pin"></i><span class="mx-4 mb-2 badge bg-secondary ">{{currentMarathon.location}}</span></li>
                        <li list-group-item><i class="fa-solid fa-person-running"></i><span class="mx-4 mb-2 badge bg-secondary ">{{currentMarathon.distance}}</span></li>
                        <li list-group-item><i class="fa-regular fa-clock"></i><span class="mx-4 mb-2 badge bg-secondary ">{{currentMarathon.time}}</span></li>
                        <li list-group-item><i class="fa-solid fa-bolt"></i><span class="mx-4 mb-2 badge bg-secondary ">{{currentMarathon.pace}}</span></li>
                    </ul>
                    <div class="mt-4">
                        <img class="rounded" v-bind:src="'/assets/content/img/' + currentMarathon.picture" v-bind:alt="currentMarathon.name" height="240">
                    </div>

                    </div>
                    </transition>
                </div>
            </div>
            <div class="container  mt-4 rounded">
                <blockquote class="blockquote text-center font-weight-bold font-italic">
                    <i style="font-size:20px;color:black">"{{randomQuote}}"</i>
                </blockquote>
            </div>
        </div>
    </div>

</template>

<style>
.result-enter-from {
  opacity: 0;
  transform: translateX(100px);
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
