<template>
    <!-- 上下排布剧中对齐 间隔20px -->
    <div class="main">
        <!-- 标题 -->
        <h1>中文转日语假名</h1>
        <h4>输入中文 点击转换 会以拼音按照一定规则转换为假名</h4>
        <!-- 输入中文 输入框大小为50px 黑色主题 默认显示一些内容-->
        <input type="text" v-model="input" class="input" placeholder="请输入中文" />

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
                    <!-- 缩小图片 -->
                    <img src="@/assets/icon/copy.png" alt="copy" style="width: 20px; height: 20px;" />
                </div>
            </div>
        </div>
        <!-- 点击按钮 按钮大小为50px 黑色主题-->
        <button @click="handleClick" class="button">转换</button>
        <div class="rule">
            <!-- 以表格显示 表格带边框 表格内容居中-->
            <h4 style="margin-top: 20px;">转换规则</h4>
            <div>
                L->R, C->K
                X->shi,
                Q->KI,
                <br>
                ZH->Z,
                CH->S,
                SH->S,
                <br>
                后鼻音全部改为前鼻音
            </div>
            <!-- 显示在浏览器底部 -->
            <h4 style="position: fixed; bottom: 0; left: 0; right: 0; text-align: center;">纯属娱乐</h4>
        </div>
    </div>
</template>



<script setup>
import { ref, onMounted } from 'vue';
import { pinyin } from 'pinyin-pro';
import hepburn from 'hepburn';
import Speech from 'speak-tts';
const input = ref('');
const result = ref('这里将显示结果');
const speech = new Speech()


function initSpeech() {
    speech.init({
        'volume': 1,
        'lang': 'ja-JP',
        'rate': 1,
        'pitch': 1,
        'voice': 'Eddy (日语（日本）)',
        'splitSentences': true,
        'listeners': {
            'onvoiceschanged': (voices) => {
                for (let i = 0; i < voices.length; i++) {
                    if (voices[i].lang == 'ja-JP') {
                        console.log("Event voiceschanged", voices[i])
                    }
                }
            }
        }
    });
}

// vue3 初始化
onMounted(() => {
    initSpeech();
})

const handleClick = () => {
    if (input.value != '') {
        const temp = pinyin(input.value, { toneType: 'none', type: 'str' });
        console.log(temp);

        let input_str = temp + " ";
        console.log(input_str);

        let result_str = [];
        let temp_word = '';
        for (let i = 0; i < input_str.length; i++) {
            if (input_str[i] != ' ') {
                temp_word += input_str[i];
                console.log("temp_word" + temp_word);
            } else {
                console.log("temp_word is == " + temp_word);
                if (temp_word[0] == 'l') {
                    // 修改temp_word的第一个字符    
                    temp_word = temp_word.replace('l', 'r');
                    console.log("now is ===" + temp_word);
                }
                // 如果temp_word的第一个字符是c 则替换为s
                if (temp_word[0] == 'c') {
                    if (temp_word[1] == 'h') {
                        temp_word = temp_word.replace('ch', 's');
                        console.log("now is ===" + temp_word);
                    } else {
                        temp_word = temp_word.replace('c', 'z');
                        console.log("now is ===" + temp_word);
                    }
                }
                // 如果temp_word的第一个字符是x 则替换为shi
                if (temp_word[0] == 'x') {
                    temp_word = temp_word.replace('x', 'shi');
                    console.log("now is ===" + temp_word);
                }
                // 如果temp_word的第一个字符是q 则替换为k
                if (temp_word[0] == 'q') {
                    temp_word = temp_word.replace('q', 'ki');
                    console.log("now is ===" + temp_word);
                }
                if (temp_word[0] == 'z') {
                    if (temp_word[1] == 'h') {
                        temp_word = temp_word.replace('zh', 'z');
                        console.log("now is ===" + temp_word);
                    } else {
                        temp_word = temp_word.replace('zi', 'zu');
                        console.log("now is ===" + temp_word);
                    }
                }
                if (temp_word[0] == 'r') {
                    temp_word = temp_word.replace('r', '');
                    console.log("now is ===" + temp_word);
                }
                // 后鼻音的g去掉 取字符串最后一位
                if (temp_word.endsWith('g')) {
                    temp_word = temp_word.slice(0, -1);
                    console.log("now is ===" + temp_word);
                }
                result_str.push(temp_word);
                temp_word = '';
            }
        }
        console.log(result_str);
        // 将数组合成字符串
        let string_pinyin = result_str.join('');
        // 将拼音转换为假名
        let string_katakana = hepburn.toKatakana(string_pinyin);
        // 去掉中间的空格
        // string_katakana = string_katakana.replace(/\s/g, '');
        console.log(string_katakana);
        result.value = string_katakana;
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

</script>

<style scoped>
.main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    gap: 20px;
}

.title {
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 20px;
}

.input {

    /* 如果是手机打开，则宽度为80% */
    @media (max-width: 768px) {
        width: 80%;
    }

    /* 如果是电脑打开，则宽度为40% */
    @media (min-width: 768px) {
        width: 40%;
    }

    height: 40px;
    border: 1px solid #333;
    border-radius: 5px;
    padding: 0 10px;
    font-size: 16px;

    @media (prefers-color-scheme: dark) {
        color: #fff;
        background-color: #000;
    }

    @media (prefers-color-scheme: light) {
        color: #000000;
        background-color: #e9e9e9;
    }

    outline: none;
    /* 自动换行 */
    word-wrap: break-word;
}

.input:focus {
    border-color: #666;
}

.button {
    width: 40%;
    height: 50px;
    border: none;
    border-radius: 5px;
    padding: 0 10px;
    font-size: 16px;

    /* color: #fff;
    background-color: #000; */
    /* 判断浏览器是深色还是浅色 */
    @media (prefers-color-scheme: dark) {
        color: #fff;
        background-color: #000;
    }

    @media (prefers-color-scheme: light) {
        color: #818181;
        background-color: #e9e9e9;
    }

    cursor: pointer;
    transition: background-color 0.3s;
}

.button:hover {
    @media (prefers-color-scheme: dark) {
        background-color: #333;
    }

    @media (prefers-color-scheme: light) {
        background-color: #f1f1f1;
    }
}

.result {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
    max-width: 60%;
}

.result-text {
    flex: 1;
    font-size: 20px;
    border-radius: 5px;
    padding: 0 10px;
    /* 限制宽度 */
    /* max-width: 70%; */
    /* 字符在小于宽度时换行 */
    /* word-break: break-all;
    word-wrap: break-word; */
}

.icon {
    width: 20px;
    height: 20px;
    background-color: var(--color-background);
    border-radius: 5px;
    /* 间距10px */
    margin-left: 10px;
    cursor: pointer;
}

.icon:hover {
    background-color: #333;
}

.icon-container {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: center;
}
</style>