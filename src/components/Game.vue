<template>
    <div class="game">
        <h1>Simon The Game</h1>
        <div class="game__content">
            <div class="game__item game__item_1"
                 @click="clicked(1)"
                 :class="{lit: isLit[1]}"></div>
            <div class="game__item game__item_2"
                 @click="clicked(2)"
                 :class="{lit: isLit[2]}"></div>
            <div class="game__item game__item_3"
                 @click="clicked(3)"
                 :class="{lit: isLit[3]}"></div>
            <div class="game__item game__item_4"
                 @click="clicked(4)"
                 :class="{lit: isLit[4]}"></div>
        </div>
        <div class="game__start-btn" @click="startGame">{{ centerButton }}
            <span v-show="playing">{{ showScore }}</span>
        </div>
        <div class="game__mode">
            <p>Уровень сложности</p>
            <button :class="{active: activeBtn === 'btn1'}"
                    @click="gameMode = 'easy', activeBtn = 'btn1'">Легкий</button>
            <button :class="{active: activeBtn === 'btn2'}"
                    @click="gameMode = 'normal', activeBtn = 'btn2'">Средний</button>
            <button :class="{active: activeBtn === 'btn3'}"
                    @click="gameMode = 'hard', activeBtn = 'btn3'">Сложный</button>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'SimonGame',
        data() {
            return {
                centerButton: "СТАРТ",
                playing: false,
                isClickable: false,
                isWon: false,
                isWrong: false,
                gameMode: 'normal',
                activeBtn: 'btn2',
                score: 0,
                sequence: [],
                sequenceInterval: null,
                playerSequence: [],
                sounds: {
                    1: "sounds/plip.mp3",
                    2: "sounds/govilive.mp3",
                    3: "sounds/error.mp3",
                    4: "sounds/sswitch1.mp3",
                    5: "sounds/haa.mp3",
                    6: "sounds/good.mp3",
                },
                isLit: {
                    1: false,
                    2: false,
                    3: false,
                    4: false
                }
            }
        },
        computed: {
            showScore() {
                if (this.isWon) {
                    return "Еще раз?";
                }
                return "Очко: " + this.score;
            }
        },
        methods: {
            playSound(tile) {
                if(this.sounds[tile]) {
                    var audio = new Audio(this.sounds[tile]);
                    audio.play();
                }
            },
            startGame() {
                this.playing = true;
                this.sequence = [];
                this.playSound(5);
                this.playerSequence = [];
                this.centerButton = "Заново";
                this.isWon = false;
                this.isWrong = false;
                this.score = 0;
                clearInterval(this.sequenceInterval);
                this.showSequence();
            },
            clicked(tile) {
                if (this.isClickable) {
                    this.playSound(tile);
                    this.lightUp(tile);
                    this.playerSequence.push(tile);
                    this.checkWinLose();
                }
            },
            checkWinLose() {
                for (let i = 0; i < this.playerSequence.length; i++) {
                    if (this.playerSequence[i] !== this.sequence[i]) {
                        this.playerSequence = [];
                        this.centerButton = "Не правильно!";
                        this.isWrong = true;
                        setTimeout(() => {
                            this.centerButton = "Заново";
                            this.isWrong = false;
                        }, 1000);
                        this.showSequence(true);
                    }
                }
                if (this.playerSequence.length === this.sequence.length) {
                    this.playerSequence = [];
                    this.score++;
                    if (this.score === 5) {
                        this.centerButton = "Победа!";
                        setTimeout(() => this.playSound(6), 600);
                        this.isClickable = false;
                        this.isWon = true;
                    } else {
                        this.showSequence();
                    }
                }
            },
            lightUp(tile) {
                this.isLit[tile] = true;
                setTimeout(() => {
                    this.isLit[tile] = false;
                }, 300);
            },
            showSequence(redo) {
                let currentIndex = 0;
                let speed = this.sequence.length === 0 ? 1000 : this.getModeSpeed(this.gameMode);
                this.isClickable = false;
                if (!redo) {
                    this.sequence.push(Math.floor(Math.random() * 4 + 1));
                }
                this.sequenceInterval = setInterval(() => {
                    if (currentIndex >= this.sequence.length) {
                        clearInterval(this.sequenceInterval);
                        return (this.isClickable = true);
                    }
                    this.playSound(this.sequence[currentIndex]);
                    this.lightUp(this.sequence[currentIndex]);
                    currentIndex++;
                }, speed);
            },
            getModeSpeed(mode) {
                if (mode === 'easy') {
                    return 1500;
                }
                else if (mode === 'normal') {
                    return 1000;
                }
                else {
                    return 400;
                }
            }
        }
    }
</script>

<style lang="scss" scoped>
    $red: #aa2525;
    $yellow: #aaaa25;
    $green: #45aa25;
    $blue: #2525aa;

    .game {
        display: flex;
        flex-direction: column;
        align-items: center;
        .game__content {
            background-color: rgb(63, 56, 56);
            width: 300px;
            height: 300px;
            border-radius: 50%;
            display: grid;
            grid-template-columns: 1fr 1fr;
            grid-template-rows: 1fr 1fr;
            .game__item {
                cursor: pointer;
                border: 3px solid rgb(63, 56, 56);
                &:hover {
                    opacity: 0.6;
                }
            }
            .game__item_1 {
                background-color: $blue;
                border-top-left-radius: 100%;
                &.lit {
                    background: lighten($blue, 25%);
                }
            }
            .game__item_2 {
                background-color: $red;
                border-top-right-radius: 100%;
                &.lit {
                    background: lighten($red, 25%);
                }
            }
            .game__item_3 {
                background-color: $yellow;
                border-bottom-left-radius: 100%;
                &.lit {
                    background: lighten($yellow, 25%);
                }
            }
            .game__item_4 {
                background-color: $green;
                border-bottom-right-radius: 100%;
                &.lit {
                    background: lighten($green, 25%);
                }
            }
        }

        .game__start-btn {
            background-color: #c5bbbb;
            width: fit-content;
            padding: 15px;
            border-radius: 10px;
            margin-top: 20px;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
            &:hover {
                background-color: #b8aeae;
            }
        }

        .game__mode {
            background-color: #394432;
            padding: 20px;
            margin-top: 20px;
            border-radius: 5px;
            p {
                text-align: center;
                color: #f2f2f2;
                font-size: 18px;
                margin-top: 0;
            }
            button {
                cursor: pointer;
                background-color: rgb(177, 219, 196);
                font-size: 14px;
                letter-spacing: 1px;
                padding: 7px;
                &.active {
                    background: $green;
                }
            }
            button + button {
                margin-left: 10px;
            }
        }
    }
</style>
