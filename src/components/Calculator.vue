<template>
    <form @submit.prevent="validateForm" class="insurance-form">
        <!-- Typ poistenia -->
        <div class="first-row">
            <div class="form-row mb-4" v-for="(insurance, index) in values" :key="index">
                <div class="radio-wrapper">
                    <input
                        class="form-check-input"
                        type="radio"
                        name="insurance"
                        :id="insurance.code"
                        :value="index"
                        v-model="insurancePicked"
                    />
                    <label class="form-check-label" :for="insurance.code">{{ insurance.name }}</label>
                </div>
            </div>
        </div>
        <!-- Krátkodobé poistenie dátum -->
        <div class="form-row mb-4">
            <label for="startDate">Začiatok poistenia</label>
            <input id="startDate" class="form-control" type="date" v-model="insuranceStartDate" />
            <Transition name="slide-up">
                <div
                    class="warning"
                    v-if="attemptSubmit && missingStartDate"
                >Výber začiatku poistenia je povinný!</div>
            </Transition>
        </div>
        <!-- Celoročné poistenie dátum-->
        <Transition name="slide-up">
            <div class="form-row mb-4" v-if="values[insurancePicked].code == 'short'">
                <label for="endDate">Koniec poistenia</label>
                <input
                    id="endDate"
                    class="form-control"
                    type="date"
                    v-model="insuranceEndDate"
                    @change="compareDates"
                />
                <Transition name="slide-up">
                    <div
                        class="warning"
                        v-if="attemptSubmit && compareDates"
                    >Konečný dátum nemôže byť menší ako začiatočný!</div>
                </Transition>
            </div>
        </Transition>
        <!-- Balíky -->
        <div class="form-row mb-4">
            <label for="package" class="form-label">Balík</label>
            <select
                class="form-select"
                name="package"
                aria-label="Vyberte jeden z balíkov"
                v-model="insurancePackage"
            >
                <option
                    v-for="(pack, index) in values[insurancePicked].packages"
                    :key="index"
                    :value="index"
                >{{ pack.name }}</option>
            </select>
        </div>
        <!-- Pripoistenie -->
        <div class="form-row mb-4">
            <label class="form-label">Pripoistenia</label>
            <div class="d-flex">
                <div
                    class="me-3 custom-checkbox"
                    v-for="(additional, index) in values[insurancePicked].additionalInsurances"
                    :key="index"
                >
                    <input
                        class="form-check-input"
                        type="checkbox"
                        :id="additional.code"
                        v-model="additionalInsurances"
                        :value="index"
                    />
                    <label class="form-check-label" :for="additional.code">{{ additional.name }}</label>
                </div>
            </div>
        </div>
        <!-- počet osôb -->
        <div class="form-row mb-4">
            <p style="margin-bottom: 0.5rem">Počet osôb</p>
            <label for="persons" class="form-label number-of-persons">
                <div class="count">{{ numberOfPersons }}</div>
                <div class="controls">
                    <span @click="incrementPerson">+</span>
                    <span @click="decrementPerson">-</span>
                </div>
            </label>

            <input
                type="number"
                class="form-control"
                name="persons"
                min="1"
                max="3"
                step="1"
                id="persons"
                v-model="numberOfPersons"
            />
        </div>
    </form>

    <div
        class="result"
        v-if="!attemptSubmit && compareDates || this.values[insurancePicked].code == 'year'"
    >
        <FormResult
            :insurancePicked="values[insurancePicked].name"
            :insuranceStartDate="formatDate(insuranceStartDate)"
            :insuranceEndDate="formatDate(insuranceEndDate)"
            :insurancePackage="values[insurancePicked].packages[insurancePackage].name"
            :additionalInsurances="pickedAdditionalInsurances"
            :numberOfPersons="numberOfPersons"
            :daysOfInsurance="getInsuranceDiffDays()"
            :finalCost="calculateFinalCost"
        />
    </div>
    <div v-else class="result">
        <h6>
            Prosím, pre správnu kalkuláciu
            <strong>upravte dátum</strong>.
        </h6>
    </div>
</template>

<script>
import insurance from '@/data/data.json';
import FormResult from './FormResult.vue'

export default {
    components: {
        FormResult
    },
    data() {
        return {
            insurancePicked: 0,
            insuranceStartDate: '',
            insuranceEndDate: '',
            insuranceDaysDiff: '',
            insurancePackage: 0,
            additionalInsurances: [],
            numberOfPersons: 1,
            minPersons: 1,
            maxPersons: 3,
            attemptSubmit: false,
            values: insurance,
            finalCost: 0,
        }
    },
    computed: {
        calculateFinalCost: function () {
            let basePrice = this.values[this.insurancePicked].packages[this.insurancePackage].price;
            if (this.values[this.insurancePicked].calculateDays) {
                basePrice *= this.getInsuranceDiffDays()
            }
            this.finalCost = basePrice;
            if (this.additionalInsurances) {
                for (const additionalInsurance of this.additionalInsurances) {
                    this.finalCost += basePrice * this.values[this.insurancePicked].additionalInsurances[additionalInsurance].percentage;
                }
            }
            this.finalCost = (this.finalCost * this.numberOfPersons)
            return this.finalCost.toLocaleString("sk-SK", { style: 'currency', currency: 'EUR', minimumFractionDigits: 2, maximumFractionDigits: 2 })
        },
        pickedAdditionalInsurances: function () {
            let result = [];
            for (const index of this.additionalInsurances) {
                result.push(this.values[this.insurancePicked].additionalInsurances[index].name);
            }
            return result;
        },


    },
    methods: {
        formatDate(date) {
            if (!date) return
            return new Date(date).toLocaleDateString('sk')
        },
        compareDates() {
            if (this.insuranceStartDate > this.insuranceEndDate) {
                this.attemptSubmit = true;
                return this.insuranceEndDate === '';
            }
            else {
                this.attemptSubmit = false;
            }
        },
        incrementPerson() {
            this.numberOfPersons++;
            if (this.numberOfPersons > this.maxPersons) {
                this.numberOfPersons = this.maxPersons
            }
        },
        decrementPerson() {
            this.numberOfPersons--;
            if (this.numberOfPersons < this.minPersons) {
                this.numberOfPersons = this.minPersons
            }
        },
        getInsuranceDiffDays() {
            const dateA = new Date(this.insuranceStartDate);
            const dateB = new Date(this.insuranceEndDate);
            const diffDays = Math.ceil(Math.abs(dateB - dateA) / (1000 * 60 * 60 * 24));
            return diffDays + 1
        },
    }
}
</script>

