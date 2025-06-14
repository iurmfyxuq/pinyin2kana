<template>
    <div class="mainContent">
        <h1>背单词</h1>
        <!--上传单词文本 
        按钮 点击后弹出文件选择器 选择后读取文件内容 显示在文本框中 文本框大小为500px 黑色主题 
        默认显示一些内容
        如果已经选择了文件重新点击则切换选择-->

        <div class="result">
            <div class="word">{{ currentWord }}</div>
            <div v-if="currentWord" class="icon-container">
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

        <!-- 如果列表为空则显示一些内容 如果列表不为空则显示下一个 -->
        <button class="button" @click="nextWord" v-if="words.length > 0">下一个</button>
        <button class="button" @click="readFile" v-else>选择单词文件</button>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Speech from 'speak-tts';


const text = ref('');
const words = ref([]);
const currentWord = ref('');
const speech = new Speech();


const handleFileChange = (e) => {
    const file = e.target.files[0];
    const reader = new FileReader();
    reader.onload = (e) => {
        // 读取单词到列表中 如果为空则跳过
        words.value = e.target.result.replace(/\r/g, '').split('\n').map(word => word.trim()).filter(word => word !== '');
        console.log(words.value);
        // 在文件内容完全读取后再显示第一个单词
        if (words.value.length > 0) {
            nextWord();
        }
    };
    reader.readAsText(file);
};

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

const readFile = () => {
    // 重新选择单词文件
    const input = document.createElement('input');
    input.type = 'file';
    input.accept = '.txt';
    input.onchange = handleFileChange;
    input.click();
};

const nextWord = () => {
    currentWord.value = words.value[Math.floor(Math.random() * words.value.length)];
};

const handleCopy = () => {
    navigator.clipboard.writeText(currentWord.value);
}
const handleSound = () => {
    speech.speak({ text: currentWord.value });
}
</script>

<style scoped>
@import  "../assets/style.css";
.word {
    flex: 1;
    font-size: 50px;
    width: 100%;
    border-radius: 5px;
    padding: 0 10px;
}
</style>