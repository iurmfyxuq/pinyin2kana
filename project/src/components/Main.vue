<template>
    <div class="main">
        <!-- 导航栏 -->
        <div class="nav">
            <div 
                class="nav-item" 
                :class="{ active: currentPage === 'convert' }"
                @click="currentPage = 'convert'">
                汉字转片假名
            </div>
            <div 
                class="nav-item" 
                :class="{ active: currentPage === 'romaji2hira' }"
                @click="currentPage = 'romaji2hira'">
                罗马字转假名
            </div>
            <div 
                class="nav-item" 
                :class="{ active: currentPage === 'reciteWords' }"
                @click="currentPage = 'reciteWords'">
                背单词
            </div>
        </div>
        <!-- 上下间隔 -->
        <div style="height: 80px;"></div>
        <!-- 页面内容 -->
        <Convert v-if="currentPage === 'convert'" />
        <Romaji2hira v-if="currentPage === 'romaji2hira'" />
        <ReciteWords v-if="currentPage === 'reciteWords'" />
    </div>
</template>

<script setup>
import { ref } from 'vue';
import Convert from './Convert.vue';
import Romaji2hira from './Romaji2hira.vue';
import ReciteWords from './ReciteWords.vue';

const currentPage = ref('convert');
</script>

<style scoped>
.main {
    display: flex;
    flex-direction: column;
    align-items: center;
    min-height: 100vh;
    padding: 20px;
}

.nav {
    display: flex;
    gap: 20px;
    margin-bottom: 20px;
}

.nav-item {
    padding: 10px 20px;
    cursor: pointer;
    border-radius: 5px;
    transition: all 0.3s;
}

.nav-item.active {
    background-color: #333;
    color: #fff;
}

@media (prefers-color-scheme: light) {
    .nav-item.active {
        background-color: #e0e0e0;
        color: #000;
    }
}
</style>