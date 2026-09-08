<template>
    <div id="content">
        <span>{{ text }}</span>
        <div id="timer">
            <button @click="addDaysToWork" :style="buttonsStyle">+</button>
            <p>{{ timerDaysToWork }}</p>
            <button @click="removeDaysToWork" :style="buttonsStyle">-</button>
        </div>
        <button @click="startTimer" :style="buttonsStyle">поставить таймер</button>
        <button @click="resetTimer">сбросить таймер</button>
    </div>
</template>

<script setup>
localStorage.removeItem("date");
localStorage.removeItem("value");
localStorage.removeItem("timer");

import { ref, onMounted, onUnmounted } from 'vue';

// отображаемое число дней работы таймера в "string"
const timerDaysToWork = ref('0');
// введенное количество дней работы таймера в "int"
const inputedDaysToWork = ref(0);
// айди функции воспроизведения таймера
let timerId = null;

// время начала работы таймера в милисекундах
let timerStartTime;
// время работы таймера в милисекундах
const timerToWorkTime = ref(0);
// время окончания работы таймера в милисекундах
const timerFinishTime = ref(0);

// статус работы таймера
const timerIsGoing = ref(false);
// отображаемый статусный текст
const text = ref('заведи таймер');
// стиль кнопок отображающий статус их блокировки
const buttonsStyle = ref({
    background: '#00000016'
});


// фун-я для добавления одного дня работы таймера при нажатии кнопки "+"
const addDaysToWork = () => {
    // если текущее значение дней работы таймера меньше максимального и таймер не запущен
    if ((inputedDaysToWork.value < 9) && !timerIsGoing.value) {

        inputedDaysToWork.value++;
        // изменение отображаемого значения
        timerDaysToWork.value = inputedDaysToWork.value;
    };
};
// фун-я для вычитания одного дня работы таймера при нажатии кнопки "-"
const removeDaysToWork = () => {
    // если текущее значение дней работы таймера больше минимального и таймер не запущен
    if ((inputedDaysToWork.value > 1) && !timerIsGoing.value) {

        inputedDaysToWork.value--;
        // изменение отображаемого значения
        timerDaysToWork.value = inputedDaysToWork.value;
    };
};
// фун-я для запуска таймера при нажати кнопки "запустить таймер"
const startTimer = () => {
    // если текущее значение дней работы таймера в заданном диапазоне и таймер не запущен
    if (0 < inputedDaysToWork.value && inputedDaysToWork.value < 10 && !timerIsGoing.value) {

        // сохранение статуса работы таймера
        timerIsGoing.value = true;
        // сохранение в "localStorage"
        localStorage.setItem("timerIsGoing", true);

        // время начала работы таймера в милисекундах
        timerStartTime = Date.now();
        // сохранение в "localStorage"
        localStorage.setItem("timerStartTime", timerStartTime);

        // время работы таймера в милисекундах
        timerToWorkTime.value = inputedDaysToWork.value * 1000 * 60 * 60 * 24;
        // сохранение в "localStorage"
        localStorage.setItem("timerToWorkTime", timerToWorkTime.value);
        // время окончания работы таймера в милисекундах
        timerFinishTime.value = timerStartTime + timerToWorkTime.value;


        // изменение статусного текста
        text.value = 'таймер заведен, до конца таймера осталось';
        // изменение статусного стиля кнопок
        buttonsStyle.value = {
            backgroundColor: '#00000066'
        };

        // изменение отображения отсчета таймера на формат "hh:mm"
        timerDaysToWork.value =
            timerToWorkTime.value / (1000 * 60 * 60)
            + 'h:'
            + timerToWorkTime.value / (1000 * 60)
            + 'min'
        ;

        // первое обновление таймера
        updateTime();
        // присвоение айди цикла с обновлением таймера раз в 1 секунду (1000 милисекунд) и старт работы самого цикла
        timerId = setInterval(updateTime, 1000);
    }
};
// фун-я для сброса таймера при нажати кнопки "сбросить таймер"
const resetTimer = () => {
    // если таймер идет и отсчет не равен нулю
    if (timerIsGoing && timerToWorkTime.value) {

        // остановка цикла обновлений таймера
        clearInterval(timerId);

        // обнуление выводимых значений
        inputedDaysToWork.value = 0;
        timerDaysToWork.value = 0;
        // обнуление статуса работы таймера
        timerIsGoing.value = false;
        // обнуление статуса в "localStorage"
        localStorage.setItem("timerIsGoing", false);
        // обнуление сохраненных в "localStorage" значений
        localStorage.removeItem("timerStartTime");
        localStorage.removeItem("timerToWorkTime");

        // изменение статусного текста
        text.value = 'таймер сброшен';
        // изменение статусного стиля кнопок
        buttonsStyle.value = {
            backgroundColor: '#00000016'
        };
    };
};
// фун-я для обновления отсчета до конца таймера
const updateTime = () => {
    
    // текущее время в милисекундах
    const currentTime = Date.now();
    // время в милисекундах, прошедшее с начала работы таймера
    const passedTime = currentTime - timerStartTime;
    // обновление локального значения начала отсчета таймера
    timerStartTime = currentTime;

    // время работы таймера в милисекундах
    timerToWorkTime.value -= passedTime;
    // изменение отображения отсчета таймера на формат "hh:mm"
    timerDaysToWork.value =
        Math.floor((timerToWorkTime.value / (1000 * 60 * 60 * 24)) % 365)
        + 'd:'
        + Math.floor((timerToWorkTime.value / (1000 * 60 * 60)) % 24)
        + 'h:'
        + Math.floor((timerToWorkTime.value / (1000 * 60)) % 60)
        + 'min'
    ;

    // если время работы таймера отрицательно или равно нулю и таймер работает
    if (timerToWorkTime.value <= 0 && timerIsGoing.value) {

        timerToWorkTime.value = 0;
        // сброс таймера
        resetTimer();
        // изменение статусного текста
        text.value = 'таймер завершился';
    };
};