<style lang="scss">
@import "@/assets/_variables.scss";

.insurance-form {
    width: 600px;
    margin: 0 auto;
    user-select: none;
    padding-right: 50px;
    @media (max-width: 800px) {
        width: 100%;
        padding-right: 0;
    }
}
.first-row {
    display: flex;
    justify-content: center;
    gap: 15px;
    margin-top: 30px;
}
.form-row {
    position: relative;
    .warning {
        position: absolute;
        background: #dc3545;
        color: white;
        border-radius: 6px;
        font-size: 12px;
        text-align: center;
        padding: 3px 6px;
        left: 10px;
        bottom: -27px;
        z-index: 2;
        &:before {
            content: "";
            position: absolute;
            width: 10px;
            height: 10px;
            top: -5px;
            left: 10px;
            background: #dc3545;
            border-radius: 1px;
            transform: rotate(45deg);
        }
        &.insurancePicked {
            position: absolute;
            top: -27px !important;
            bottom: unset;
            background: #dc3545;
            color: white;
            border-radius: 6px;
            font-size: 12px;
            text-align: center;
            padding: 3px 6px;
            width: 100%;
            left: 50%;
            z-index: 2;
            &:before {
                content: "";
                position: absolute;
                width: 10px;
                height: 10px;
                top: 18px;
                left: 10px;
                background: #dc3545;
                border-radius: 1px;
                transform: rotate(45deg);
            }
            &:after {
                content: "";
                position: absolute;
                width: 10px;
                height: 10px;
                top: 18px;
                right: 10px;
                background: #dc3545;
                border-radius: 1px;
                transform: rotate(45deg);
            }
        }
    }
}
.radio-wrapper {
    position: relative;
    display: inline-block;
    .form-check-label {
        border: 1px solid #ced4da;
        padding: 6px 12px;
        border-radius: 4px;
        color: rgb(70, 69, 69);
        transition: 0.2s ease;
        &:hover {
            box-shadow: 0 0 8px -3px grey;
        }
    }
}

.custom-checkbox {
    input {
        display: none;
        &:checked ~ label {
            color: $clr-white;
            background: $clr-red;
            border-color: darken($clr-red, 4%);
            text-shadow: 0 0 0px white;
        }
    }
    label {
        border: 1px solid #ced4da;
        padding: 6px 12px;
        border-radius: 4px;
        color: rgb(70, 69, 69);
        transition: 0.2s ease;
        &:hover {
            box-shadow: 0 0 8px -3px grey;
        }
    }
}

.number-of-persons {
    display: inline-flex;
    align-items: center;
    border: 1px solid #ced4da;
    .count {
        margin: 10px 20px;
    }
    .controls {
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
        span {
            padding: 3px;
            background: $clr-red;
            width: 25px;
            height: 25px;
            display: inline-block;
            text-align: center;
            color: $clr-white;
            font-size: 24px;
            line-height: 20px;
            &:first-child {
                font-size: 18px;
            }
        }
    }
}

input,
select {
    &:focus {
        box-shadow: none !important;
    }
    &[type="range"] {
        &::-webkit-slider-thumb {
            background: $clr-red;
        }
    }
    &[type="radio"] {
        opacity: 0.5;
        left: -9999px;
        position: absolute;
        &:checked ~ .form-check-label {
            color: $clr-white;
            background: $clr-red;
            border-color: darken($clr-red, 4%);
            text-shadow: 0 0 0px white;
        }
    }
    &[type="date"] {
        &:focus {
            border: 1px solid $clr-red;
        }
    }
    &[type="number"] {
        display: none;
    }
    &.form-select {
        &:focus {
            border: 1px solid $clr-red;
        }
    }
}

button[type="submit"] {
    border: 1px solid #ced4da;
    padding: 6px 12px;
    border-radius: 4px;
    color: rgb(70, 69, 69);
    transition: 0.2s ease;
    background: $clr-red;
    color: $clr-white;
    transition: unset;
    &:hover {
        box-shadow: 0 0 8px -3px grey;
    }
    &:focus {
        box-shadow: none;
    }
    &:active {
        transform: translateY(2px);
    }
}

.result {
    width: 300px;
    ul {
        padding: 0;
        li {
            background: $clr-red;
            color: $clr-white;
            margin: 2px;
            display: inline-block;
            padding: 2px 6px;
            font-size: 14px;
            border-radius: 4px;
        }
    }
    p {
        margin-bottom: 2px;
    }
    h5,
    h6 {
        strong {
            color: $clr-red;
        }
    }
}
</style>