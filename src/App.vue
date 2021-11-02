<template>
<div>

    <div class="intro"><img src="https://wallpapercave.com/wp/wp6633182.jpg" alt=""></div>

    <header class="py-2">
        <nav class="container d-flex justify-content-between">
            <a class="navbar-brand text-danger" href="/"><img width="100" src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/08/TeenWolfLogo.png/280px-TeenWolfLogo.png" alt=""></a>
            <span class="btn btn-success d-flex align-items-center justify-content-center" @click="startGame()">🎮 Start Game</span>
        </nav>
    </header>

    <main class="text-center">
        <div @click="show_alert = false" v-if="show_alert" class="overlay-alert">
            <div class="message alert m-0 alert-success show_alert">
                <p>
                    Congratulazioni! Hai trovato tutte le coppie 🎉<br>
                    Sei un vero fan di Teen Wolf 🐺 <br>
                </p>
                <div class="fw-bold alert m-0 alert-info">➡ Primi su 
                    <span class="btn btn-success" @click="startGame()">🎮 Start Game</span>
                    per giocare ancora ⬅
                </div>
                <span class="text-danger">fai click fuori da questa allerta per chiuderla</span>
            </div>
        </div>

        <div class="cards d-flex justify-content-center flex-wrap">
            <div class="flip-card m-3"
                @click="selectCards(card, card.id, index)"
                v-for="(card, index) in random_cards" :key="card.index">
                <div class="flip-card-inner" :class="selected_cards.includes(index) || card.isGot ? 'rotate' : ''">
                    <div class="flip-card-front">
                        <img class="rounded" src="https://i.pinimg.com/originals/60/0e/13/600e1317548e1716913d8460757dbc02.jpg" alt="">
                    </div>

                    <div v-if="!hide_cards" class="flip-card-back rounded">
                        <img class="path rounded-top" :src="card.path" alt="">
                        <span class="name text-white rounded-bottom" :class="card.isGot ? 'bg-success' : 'bg-primary'">{{card.name}}</span>
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
                {name: 'Stiles Stilinski', path: 'https://assets.puzzlefactory.pl/puzzle/223/245/original.jpg', id: 1, isGot: false},
                {name: 'Scott McCall', path: 'https://i.pinimg.com/550x/1c/33/2b/1c332bcd0a305721ff410352429bf317.jpg', id: 2, isGot: false},
                {name: 'Derek Hale', path: 'http://data.pixiz.com/output/user/frame/preview/api/big/4/4/7/1/2621744_e6616.jpg', id: 3, isGot: false},
                {name: 'Allison Argent', path: 'https://i.pinimg.com/474x/b1/2b/df/b12bdf82fcd7a6c8088193c289f2495f--allison-argent-hair-alison-argent.jpg', id: 4, isGot: false},
                //MEDIO
                {name: 'Lydia Martin', path: 'https://i.pinimg.com/736x/f4/f4/fd/f4f4fda5b2e4940d8c03a33c46b8b661.jpg', id: 5, isGot: false},
                {name: 'Jackson Whittemore', path: 'https://pbs.twimg.com/profile_images/1289549908790448128/PkASyI5H.jpg', id: 6, isGot: false},
                {name: 'Jordan Parrish', path: 'https://i.pinimg.com/564x/d7/6e/a0/d76ea043b6fe4d186fca8a8a6d35ed82.jpg', id: 7, isGot: false},
                {name: 'Alan Deaton', path: 'https://i.pinimg.com/236x/e4/1a/32/e41a326218934a42f7dfc60d2d1caf3d.jpg', id: 8, isGot: false},
                //DIFFICILE
                {name: 'Sceriffo Stilinski', path: 'https://i.pinimg.com/236x/d3/e0/a0/d3e0a089ba9a2c651d408252c12ef315.jpg', id: 9, isGot: false},
                {name: 'Chris Argent', path: 'https://i.pinimg.com/564x/c2/83/99/c28399e5be1a265a275127e70f068e39.jpg', id: 10, isGot: false},
                {name: 'Melissa McCall', path: 'https://i.pinimg.com/236x/72/4b/3f/724b3f1d29346fa4181b4c7da174f897.jpg', id: 11, isGot: false},
                {name: 'Liam Dunbar', path: 'https://i.pinimg.com/550x/83/d6/42/83d642c9e3c655e78e715b44d8be42d0.jpg', id: 12, isGot: false},

                // EXTRA, non entrano nello schermo
                // {name: 'Theo Raeken', path: 'https://i.pinimg.com/564x/b5/25/8f/b5258f28592ef6779b011b983b2626b3.jpg', id: 13, isGot: false},
                // {name: 'Malia Tate', path: 'https://i.pinimg.com/originals/f4/77/04/f47704c4ccaa7ac83d51f0694b6586f0.jpg', id: 14, isGot: false},
                // {name: 'Peter Hale', path: 'https://i.pinimg.com/564x/07/0a/87/070a879c7b75fd42112c45f782018e1e.jpg', id: 15, isGot: false},
                // {name: 'Kira Yukimura', path: 'https://i.pinimg.com/564x/18/de/60/18de603d86316d16ea6df9332127a9ba.jpg', id: 16, isGot: false},
                // {name: 'Bobby Finstock', path: 'https://i.pinimg.com/236x/45/05/36/4505369a6881ff02a04193ce6b6ffaac.jpg', id: 17, isGot: false},
                // {name: 'Isaac Lahey', path: 'https://i.pinimg.com/474x/48/19/20/481920eacad5da80ff79b060024f40ad--werewolf-art-amber-eyes.jpg', id: 18, isGot: false},



            ],
            random_cards: [],
            compared_cards: [],
            selected_cards: [],
            show_alert: false,
            hide_cards: false,
        } 
    },
    methods: {
        resetCards(){
            this.compared_cards = []; 
            this.selected_cards = [];
        },
        checkGame(){
            let i = 0;
            this.originalCards.forEach(card => {
                if (card.isGot) {
                    i++;
                }
            });
            if (i == this.originalCards.length) {
                this.show_alert = true;
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
            this.originalCards.forEach(element => {
                deck1.push(element);
                deck2.push(element);
            });
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
        startGame(){
            this.hide_cards = true;
            this.show_alert = false;
            this.random_cards = [],
            this.originalCards.forEach(card => {
                card.isGot = false;
            });
            this.resetCards();
            this.randomCards(this.duplicateCards());
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
.cards{
    min-width: 510px;
    min-height: 800px;
    height: calc(100vh - 67.42px);
    align-items: center;
    align-content: center;
    overflow: hidden;
    background-image: url('https://wallpaperaccess.com/full/125181.jpg');
    background-repeat: no-repeat;
    background-size: cover;
}

// FLIP CARD
.flip-card {
    // default
    width: 160px;
    height: 230px;

    @media screen and (max-width: 1600px) {
        width: 140px;
        height: 210px;
    }
    @media screen and (max-width: 1400px) {
        width: 120px;
        height: 180px;
    }
    @media screen and (max-width: 1250px) {
        width: 100px;
        height: 130px;
    }
    @media screen and (max-width: 800px) {
        width: 90px;
        height: 110px;
    }
    @media screen and (max-width: 610px) {
        width: 90px;
        height: 90px;
    }


    // small
    // width: 160px;
    // height: 240px;



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
    background-color: white;
    transform: rotateY(180deg);

    .path{
        height: 90%;
        object-fit: cover;
        @media screen and (max-width: 1400px) {
            height: 100%;
            border-bottom-left-radius: 5px;
            border-bottom-right-radius: 3px;
        }
    }
    .name{
        height: 10%;
        font-size: .9rem;
        display: flex;
        align-items: center;
        justify-content: center;

        @media screen and (max-width: 1400px) {
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
.show_alert{
    animation: show_alert .8s;
    @keyframes show_alert {
        from{opacity: 0; transform: scale(.7);}
    }
}

//INTRO EFFECT
.intro img{
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 9999;
    visibility: hidden;

    animation: intro 2s ease-in;
    @keyframes intro {
        60%{opacity: 1; visibility: visible;}
        to{opacity: 0;}
    }
}
</style>