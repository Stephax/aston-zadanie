<template>
    <div class="result">
        <p v-if="insurancePicked">
            Zvolené poistenie:
            <strong>{{ insurancePicked }}</strong>
        </p>

        <p v-if="insuranceStartDate">Začiatok poistenia: <strong>{{ insuranceStartDate }}</strong></p>

        <p v-if="insuranceEndDate && insurancePicked !== 'Celoročné poistenie'">
            Koniec poistenia: <strong>{{ insuranceEndDate }}</strong>
        </p>
        <p v-if="daysOfInsurance && insurancePicked !== 'Celoročné poistenie'">Počet dní poistenia: <strong>{{
            daysOfInsurance
        }}
                dní</strong></p>

        <p v-if="insurancePackage">Balík poistenia: <strong>{{ insurancePackage }}</strong></p>

        <p v-if="additionalInsurances.length > 0">
            Pripoistenia:
        <ul>
            <li v-for="(additional, index) in additionalInsurances" :key="index"><strong>{{ additional }}</strong></li>
        </ul>
        </p>

        <p v-if="insurancePicked">Počet osôb: <strong>{{ numberOfPersons }}</strong></p>


        <h5 v-if="insurancePicked !== 'Krátkodobé poistenie' && finalCost">
            Výsledná suma: <strong>{{ finalCost }}</strong>
        </h5>
        <h5 v-else-if="insuranceStartDate && insuranceEndDate">
            Výsledná suma: <strong>{{ finalCost }}</strong>
        </h5>
    </div>
</template>

<script >
export default {
    props: {
        insurancePicked: {
            type: String,
        },
        insuranceStartDate: {
            type: String,
        },
        insuranceEndDate: {
            type: String,
        },
        daysOfInsurance: {
            type: Number
        },
        insurancePackage: {
            type: String,
        },
        additionalInsurances: {
            type: Array,
        },
        numberOfPersons: {
            type: Number,
        },
        finalCost: {
            type: String,
        },
    }
}
</script>

<style lang="scss" scoped>
@import "@/assets/_variables.scss";
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
    h5 {
        strong {
            color: $clr-red;
        }
    }
}
</style>