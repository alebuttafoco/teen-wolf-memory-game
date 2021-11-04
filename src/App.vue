<template>
<div>
    <!-- ---------------------------------------------------- -->
    <!-- HEADER -->
    <!-- ---------------------------------------------------- -->
    <header class="px-3">
        <nav class="d-flex justify-content-between align-items-center row">
            <a class="navbar-brand text-danger col-1" href="/"><img width="100" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/08/TeenWolfLogo.png/280px-TeenWolfLogo.png" alt=""></a>
            <!-- timer -->
            <div class="col text-center">
                <div @click="isActive_timer = !isActive_timer" class="timer btn" :class="isActive_timer?'btn-danger':'btn-secondary'">
                    {{formattedTimer()}}
                    <!-- info on timer hover -->
                    <div class="timer_info alert alert-light">Fai click per mettere ON/OFF il timer</div>
                </div>
            </div>
             <div class="d-flex align-items-center col-1 justify-content-end">
                <!-- scegli difficolta' -->
                <div class="btn">
                    <span class="btn btn-primary dropdown-toggle" id="dropdownDifficult" data-bs-toggle="dropdown">
                        <span class="d-none d-md-inline">Scegli difficolta'</span> 
                        <i class="d-md-none fas fa-cogs"></i>
                    </span>
                    <ul class="dropdown-menu text-center" aria-labelledby="dropdownDifficult">
                        <li><span @click="difficult = 6, startGame()" class="dropdown-item">Facile</span></li>
                        <li><span @click="difficult = 12, startGame()" class="dropdown-item">Media</span></li>
                        <li><span @click="difficult = 18, startGame()" class="dropdown-item">Difficile</span></li>
                    </ul>
                </div>
                <!-- restart game -->
                <div class="btn btn-success d-flex align-items-center justify-content-center" @click="startGame()">🎮<span class="ms-1 d-none d-lg-block">Restart</span></div>
            </div>
        </nav>
    </header>
    <!-- ---------------------------------------------------- -->
    <!-- MAIN -->
    <!-- ---------------------------------------------------- -->
    <main class="text-center">
        <!-- progression bar timer -->
        <div class="progress-bar rounded bg-dark">
            <div class="percentage rounded" :class="colorProgressBar()" :style="{width: progress_bar + '%'}"></div>
        </div>
        <!-- alert win -->
        <div @click="alert_game_win = false" v-if="alert_game_win" class="overlay-alert">
            <div class="message alert m-0 alert-success alert_game_win">
                <p>
                    Congratulazioni! Hai trovato tutte le coppie 🎉<br>
                    Sei un vero fan di Teen Wolf 🐺 <br>
                </p>
                <div class="border border-dark">
                    Il tuo tempo e' stato di <br> 
                    <span class="fs-3">{{result_time}}</span>
                </div>
                <div class="fw-bold alert">➡ Primi su 
                    <span class="btn btn-success" @click="startGame()">🎮 Restart Game</span>
                    per giocare ancora ⬅
                </div>
                <span class="text-danger">oppure fai click fuori da questa allerta per chiuderla</span>
            </div>
        </div>
        <!-- alert game over -->
        <div @click="alert_game_over = false" v-if="alert_game_over" class="overlay-alert">
            <div class="message alert m-0 alert-danger alert_game_win">
                <p>
                    Game Over 😭<br>
                    Non sei riuscito a completare il gioco in tempo <br>
                    Forse non sei un grande fan di Teen Wolf 🐺🙄 <br>
                </p>
                <div class="fw-bold alert m-0">➡ Primi su 
                    <span class="btn btn-success" @click="startGame()">🎮 Restart Game</span>
                    per riprovare ⬅
                </div>
                <span class="text-danger">oppure fai click fuori da questa allerta per chiuderla</span>
            </div>
        </div>
        <!-- game cards -->
        <div class="cards container-card">
            <div class="flip-card" :class="layoutCards()"
                @click="selectCards(card, card.id, index)"
                v-for="(card, index) in random_cards" :key="card.index">
                <div class="flip-card-inner" :class="selected_cards.includes(index) || card.isGot ? 'rotate' : ''">
                    <div class="flip-card-front">
                        <img class="rounded" src="https://i.pinimg.com/originals/60/0e/13/600e1317548e1716913d8460757dbc02.jpg" alt="">
                    </div>

                    <div v-if="!hide_cards" class="flip-card-back rounded" :class="card.isGot ? 'bg-success' : 'bg-dark'">
                        <img class="path rounded-top" :src="card.path" alt="">
                        <span class="name text-white rounded-bottom" :class="card.isGot ? 'bg-success' : 'bg-dark'">{{card.name}}</span>
                    </div>

                    <div v-else class="flip-card-back rounded bg-dark"></div>
                </div>
            </div>
        </div>
    </main>
