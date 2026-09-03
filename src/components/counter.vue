<template>
    <div id="content">
        <span>{{ text }}</span>
        <div id="timer">
            <button @click="addValue" :style="buttonsStyle">+</button>
            <p>{{ value }}</p>
            <button @click="removeValue" :style="buttonsStyle">-</button>
        </div>
        <button @click="setTimer" :style="buttonsStyle">поставить таймер</button>
        <button @click="resetTimer">сбросить таймер</button>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

const value = ref(0);
const text = ref('заведи таймер:');
const timerIsGoing = ref(false);
const buttonsStyle = ref({
    background: '#00000016'
})

const addValue = () => {
    if ((value.value < 9) && !timerIsGoing.value) {
        value.value++;
    } else {
        
    }
};
const removeValue = () => {
    if ((value.value > 2) && !timerIsGoing.value) {
        value.value--;
    }
};
const setTimer = () => {
    if ((0 < value.value && value.value < 10) && !timerIsGoing.value && value.value !== null) {
        const timerStartDate = Math.floor(Date.now() / (1000 * 60 * 60 * 24));
        localStorage.setItem("date", timerStartDate);
        localStorage.setItem("value", value.value);
        localStorage.setItem("timer", true)
        text.value = 'таймер заведен!';
        timerIsGoing.value = true;
        buttonsStyle.value = {
            backgroundColor: '#00000066'
        }
    }
};
const resetTimer = () => {
    if (timerIsGoing) {
        value.value = 0;
        localStorage.removeItem("date");
        localStorage.removeItem("value");
        localStorage.setItem("timer", false);
        timerIsGoing.value = false;
        text.value = 'заведи таймер:';
        buttonsStyle.value = {
            backgroundColor: '#00000016'
        };
    }
};

const modifyTimer = (days) => {
    value.value = value.value - days;
};

onMounted(() => {
    const timerStartDate = localStorage.getItem("date");
    const currentValue = localStorage.getItem("value");
    console.log(currentValue);
    value.value = currentValue;
    console.log('onMounted', value.value)
    timerIsGoing.value = localStorage.getItem("timer");
    text.value = 'до конца таймера осталось:';

    if (timerStartDate && currentValue && timerIsGoing.value) {
        buttonsStyle.value = {
            backgroundColor: '#00000066'
        };
        const CurrentDate = Math.floor(Date.now() / (1000 * 60 * 60 * 24));
        const daysToRemove = CurrentDate - timerStartDate;
        if (daysToRemove > 0) {
            modifyTimer(daysToRemove);
        }
    };
    if (value.value < 1 && timerIsGoing.value) {
        text.value = 'таймер завершился!';
        resetTimer();
    }
});
</script>

<style scoped>
#content {
    display: flex;
    flex-direction: column;
    gap: 30px;
/* НАЧАЛО: Добавление стилей контейнера для центрации и монохромного фона  (ИИ)*/
    align-items: center;
    justify-content: center;
    max-width: 320px;
    margin: 40px auto;
    padding: 40px 20px;
    background-color: #ffffff;
    border: 1px solid #111111;
    border-radius: 0px;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    color: #111111;
/* КОНЕЦ: Добавление стилей контейнера для центрации и монохромного фона  (ИИ)*/
}

#counter {
    flex-direction: row;
    gap: 20px;
}

/* НАЧАЛО: Добавление монохромных стилей для блока таймера  (ИИ)*/
#timer {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 20px;
    width: 100%;
}

#timer p {
    margin: 0;
    font-size: 2.5rem;
    font-weight: 300;
    letter-spacing: -1px;
    min-width: 40px;
    text-align: center;
}
/* КОНЕЦ: Добавление монохромных стилей для блока таймера  (ИИ)*/

/* НАЧАЛО: Добавление минималистичного оформления текста и кнопок  (ИИ)*/
span {
    font-size: 0.9rem;
    text-transform: uppercase;
    letter-spacing: 1.5px;
    font-weight: 500;
    color: #444444;
}

button {
    border: 1px solid #111111 !important;
    color: #111111;
    padding: 10px 18px;
    font-size: 0.85rem;
    text-transform: uppercase;
    letter-spacing: 1px;
    cursor: pointer;
    transition: all 0.2s ease;
    border-radius: 0px;
}

button:hover {
    background-color: #111111 !important;
    color: #ffffff;
}

button:active {
    transform: scale(0.98);
}
/* КОНЕЦ: Добавление минималистичного оформления текста и кнопок  (ИИ)*/
</style>