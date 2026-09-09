<template>
    <div id="content">

        <div class="section" id="head">
            <span id="heading">Harmful habit helper</span>
        </div>

        <div class="section" id="programs">
            <div class="card">
                <span class="description">Курение</span>
            </div>
            <div class="card">
                <span class="description">Алкоголь</span>
            </div>
            <div class="card">
                <span class="description">Лудомания</span>
            </div>
        </div>

        <div class="section" id="footer">
            <img src="../assets/free-icon-down-arrow-2985150.png" alt="scroll down" id="arrow"/>
            <img src="../assets/free-icon-down-arrow-2985150.png" alt="scroll down" id="arrow"/>
            <img src="../assets/free-icon-down-arrow-2985150.png" alt="scroll down" id="arrow"/>
        </div>

    </div>
</template>

<script setup>

</script>

<style scoped>
#content {
    display: grid;
    grid-template-rows: 1fr 1fr 1fr;
}
.section {
    padding: 30px;
    display: flex;
    justify-content: space-around;
    align-items: center;
}
#heading {
    font-family: Arial Black, Arial, Helvetica, sans-serif;
    font-size: 42px;
}
#programs {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 5%;
}
.card {
    display: flex;
    justify-content: center;
    align-items: center;
    border: 2px solid black;
    border-radius: 15px;
    padding: 50px 10px 50px 10px;
}
.description {
    font-family: 'Times New Roman', Times, serif;
    font-size: 16px;
}
#footer {
    flex-direction: column;
}
#arrow {
    width: 40px;
    aspect-ratio: 1/1;
}
</style>