</div>
</template>

<script>
export default {
    data(){
        return {
            originalCards : [
                //FACILE
                {name: 'Stiles Stilinski', path: 'https://i.pinimg.com/564x/21/49/44/2149448e805e048c7090b1815fc2ad7c.jpg', id: 1, isGot: false},
                {name: 'Scott McCall', path: 'https://i.pinimg.com/564x/42/1c/27/421c2705befcff5b6996b6c8c8c802ea.jpg', id: 2, isGot: false},
                {name: 'Derek Hale', path: 'https://i.pinimg.com/564x/5e/48/1a/5e481a3bc83cf733c818b5e4bdb032de.jpg', id: 3, isGot: false},
                {name: 'Allison Argent', path: 'https://i.pinimg.com/474x/b1/2b/df/b12bdf82fcd7a6c8088193c289f2495f--allison-argent-hair-alison-argent.jpg', id: 4, isGot: false},
                {name: 'Lydia Martin', path: 'https://i.pinimg.com/736x/f4/f4/fd/f4f4fda5b2e4940d8c03a33c46b8b661.jpg', id: 5, isGot: false},
                {name: 'Jackson Whittemore', path: 'https://pbs.twimg.com/profile_images/1289549908790448128/PkASyI5H.jpg', id: 6, isGot: false},
                //MEDIO
                {name: 'Jordan Parrish', path: 'https://i.pinimg.com/564x/d7/6e/a0/d76ea043b6fe4d186fca8a8a6d35ed82.jpg', id: 7, isGot: false},
                {name: 'Alan Deaton', path: 'https://i.pinimg.com/236x/e4/1a/32/e41a326218934a42f7dfc60d2d1caf3d.jpg', id: 8, isGot: false},
                {name: 'Sceriffo Stilinski', path: 'https://i.pinimg.com/236x/d3/e0/a0/d3e0a089ba9a2c651d408252c12ef315.jpg', id: 9, isGot: false},
                {name: 'Chris Argent', path: 'https://i.pinimg.com/564x/c2/83/99/c28399e5be1a265a275127e70f068e39.jpg', id: 10, isGot: false},
                {name: 'Melissa McCall', path: 'https://i.pinimg.com/236x/72/4b/3f/724b3f1d29346fa4181b4c7da174f897.jpg', id: 11, isGot: false},
                {name: 'Liam Dunbar', path: 'https://i.pinimg.com/550x/83/d6/42/83d642c9e3c655e78e715b44d8be42d0.jpg', id: 12, isGot: false},
                //DIFFICILE
                {name: 'Theo Raeken', path: 'https://i.pinimg.com/564x/b5/25/8f/b5258f28592ef6779b011b983b2626b3.jpg', id: 13, isGot: false},
                {name: 'Malia Tate', path: 'https://i.pinimg.com/originals/f4/77/04/f47704c4ccaa7ac83d51f0694b6586f0.jpg', id: 14, isGot: false},
                {name: 'Peter Hale', path: 'https://i.pinimg.com/564x/07/0a/87/070a879c7b75fd42112c45f782018e1e.jpg', id: 15, isGot: false},
                {name: 'Kira Yukimura', path: 'https://i.pinimg.com/564x/18/de/60/18de603d86316d16ea6df9332127a9ba.jpg', id: 16, isGot: false},
                {name: 'Bobby Finstock', path: 'https://i.pinimg.com/236x/45/05/36/4505369a6881ff02a04193ce6b6ffaac.jpg', id: 17, isGot: false},
                {name: 'Isaac Lahey', path: 'https://i.pinimg.com/474x/48/19/20/481920eacad5da80ff79b060024f40ad--werewolf-art-amber-eyes.jpg', id: 18, isGot: false},
            ],
            random_cards: [],
            compared_cards: [],
            selected_cards: [],
            alert_game_win: false,
            hide_cards: false,
            difficult: 6,
            timer : null,
            progress_bar: 100,
            minutes: null,
            seconds: null,
            result_time: null,
            timer_prevent: false,
            interval : null,
            alert_game_over: false,
            isActive_timer: true,
        } 
    },
    computed: {
        activeTimer(){
            return this.timer;
        },
    },
    watch: {
        activeTimer(time){
            this.progress_bar = ((time / 150) * 100).toFixed(2);
            if (time == 0) {
                clearInterval(this.interval);
                this.alert_game_over = true;
            }
        },
    },
    methods: {
        //reset
        resetAlerts(){
            this.alert_game_win = false;
            this.alert_game_over = false;
        },
        resetTimer(){
            this.alert_game_over = false;
            this.timer_prevent = false;
            clearInterval(this.interval);
            this.timer = 150;
        },
        resetCards(){
            this.compared_cards = []; 
            this.selected_cards = [];
        },  
        //game methods
        colorProgressBar(){
            if (this.progress_bar > 40) {
                return 'bg-primary';
            } else if (this.progress_bar > 10){
                return 'bg-warning';
            } else {
                return 'bg-danger';
            }
        },
        formattedTimer(){
            this.minutes = Math.floor(this.timer / 60);
            this.seconds = this.timer % 60;
            let min = this.minutes;
            let sec = this.seconds;
            if (sec < 10) {sec = `0${sec}`;}
            if (min < 10) {min = `0${min}`;}
            return `${min} : ${sec}`;
        },
        layoutCards(){
            if (this.difficult == 6) {
                return 'flip-card-easy animation_cards_easy';
            }
            else if (this.difficult == 12){
                return 'flip-card-mid animation_cards_mid';
            } else if(this.difficult == 18){
                return 'flip-card-diff animation_cards_diff';
            }
        },
        checkGame(){
            let i = 0;
            this.originalCards.forEach(card => {
                if (card.isGot) {
                    i++;
                }
            });
            if (i == this.difficult) {
                clearInterval(this.interval);
                let result_sec = 150 - ((this.minutes * 60) + this.seconds);
                let min = Math.floor(result_sec / 60);
                let sec = result_sec % 60;
                if (sec < 10) {sec = `0${sec}`;}
                if (min < 10) {min = `0${min}`;}
                this.result_time = `${min} : ${sec}`;
                this.alert_game_win = true;
            } else {
                i = 0;
            }
        },
        checkCards(){
            let card1, card2;
            this.compared_cards.forEach((card, index) => {
                if(index == 0){card1 = card;} 
                if(index == 1) {card2 = card;}
            })
            if (this.compared_cards.length == 2) {
                if(card1 == card2){
                    this.originalCards.forEach(card =>{
                        if (card.id == card1) {
                            card.isGot = true;
                        }
                    })
                    this.resetCards();
                    
                } else {
                    setTimeout(() => {
                        this.resetCards();
                    }, 1000);
                }
            }
        },
        selectCards(card, id, index){
            if (this.isActive_timer) {
                this.startTimer();
            }
            if (!card.isGot) {
                if(this.compared_cards.length < 2){
                    if (!this.selected_cards.includes(index)) {
                        this.compared_cards.push(id);
                        this.selected_cards.push(index);
                    }
                }
                this.checkCards();
                this.checkGame();
            }
        },
        duplicateCards(){
            let deck1 = [];
            let deck2 = [];
            for (let index = 0; index < this.difficult; index++) {
                deck1.push(this.originalCards[index]);
                deck2.push(this.originalCards[index]);
            }
            return this.random_cards = deck1.concat(deck2);
        },
        randomCards(array){
            let currentIndex = array.length,  randomIndex;
            while (currentIndex != 0) {
                randomIndex = Math.floor(Math.random() * currentIndex);
                currentIndex--;
                [array[currentIndex], array[randomIndex]] = [array[randomIndex], array[currentIndex]];
            }
            return array;
        },
        //start
        startTimer(){
            if (!this.timer_prevent) {
                this.timer_prevent = true;
                this.interval = setInterval(() => {
                    this.timer--;
                }, 1000);
            }
        },
        startGame(){
            this.hide_cards = true;
            this.resetTimer();
            this.resetAlerts();
            this.resetCards();
            this.random_cards = [],
            this.originalCards.forEach(card => {
                card.isGot = false;
            });
            this.randomCards(this.duplicateCards());
            //rende le carte di nuovo visibili
            setTimeout(() => {
                this.hide_cards = false;
            }, 500);
        }
    },
    mounted(){
        this.startGame();
    }
}
</script>