// фун-я для установки таймера при загрузке DOM-элементов
onMounted(() => {

    // вывод сохраненных значений из "localStorage"
    timerToWorkTime.value = JSON.parse(localStorage.getItem("timerToWorkTime")) || 0;
    timerIsGoing.value = JSON.parse(localStorage.getItem("timerIsGoing")) || false;
    timerStartTime = JSON.parse(localStorage.getItem("timerStartTime")) || 0;


    // если таймер работает и у таймера осталось время работы
    if (timerIsGoing.value && timerToWorkTime.value) {

        // изменение статусного текста
        text.value = 'до конца таймера осталось';
        // изменение статусного стиля кнопок
        buttonsStyle.value = {
            backgroundColor: '#00000066'
        };
        
        // первое обновление таймера
        updateTime();
        // присвоение айди цикла с обновлением таймера раз в 1 секунду (1000 милисекунд) и старт работы самого цикла
        timerId = setInterval(updateTime, 1000);
    };
});

// фун-я для удаления цикла обновлений таймера при удалении DOM-элемента
onUnmounted(() => {

    // если цилк обновлений таймера есть
    if (timerId) {

        // удаление цикла
        clearInterval(timerId);
    };
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
    justify-content: space-between;
    gap: 10px;
    width: 100%;
}

#timer p {
    margin: 0;
    font-size: 1.25rem;
    font-weight: 500;
    font-family: monospace;
    letter-spacing: -0.5px;
    text-align: center;
    flex: 1;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

#timer button {
    flex-shrink: 0;
    width: 36px;
    padding: 10px 0;
    text-align: center;
}
/* КОНЕЦ: Добавление монохромных стилей для блока таймера  (ИИ)*/

/* НАЧАЛО: Добавление минималистичного оформления текста и кнопок  (ИИ)*/
span {
    font-size: 0.9rem;
    text-align: center;
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