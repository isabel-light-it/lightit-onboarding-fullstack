<template>
    <div class="container py-2">
        <div class="row justify-content-center">
            <div class="col-md-8">
                <div class="card">
                    <div class="card-header bg-indigo-400 text-white">
                        Flight panel
                    </div>

                    <div
                        class="card-body space-around row justify-content-center"
                    >
                        <label for="companiesSelect">Airline Company:</label>
                        <div>
                            <VueMultiselect
                                v-model="selectedCompany"
                                :options="companies"
                                track-by="id"
                                label="name"
                                value="id"
                            >
                            </VueMultiselect>
                        </div>
                        <hr class="my-3" />

                        <label for="originsSelect">Origin city:</label>
                        <div>
                            <VueMultiselect
                                v-model="selectedOrigin"
                                :options="availableOrigins"
                                track-by="id"
                                label="name"
                                value="id"
                            >
                            </VueMultiselect>
                        </div>
                        <hr class="my-3" />

                        <label for="destinationsSelect"
                            >Destination city:</label
                        >
                        <div>
                            <VueMultiselect
                                v-model="selectedDestination"
                                :options="availableDestinations"
                                track-by="id"
                                label="name"
                                value="id"
                            >
                            </VueMultiselect>
                        </div>
                        <hr class="my-3" />

                        <label for="departureCalendar">Departure:</label>
                        <input
                            type="datetime-local"
                            id="departureCalendar"
                            name="trip-start"
                            min="2022-03-01T00:00"
                            :max="maxDepartureDate"
                            @change="setDepature"
                            :value="selectedDeparture"
                        />
                        <hr class="my-3" />

                        <label for="arrivalCalendar">Arrival:</label>
                        <input
                            type="datetime-local"
                            id="arrivalCalendar"
                            name="trip-end"
                            :min="minArrivalDate"
                            max="2022-12-31T00:00"
                            @change="setArrival"
                            :value="selectedArrival"
                        />
                        <hr class="my-3" />
                        <button
                            class="btn btn-primary max-w-sm bg-indigo-500 hover:bg-indigo-600"
                            :disabled="buttonStatus"
                            @click="storeFlight"
                        >
                            Register new flight
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div
        class="bg-green-100 border border-green-400 text-green-700 px-4 py-3 rounded relative"
        role="alert"
        v-if="savingSuccessful"
    >
        <strong class="font-bold">Flight registered successfully!</strong>
        <span class="absolute top-0 bottom-0 right-0 px-4 py-3">
            <svg
                class="fill-current h-6 w-6 text-green-500"
                role="button"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                @click="closePopupSuccess"
            >
                <title>Close</title>
                <path
                    d="M14.348 14.849a1.2 1.2 0 0 1-1.697 0L10 11.819l-2.651 3.029a1.2 1.2 0 1 1-1.697-1.697l2.758-3.15-2.759-3.152a1.2 1.2 0 1 1 1.697-1.697L10 8.183l2.651-3.031a1.2 1.2 0 1 1 1.697 1.697l-2.758 3.152 2.758 3.15a1.2 1.2 0 0 1 0 1.698z"
                />
            </svg>
        </span>
    </div>
    <div
        class="bg-red-100 border border-red-400 text-red-700 px-4 py-3 rounded relative"
        role="alert"
        v-if="savingFailed"
    >
        <strong class="font-bold">Oops something went wrong!</strong>
        <span class="absolute top-0 bottom-0 right-0 px-4 py-3">
            <svg
                class="fill-current h-6 w-6 text-red-500"
                role="button"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
                @click="closePopupFailed"
            >
                <title>Close</title>
                <path
                    d="M14.348 14.849a1.2 1.2 0 0 1-1.697 0L10 11.819l-2.651 3.029a1.2 1.2 0 1 1-1.697-1.697l2.758-3.15-2.759-3.152a1.2 1.2 0 1 1 1.697-1.697L10 8.183l2.651-3.031a1.2 1.2 0 1 1 1.697 1.697l-2.758 3.152 2.758 3.15a1.2 1.2 0 0 1 0 1.698z"
                />
            </svg>
        </span>
    </div>
</template>

<script>
import axios from "axios";
import VueMultiselect from "vue-multiselect";

export default {
    props: ["companies", "cities"],

    mounted() {},

    data() {
        return {
            selectedCompany: null,
            selectedOrigin: null,
            selectedDestination: null,
            selectedDeparture: null,
            selectedArrival: null,
            savingSuccessful: false,
            savingFailed: false,
        };
    },

    methods: {
        setDepature(e) {
            this.selectedDeparture = e.target.value;
        },
        setArrival(e) {
            this.selectedArrival = e.target.value;
        },
        async storeFlight() {
            try {
                const response = await axios.post("/manage/flights/", {
                    origin_city_id: this.selectedOrigin.id,
                    destination_city_id: this.selectedDestination.id,
                    company_id: this.selectedCompany.id,
                    departure: this.selectedDeparture,
                    arrival: this.selectedArrival,
                });
                if (response.status >= 200 && response.status < 300) {
                    this.savingSuccessful = true;
                    this.clearInputs();
                }
            } catch (err) {
                this.savingFailed = true;
            }
        },
        closePopupSuccess() {
            this.savingSuccessful = false;
        },
        closePopupFailed() {
            this.savingFailed = false;
        },
        clearInputs() {
            this.selectedCompany = null;
            this.selectedOrigin = null;
            this.selectedDestination = null;
            this.selectedDeparture = null;
            this.selectedArrival = null;
        },
    },

    computed: {
        availableOrigins() {
            return this.selectedDestination
                ? this.cities.filter(
                      (city) => city.id != this.selectedDestination.id
                  )
                : this.cities;
        },
        availableDestinations() {
            return this.selectedOrigin
                ? this.cities.filter(
                      (city) => city.id != this.selectedOrigin.id
                  )
                : this.cities;
        },
        minArrivalDate() {
            return this.selectedDeparture
                ? this.selectedDeparture
                : "2022-03-01T00:00";
        },
        maxDepartureDate() {
            return this.selectedArrival
                ? this.selectedArrival
                : "2022-12-30T00:00";
        },
        buttonStatus() {
            return this.selectedCompany &&
                this.selectedOrigin &&
                this.selectedDestination &&
                this.selectedDeparture &&
                this.selectedArrival
                ? false
                : true;
        },
    },
    components: { VueMultiselect },
};
</script>
