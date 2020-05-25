<template>
    <div id="app">
        <div class="content">
            <div class="select select__loc">
                <button class="select__loc-box" :class="{ 'select__loc-box--active': selectedLocationId === loc.id }" v-for="(loc, i) in locations" :key="i" @click="selectedLocationId = loc.id">
                    <span class="t-select select__loc-text">{{ loc.title }}</span>
                </button>
            </div>

            <div class="select select__date" :class="{ 'select__date--active': selectedLocationId !== null }">
                <!-- container not necessary -->
                <div class="select__date-container">
                    <div class="select__date-box t-select" :class="{ 'select__date-box--active': selectedMonth === month.name }" v-for="(month, i) in months" :key="i" @click="updateDateVars(month)">{{ month.name }}</div>
                </div>
            </div>

            <div class="select select__ret" :class="{ 'select__ret--active': selectedLocationId !== null && selectedMonth !== null }">
                <div class="select__ret-box t-select" :class="{ 'select__ret-box--active': flyingOut }" @click="updateFlyingVars(true)">Flying out</div>
                <div class="select__ret-box t-select" :class="{ 'select__ret-box--active': flyingOut === false }" @click="updateFlyingVars(false)">Returning home</div>
            </div>

            <div class="select select__search">
                <button class="select__search-btn t-search-btn" :class="{ 'select__search-btn--active': searchBtnReady }" @click="performSearch">Search</button>
            </div>

            <div class="flights-container" :class="{'flights-container--open': searchFirstClicked }">
                <div class="flights__header t-flights-header">
                    Lowest flight prices for your options
                </div>
                <div class="flights__list">
                    <div class="flights__loading t-flights-loading" v-if="loading">Loading flights...</div>
                    <div class="flights__flight" v-for="(flight, i) in loadedFlights" :key="i">
                        Price: €{{ flight.price }} Fly from: {{ flight.cityFrom }} ({{ flight.flyFrom }}) Fly to: {{ flight.cityTo }} ({{ flight.flyTo }}) Time: {{ flight.local_departure }} Flight duration: {{ flight.duration.departure }} Seats remaining: {{ flight.availability.seats }}
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

import axios from 'vue-cli-plugin-axios'

export default {
    name: 'App',
    components: {

    },

    data: () => {
        return {
            homeLocationId: 'london_gb',
            locations: [
                {
                    title: 'Amsterdam',
                    id: 'amsterdam_nl'
                },
                {
                    title: 'Madrid',
                    id: 'madrid_es'
                },
                {
                    title: 'Budapest',
                    id: 'budapest_hu'
                }
            ],
            months: [{
                name: 'January 2021',
                from: '01/01/2021',
                to: '31/01/2021'
            }, {
                name: 'February 2021',
                from: '01/02/2021',
                to: '28/02/2021'
            }, {
                name: 'March 2021',
                from: '01/03/2021',
                to: '31/03/2021'  
            }],
            selectedLocationId: null,
            selectedMonth: null,
            dateFrom: null,
            dateTo: null,
            flyingOut: null,
            flyingFrom: null,
            flyingTo: null,
            searchBtnReady: null,
            loading: false,
            searchFirstClicked: false,
            loadedFlights: null
        }
    },

    watch: {
        selectedLocationId: function () {
            this.resetSearch()
        },

        selectedMonth: function () {
            this.resetSearch();
        },

        flyingOut: function () {
            this.resetSearch();
        }
    },

    mounted () {
        this.ready()
    },

    methods: {
        ready () {
            console.log('Ready')
        },

        updateDateVars (month) {
            this.selectedMonth = month.name
            this.dateFrom = month.from
            this.dateTo = month.to
        },

        updateFlyingVars (flyingOut) {
            this.flyingOut = flyingOut

            if (flyingOut) {
                this.flyingFrom = this.homeLocationId
                this.flyingTo = this.selectedLocationId
            } else {
                this.flyingFrom = this.selectedLocationId
                this.flyingTo = this.homeLocationId
            }

            // If it's the first time all options have been filled for a search, enable search button
            if (this.searchBtnReady === null) this.searchBtnReady = true
        },

        resetSearch () {
            this.searchBtnReady = true;
            this.loadedFlights = null;
        },

        performSearch () {
            if (!this.searchFirstClicked) this.searchFirstClicked = true

            this.loading = true;
            this.searchBtnReady = false;

            let url = `https://tequila-api.kiwi.com/v2/search?adults=1&curr=EUR&date_from=${ encodeURIComponent(this.dateFrom) }&date_to=${ encodeURIComponent(this.dateTo) }&fly_from=${ this.flyingFrom }&fly_to=${ this.flyingTo }`;


            this.axios.get(url, { headers: { apikey: 'ZRptrANSRtLGpvM1lA2qU0xgZiNkhat0' } })
            .then(response => {
                if(response.data && response.data.data) {
                    this.loadedFlights = response.data.data.slice(0, 5);
                }
                this.loading = false;
            })
        }
    }
}
</script>

<style lang="stylus">
    @import "assets/stylus/app.styl"
</style>
