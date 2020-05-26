<template>
    <div id="app">
        <div class="bg bg__amsterdam" :class="{ 'bg--active': selectedLocationId === 'amsterdam_nl' }"></div>
        <div class="bg bg__madrid" :class="{ 'bg--active': selectedLocationId === 'madrid_es' }"></div>
        <div class="bg bg__budapest" :class="{ 'bg--active': selectedLocationId === 'budapest_hu' }"></div>
        <div class="content">
            <!-- The select containers could be separated into a reusable component possibly with slots but seems like overengineering for a simple UI -->
            <div class="intro t-intro">
                Looking to move house Jamie? This UI will help you check the kind of flight prices you can expect throughout the coming months whether your planning on returning home to visit or heading back out.
            </div>
            <div class="select select__loc">
                <button class="select__loc-box" :class="{ 'select__loc-box--active': selectedLocationId === loc.id }" v-for="(loc, i) in locations" :key="i" @click="selectedLocationId = loc.id">
                    <span class="t-select select__loc-text">{{ loc.title }}</span>
                </button>
            </div>

            <div class="select select__date" :class="{ 'select__date--active': selectedLocationId !== null }">
                <!-- Dates selection could be replace perhaps by a slider of date boxes rather than showing all at once -->
                <div class="select__date-container">
                    <button class="select__date-box t-select-date" :class="{ 'select__date-box--active': selectedMonth === month.name }" v-for="(month, i) in months" :key="i" @click="updateDateVars(month)">{{ month.name }}</button>
                </div>
            </div>

            <div class="select select__ret" :class="{ 'select__ret--active': selectedLocationId !== null && selectedMonth !== null }">
                <button class="select__ret-box t-select" :class="{ 'select__ret-box--active': flyingOut }" @click="flyingOut = true">Flying out</button>
                <button class="select__ret-box t-select" :class="{ 'select__ret-box--active': flyingOut === false }" @click="flyingOut = false">Returning home</button>
            </div>

            <div class="select select__search">
                <button class="select__search-btn t-search-btn" :class="{ 'select__search-btn--active': searchBtnReady }" @click="performSearch">Search</button>
            </div>

            <div class="flights-container" :class="{'flights-container--open': searchFirstClicked }">
                <div class="flights__header t-flights-header">
                    Lowest flight prices for your options
                </div>
                <div class="flights__list">
                    <div class="flights__loading t-flights-loading" ref="flightLoading">Loading flights...</div>
                    <!-- The flight block could be developed to display flights more beautifully and with more detail, icons etc. -->
                    <div class="flights__flight" v-for="(flight, i) in loadedFlights" :key="i" ref="flightItem">
                        <span class="" v-for="(data, j) in parseFlightData(flight)" :key="j" v-html="data"></span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>

import axios from 'vue-cli-plugin-axios';
import { gsap } from 'gsap';
import moment from 'moment';

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
            // The month data below could be generated using MomentJS, making the UI resuable as the current date changes, in this example I have hardcoded them
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
            }, {
                name: 'April 2021',
                from: '01/04/2021',
                to: '30/04/2021'
            }, {
                name: 'May 2021',
                from: '01/05/2021',
                to: '31/05/2021'
            }, {
                name: 'June 2020',
                from: '01/06/2020',
                to: '30/06/2020'
            }, {
                name: 'July 2020',
                from: '01/07/2020',
                to: '31/07/2020'
            }, {
                name: 'August 2020',
                from: '01/08/2020',
                to: '31/08/2020'
            }, {
                name: 'September 2020',
                from: '01/09/2020',
                to: '30/09/2020'
            }, {
                name: 'October 2020',
                from: '01/10/2020',
                to: '30/10/2020'
            }, {
                name: 'November 2020',
                from: '01/11/2020',
                to: '30/11/2020'
            }, {
                name: 'December 2020',
                from: '01/12/2020',
                to: '31/12/2020'
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
            loadedFlights: null,
            maxResults: 5,
            flightsTimeline: null
        }
    },

    watch: {
        selectedLocationId: function () {
            this.variablesUpdated();
        },

        selectedMonth: function () {
            this.variablesUpdated();
        },

        flyingOut: function () {
            this.variablesUpdated();
        }
    },

    methods: {
        updateDateVars (month) {
            this.selectedMonth = month.name;
            this.dateFrom = month.from;
            this.dateTo = month.to;
        },

        updateFlyingVars () {
            if (this.flyingOut === null) return;

            if (this.flyingOut) {
                this.flyingFrom = this.homeLocationId;
                this.flyingTo = this.selectedLocationId;
            } else {
                this.flyingFrom = this.selectedLocationId;
                this.flyingTo = this.homeLocationId;
            }

            if (this.searchBtnReady === null) this.searchBtnReady = true;
        },

        variablesUpdated () {
            if (this.searchBtnReady !== null) this.searchBtnReady = true;
            this.updateFlyingVars();
        },

        performSearch () {
            if (!this.searchFirstClicked) this.searchFirstClicked = true;

            this.loading = true;
            this.searchBtnReady = false;

            const hideTimeline = gsap.timeline()
            .staggerTo(this.$refs.flightItem, 0.2, {
                opacity: 0,
                y: 10
            }, 0.08)
            .to(this.$refs.flightLoading, 0.2, {
                opacity: 1
            });

            const url = `https://tequila-api.kiwi.com/v2/search?adults=1&curr=EUR&date_from=${encodeURIComponent(this.dateFrom)}&date_to=${encodeURIComponent(this.dateTo)}&fly_from=${this.flyingFrom}&fly_to=${this.flyingTo}`;

            this.axios.get(url, { headers: { apikey: 'ZRptrANSRtLGpvM1lA2qU0xgZiNkhat0' } })
            .then(response => {
                if (response.data && response.data.data) {
                    this.handleSearchResponse(response.data.data);
                }
                this.loading = false;
            })
        },

        handleSearchResponse (data) {
            this.loadedFlights = data.slice(0, this.maxResults);

            this.$nextTick(() => {
                const showTimeline = gsap.timeline()
                .set(this.$refs.flightItem, {
                    opacity: 0,
                    y: 10
                })
                .to(this.$refs.flightLoading, 0.2, {
                    opacity: 0
                })
                .staggerTo(this.$refs.flightItem, 0.2, {
                    opacity: 1,
                    y: 0
                }, 0.08);
            })
        },

        parseFlightData (flight) {
            return [`€${flight.price}`, `${flight.cityFrom} (${flight.flyFrom}) to ${flight.cityTo} (${flight.flyTo})`, moment(flight.local_departure, ['DD MM YYYY hh:mm', moment.ISO_8601])];
        }
    }
}
</script>

<style lang="stylus">
    @import "assets/stylus/app.styl"
</style>
