<template>
    <div class="mainContent">

        <!-- 标题 -->
        <h1>罗马字转假名</h1>
        <h4>输入罗马字点击 转换 转换为平假名</h4>
        <!-- 输入罗马字 输入框大小为50px 黑色主题 默认显示一些内容-->
         <!-- 无法输入除了英文字母以外的字符 -->
        <input type="text" v-model="input" class="input" placeholder="请输入罗马字" @input="handleInput" /> 

        <!-- 结果栏 左右两边分开 -->
        <div class="result">
            <!-- 转换结果 不可编辑 限制宽度 -->
            <div class="result-text">{{ result }}</div>
            <div class="icon-container">
                <!-- 发音图标 点击播放-->
                <div class="icon" @click="handleSound">
                    <img src="@/assets/icon/sound.png" alt="sound" style="width: 20px; height: 20px;" />
                </div>
                <!-- 复制图标 点击复制-->
                <div class="icon" @click="handleCopy">
                    <img src="@/assets/icon/copy.png" alt="copy" style="width: 20px; height: 20px;" />
                </div>
            </div>
        </div>
        <!-- 点击按钮 按钮大小为50px 黑色主题-->
        <button @click="handleClick" class="button">转换</button>
        
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Speech from 'speak-tts';
import hepburn from 'hepburn';


const input = ref('');
const result = ref('这里将显示结果');
const speech = new Speech();

function initSpeech() {
    speech.init({
        'volume': 1,
        'lang': 'ja-JP',
        'rate': 1,
        'pitch': 1,
        'splitSentences': true,
    });
}

onMounted(() => {
    initSpeech();
});

const handleClick = () => {
    if (input.value != '' && input.value != '请输入罗马字') {
        let string_hira = hepburn.toHiragana(input.value);
        result.value = string_hira;
    }
}

const handleCopy = () => {
    if (result.value != '这里将显示结果') {
        navigator.clipboard.writeText(result.value);
    }
}
const handleSound = () => {
    if (result.value != '这里将显示结果') {
        speech.speak({ text: result.value });
    }
}

const handleInput = (event) => {
    // input.value = event.target.value.replace(/[^a-zA-Z]/g, '');
}
</script>


<!-- romaji2hira  -->
<style scoped>
@import '../assets/style.css';
</style> 