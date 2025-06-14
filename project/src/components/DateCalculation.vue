<template>
    <div class="mainContent">
        <h1>日期计算</h1>
        <div class="result">
            <h3 style="padding-right: 20px;">从</h3>
            <!-- 判断系统浏览器是否启用深色模式  如果是则使用深色主题 -->
            <VueDatePicker v-model="date" :dark="isDark" locale="cn" :format="showFormat"></VueDatePicker>
        </div>
        <div class="result">
            <h3 style="padding-right: 20px;">到</h3>
            <VueDatePicker v-model="date2" :dark="isDark" locale="cn" :format="showFormat"></VueDatePicker>
        </div>
        <button class="button" @click="calculate">计算</button>
        <div class="result" v-if="resultYear >= 0">
            <h3>一共过去了{{ resultYear }}年{{ resultMonth }}个月</h3>
        </div>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { differenceInDays, differenceInMonths, differenceInYears } from "date-fns";

import VueDatePicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css'

const isDark = ref(false);

const date = ref();
const date2 = ref(new Date());

const resultYear = ref(-1);
const resultMonth = ref(-1);

const calculate = () => {
    if (date.value && date2.value) {
        resultYear.value = differenceInYears(date2.value, date.value);

        resultMonth.value = differenceInMonths(date2.value, date.value);
        resultMonth.value = resultMonth.value - resultYear.value * 12;
    }
}

onMounted(() => {
    const mediaQuery = window.matchMedia('(prefers-color-scheme: dark)');
    isDark.value = mediaQuery.matches;
    if (isDark.value) {
        console.log('dark');
    }
    const handleChange = (e) => {
        isDark.value = e.matches;
    };

    mediaQuery.addEventListener('change', handleChange);
});
const showFormat = (date) => {
    const day = date.getDate();
    const month = date.getMonth() + 1;
    const year = date.getFullYear();

    return `${year}/${month}/${day}`;
}
</script>

<style scoped>
@import '../assets/style.css';
</style>