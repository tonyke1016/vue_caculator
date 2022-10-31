<template>
    <div class="background">
        <div class="caculator">
            <div class="answer">
                <!-- <el-input
                v-model="input"
                :value="input !== '' ? input : 0"
                style="border-radius: 20px !important"
            /> -->
                {{ input || 0 }}
            </div>

            <div class="computeArea">
                <div class="function" @click="clear">AC</div>
                <div class="function" @click="positiveNegativeSwitch">+/-</div>
                <div class="function" @click="divided100">%</div>
                <div
                    class="operator"
                    :class="{ highlight: highlight === '/' }"
                    @click="operatorMark('divided')"
                >
                    ÷
                </div>
                <div class="number" @click="append('7')">7</div>
                <div class="number" @click="append('8')">8</div>
                <div class="number" @click="append('9')">9</div>
                <div
                    class="operator"
                    :class="{ highlight: highlight === '*' }"
                    @click="operatorMark('times')"
                >
                    X
                </div>
                <div class="number" @click="append('4')">4</div>
                <div class="number" @click="append('5')">5</div>
                <div class="number" @click="append('6')">6</div>
                <div
                    class="operator"
                    :class="{ highlight: highlight === '-' }"
                    @click="operatorMark('minus')"
                >
                    -
                </div>
                <div class="number" @click="append('1')">1</div>
                <div class="number" @click="append('2')">2</div>
                <div class="number" @click="append('3')">3</div>
                <div
                    class="operator"
                    :class="{ highlight: highlight === '+' }"
                    @click="operatorMark('plus')"
                >
                    +
                </div>
                <div class="number" @click="append('0')">0</div>
                <div></div>
                <div class="number" @click="append('.')">.</div>
                <div class="operator" @click="result">=</div>
            </div>
        </div>
    </div>
</template>
<script setup>
import { ref, watchEffect } from "vue";
watchEffect(
    () => {
        console.log("previous 觀察", previousNum.value);
        console.log("operator 觀察", operator.value);
        console.log("input 觀察", input.value);
    },
    { flush: "post" }
);
//input v-model
const input = ref("");
const previousNum = ref("");
const operator = ref("");
// 數字按鍵添加功能
const append = (number) => {
    //按運算子後按數字 數字才變化
    if (isOperator.value === true) {
        input.value = "";
        isOperator.value = false;
    }
    //判斷按下等於後 再按數字重新計算
    if (isResult.value === true) {
        input.value = "";
        isResult.value = false;
    }
    //.的判斷
    if (number === ".") {
        if (!input.value.includes(".")) {
            // 判斷是否於第一位打.
            if (input.value.length === 0) {
                input.value += "0";
            }
            input.value += number;
            return;
        }
        return;
    }
    //0的判斷
    if (number === "0") {
        if (input.value.split("")[0] === "0" && input.value.split("")[1] !== ".") {
            return;
        }
        input.value += number;
        return;
    }
    //1-9判斷
    if (Number(number) >= 1 && Number(number) <= 9) {
        if (input.value.split("")[0] === "0" && input.value.split("")[1] !== ".") {
            input.value = "";
            input.value += number;
            return;
        }
        input.value += number;
    }
};
// 高亮選中運算符
const highlight = ref(null);
const isOperator = ref(false);
//運算符
const operatorMark = (mark) => {
    //如果有計算 再按運算符
    if (previousNum.value !== "" && input.value !== "" && operator.value !== "") {
        result();
        return;
    }
    if (mark === "plus") {
        highlight.value = "+";
        operator.value = "+";
    }
    if (mark === "minus") {
        highlight.value = "-";
        operator.value = "-";
    }
    if (mark === "times") {
        highlight.value = "*";
        operator.value = "*";
    }
    if (mark === "divided") {
        highlight.value = "/";
        operator.value = "/";
    }
    if (input.value !== "") {
        previousNum.value = input.value;
    }
    isOperator.value = true;
};
//結果
const isResult = ref(false);
const result = () => {
    isResult.value = true;
    if (previousNum.value !== "") {
        switch (operator.value) {
            case "+":
                if (previousNum.value === "錯誤") {
                    input.value = "錯誤";
                    return;
                }
                input.value = Number(previousNum.value) + Number(input.value);
                input.value = String(input.value);
                break;
            case "-":
                if (previousNum.value === "錯誤") {
                    input.value = "錯誤";
                    return;
                }
                input.value = Number(previousNum.value) - Number(input.value);
                input.value = String(input.value);
                break;
            case "*":
                if (previousNum.value === "錯誤") {
                    input.value = "錯誤";
                    return;
                }
                input.value = Number(previousNum.value) * Number(input.value);
                input.value = String(input.value);
                break;
            case "/":
                if (previousNum.value === "錯誤") {
                    input.value = "錯誤";
                    return;
                }
                if (Number(input.value) === 0) {
                    operator.value = "";
                    input.value = "錯誤";
                    return;
                }
                input.value = Number(previousNum.value) / Number(input.value);
                input.value = String(input.value);
                break;
        }
    }
    previousNum.value = "";
    highlight.value = null;
};
//正負切換
const positiveNegativeSwitch = () => {
    if (input.value.split("")[0] !== "-") {
        input.value = "-" + input.value;
    } else {
        input.value = input.value.substr(1);
    }
};
//除100
const divided100 = () => {
    input.value = Number(input.value) / 100;
    input.value = String(input.value);
};
//清空
const clear = () => {
    operator.value = "";
    previousNum.value = "";
    input.value = "";
    highlight.value = null;
};
</script>
<style lang="scss">
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
}
</style>
<style lang="scss" scoped>
.background {
    width: 100vw;
    height: 100vh;
    background-color: rgb(0, 0, 0);
    // background:
    //  radial-gradient(
    //     circle,
    //     #d16ba5,
    //     #c777b9,
    //     #ba83ca,
    //     #aa8fd8,
    //     #9a9ae1,
    //     #8aa7ec,
    //     #79b3f4,
    //     #69bff8,
    //     #52cffe,
    //     #41dfff,
    //     #46eefa,
    //     #5ffbf1
    // );
    display: flex;
    justify-content: center;
    align-items: center;
    .caculator {
        width: 700px;
        height: 700px;
        padding: 50px;
        background-color: rgba(255, 255, 255, 0.5);
        display: flex;
        flex-direction: column;
        justify-content: center;
        .answer {
            width: 100%;
            height: 80px;
            background: white;
            border-radius: 20px;
            margin-bottom: 20px;
            text-align: right;
            line-height: 80px;
            padding-right: 10px;
            // .el-input {
            //     --el-input-border-radius: 20px;
            //     // text-align: left;
            //     direction: rtl;
            //     height: 100%;
            //     caret-color: transparent;
            // }
        }
        .computeArea {
            width: 100%;
            height: 100%;
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            grid-template-rows: repeat(5, 1fr);
            grid-gap: 0.5em;
            .number {
                cursor: pointer;
                background-color: rgb(51, 51, 51);
                color: white;
                border-radius: 50%;
                line-height: 95px;
            }
            .operator {
                cursor: pointer;
                background: rgb(255, 159, 0);
                color: white;
                border-radius: 50%;
                line-height: 95px;
                &.highlight {
                    color: orange;
                    background-color: white;
                }
            }
            .function {
                cursor: pointer;
                background: rgb(165, 165, 165);
                color: black;
                border-radius: 50%;
                line-height: 95px;
            }
        }
    }
}
</style>
