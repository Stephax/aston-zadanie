<template>
    <p v-if="insurancePicked">
        Zvolené poistenie:
        <strong>{{ insurancePicked }}</strong>
    </p>
    <Transition name="slide-to-left">
        <p v-if="insuranceStartDate">
            Začiatok poistenia:
            <strong>{{ insuranceStartDate }}</strong>
        </p>
    </Transition>

    <Transition name="slide-to-left">
        <p v-if="insuranceEndDate && insurancePicked !== 'Celoročné poistenie'">
            Koniec poistenia:
            <strong>{{ insuranceEndDate }}</strong>
        </p>
    </Transition>
    <p v-if="daysOfInsurance && insurancePicked !== 'Celoročné poistenie'">
        Počet dní poistenia:
        <strong>
            {{ daysOfInsurance }}
            <strong v-if="daysOfInsurance <= 1">deň</strong>
            <strong v-else-if="daysOfInsurance >= 2 && daysOfInsurance <= 4">dni</strong>
            <strong v-else>dní</strong>
        </strong>
    </p>

    <p v-if="insurancePackage">
        Balík poistenia:
        <strong>{{ insurancePackage }}</strong>
    </p>
    <Transition name="slide-to-left">
        <p v-if="additionalInsurances.length > 0">Pripoistenia:</p>
    </Transition>
    <ul v-if="additionalInsurances.length > 0">
        <li name="slide-to-left" v-for="(additional, index) in additionalInsurances" :key="index">
            <strong>{{ additional }}</strong>
        </li>
    </ul>

    <p v-if="insurancePicked">
        Počet osôb:
        <strong>{{ numberOfPersons }}</strong>
    </p>

    <h5 v-if="insurancePicked !== 'Krátkodobé poistenie' && finalCost">
        Výsledná suma:
        <strong>{{ finalCost }}</strong>
    </h5>
    <h5 v-else-if="insuranceStartDate && insuranceEndDate">
        Výsledná suma:
        <strong>{{ finalCost }}</strong>
    </h5>
</template>

<script>
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
            type: Number,
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
    },
};
</script>