<style lang='scss'>

*{
    box-sizing: border-box;
    padding: 0;
    margin: 0;
    font-family: 'Lato', sans-serif;
}
header {
    background-color: rgb(0, 0, 0);
}
main {
    overflow: hidden;
    background-image: url('https://wallpaperaccess.com/full/125181.jpg');
    background-repeat: no-repeat;
    background-size: cover;
}
.cards{
    margin: auto;
    height: calc(100vh - 60px);
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    justify-content: center;
    align-content: center;
}
.container-card{
    max-width: 1500px;
}
// animation cards (bad solution)
.animation_cards_easy {
    animation: easy 1s;
    @keyframes easy {
        from{opacity: 0; transform: scale(.8);}
        25%{opacity: 0; transform: scale(.8);}
    }
}
.animation_cards_mid {
    animation: mid 1s;
    @keyframes mid {
        from{opacity: 0; transform: scale(.8);}
        25%{opacity: 0; transform: scale(.9);}
    }
}
.animation_cards_diff {
    animation: diff 1s;
    @keyframes diff {
        from{opacity: 0; transform: scale(.8);}
        25%{opacity: 0; transform: scale(.9);}
    }
}
// FLIP CARD
.flip-card-easy{
    margin: .5rem 1rem;
    width: calc(100% / 6 - 2rem);
    max-width: 220px;
    aspect-ratio: 9 / 14;
    @media screen and (max-width: 1200px) {
        margin: .5rem;
        width: calc(100% / 6 - 1rem);
    }
    @media screen and (max-width: 950px) {
        margin: .5rem;
        width: calc(100% / 4 - 1rem);
        max-width: 150px;
    }
    @media screen and (max-width: 510px) {
        width: calc(100% / 3 - 1rem);
        aspect-ratio: 9 / 12;
    }
    @media screen and (max-width: 410px) {
        width: calc(100% / 2 - 1rem);
        max-width: 120px;
        aspect-ratio: 1 / 1;
    }
}
.flip-card-mid{
    margin: .5rem 1rem;
    width: calc(100% / 8 - 2rem);
    max-width: 170px;
    aspect-ratio: 9 / 14;
    @media screen and (max-width: 1300px) {
        margin: .5rem;
    }
    @media screen and (max-width: 1000px) {
        width: calc(100% / 6 - 1rem);
        max-width: 120px;
    }
    @media screen and (max-width: 600px) {
        width: calc(100% / 4 - 1rem);
        aspect-ratio: 1 / 1;
    }
    @media screen and (max-width: 410px) {
        width: calc(100% / 3 - 1rem);
        max-width: 85px;
    }
}
.flip-card-diff{
    margin: .5rem 1rem;
    width: calc(100% / 9 - 2rem);
    max-width: 130px;
    aspect-ratio: 9 / 13;
    @media screen and (max-width: 1300px) {
        margin: .5rem;
        width: calc(100% / 9 - 1rem);
    }
    @media screen and (max-width: 1000px) {
        width: calc(100% / 8 - 1rem);
        aspect-ratio: 9 / 11;
    }
    @media screen and (max-width: 900px) {
        width: calc(100% / 6 - 1rem);
        max-width: 120px;
        aspect-ratio: 1 / 1;
    }
    @media screen and (max-width: 600px) {
        width: calc(100% / 4 - 1rem);
        aspect-ratio: 1.7 / 1;
    }
}
.flip-card {
    cursor: pointer;
    perspective: 1000px;
    transition: .3s;

    &:hover{
        transform: translateY(-5px);
        box-shadow: 1px 5px 10px rgba(0, 0, 0, 0.308);
    }
}
.flip-card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  transition: transform 0.6s;
  transform-style: preserve-3d;
}
.flip-card-front img {
    width: 100%;
    height: 100%;
    object-fit: cover;
}
.flip-card-front, .flip-card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
}
.flip-card-back {
    padding: 2px;
    display: flex;
    flex-direction: column;
    transform: rotateY(180deg);

    .path{
        height: 90%;
        object-fit: cover;
        @media screen and (max-width: 1200px) {
            height: 100%;
            border-bottom-left-radius: 5px;
            border-bottom-right-radius: 3px;
        }
    }
    .name{
        height: 10%;
        font-size: .7vw;
        display: flex;
        align-items: center;
        justify-content: center;

        @media screen and (max-width: 1200px) {
            display: none;
        }
    }

}
.rotate {
  transform: rotateY(180deg);
}

// ALERT
.overlay-alert{
    width: 100%;
    height: 100%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    background-color: rgba(0, 0, 0, 0.589);
    z-index: 9999;
    display: flex;
    justify-content: center;
    align-items: center;
}
.alert_game_win{
    animation: alert_game_win .8s;
    @keyframes alert_game_win {
        from{opacity: 0; transform: scale(.7);}
    }
}

// timer
.timer_info{
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
    font-size: .7rem;
    opacity: 0;
    transition: .3s ease-in;
    z-index: 99999;
}
.timer:hover .timer_info{
    opacity: 1;
}
.progress-bar {
    width: 99%;
    margin: auto;
    height: 10px;
    .percentage{
        height: inherit;
        transition: 2s;
    }

}
</style>