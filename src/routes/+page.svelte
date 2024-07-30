<script lang="ts">
import {emojies} from './emojies';
import { Confetti } from "svelte-confetti";
//etats  possibles du jeu
/* 
playing 
playing.matching 
paused
won lost
 */
type State='start'|'playing'|'paused'|'won'|'lost';
let state :State ='start';

let size = 20;
let grid = createGrid();
let maxMatches =  grid.length/2;
let selected :number[] = [];
let matches:string[] = [];
let timerId :number | null = null;
let time = 60;

function startGameTimer(){
    function countdown(){
        state !== 'paused' && (time -=1) ;
    }
    timerId = setInterval(countdown,1000) as unknown as number;
}
/** Création de la grille de jeu */
function createGrid(){
    let cards = new Set <string>()
        let maxSize = size / 2;

    while(cards.size<maxSize){
      const randomIndex = Math.floor(Math.random()*emojies.length);
      cards.add(emojies[randomIndex]);
    }
    return shuffle([...cards,...cards]);

}
function shuffle<Items>(array :Items[]){
return array.sort(()=>Math.random()-0.5);
}
function selectCard(cardIndex:number){
    selected = selected.concat(cardIndex);
}

function matchCards(){
    const [first,second] = selected;
    if(grid[first]===grid[second]){
        matches = matches.concat(grid[first]);
    }
    setTimeout(()=>{
        selected = [];
    },1000);
}

function resetGame(){
    timerId && clearInterval(timerId);
    grid = createGrid();
    maxMatches = grid.length/2;
    selected = [];
    matches = [];
    timerId = null;
    time = 60;
}
function gameWon(){
state = 'won';
resetGame();
}
function gameLost(){
state = 'lost'; 
resetGame();

}


function pauseGame(e:KeyboardEvent){
    if(e.key === 'Escape'){
       switch(state){
           case 'playing':
               state = 'paused';
               break;
            case 'paused':
                state = 'playing';
                break;
       }
    }
}

$:if(state=='playing'){
    !timerId && startGameTimer();
}

$:selected.length ==2 && matchCards();
$:maxMatches == matches.length && gameWon();
$:time == 0 && gameLost();
$:console.log({state ,selected,matches});


</script>
<svelte:window on:keydown={pauseGame}/>

{#if state == 'start'}
<div class="landing_screen">
    <h1 style="font-size:4rem;">EmoGuess</h1>
    
    <p>Associez les paires d'emojis</p>
    <button class="button-56" on:click={()=>state='playing'}>Jouer</button>
    <br><br>
    <span>Réalisé avec Svelte <img height="20px" alt="svelte" src="/favicon.png"> </span>
</div>
{/if}

{#if state == 'playing'}
<i class="custom-text">
    Appuyez sur 
    <kbd class="custom-kbd">
      <span class="custom-kbd-span">Esc</span> 
    </kbd>
    pour mettre en pause
</i>
<h1 class="timer" class:pulse ={time <= 10 }>{time}</h1>
<div class="matches">
    {#each matches as card}
    <div>{card}</div>
    {/each}
</div>

<div class= "cards">
    {#each grid as card,index}
    {@const isSelected = selected.includes(index) || matches.includes(card)}
    {@const isSelectedOrMatched = isSelected || matches.includes(card)}
    {@const isMatched = matches.includes(card)}     


    <button class="card"
    class:selected={isSelected}
    class:flip = {isSelectedOrMatched}
    disabled={isSelectedOrMatched}
    on:click={()=> selectCard(index)}>
    <div class="back" class:isMatched>{card}</div>

</button>
{/each}
</div>
{/if}
{#if state == 'paused'}
<div class="title_screen">
<h1>Pause</h1>
<button class="button-56" on:click={()=>state='playing'}>Reprendre</button>
<p class="custom-text">
    Ou appuyez sur
    <kbd class="custom-kbd">
      <span class="custom-kbd-span">Esc</span> 
    </kbd>
  </p>
</div>
{/if}

{#if state == 'lost'}
<div class="title_screen">
<h1>Perdu ! 💩</h1>
<button class="button-56" on:click={()=>state='playing'}>Rejouer</button>
</div>
{/if}


{#if state == 'won'}
<Confetti x={[-5, 5]} y={[0, 0.1]} delay={[500, 2000]} duration={3000} fallDistance="100vh" /> 
<div class="title_screen">

<h1>Gagné ! 🥳</h1>
<button class="button-56" on:click={()=>state='playing'}>Rejouer</button>
</div>

{/if}

<style>

    .cards{
        display:grid;
        grid-template-columns:repeat(5,1fr);
        gap:0.4rem;
    }
    .title_screen{
        display:flex;
        flex-direction:column;
        align-items:center;
        gap: 0.5em;
    }
    .landing_screen{
        display:flex;
        flex-direction:column;
        align-items:center;
        gap: 0.5em;
    }
    .card{
        height: 140px;
        width: 140px;
        border: 2px solid #111;
        border-radius: 8px;
        box-sizing: border-box;
        color: #111;
        cursor: pointer;
        font-size: 4rem;
        background-color: var(--bg-2);
        transition :rotate 0.3s ease-out;
        transform-style: preserve-3d ;

        &.selected{
            border:4px solid var(--border);
        }
        & .match {
            transition: opacity 0.3s ease-out;
            opacity: 0.4;
        }
        &.flip{
            rotate:y 180deg;
            pointer-events :none;
            border: 4px solid black;
        }
        & .back{
            position:absolute;
            inset: 0;
            display:grid;
            place-content: center;
            backface-visibility: hidden;
            rotate:y 180deg;
        }
        &:hover{
            transform: scale(1.05); /* Example hover effect: scale up */
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);  
        }

    }
    .matches{
        display:flex;
        gap:1rem;
        margin-block:2rem;
        font-size: 3rem;
        margin-top: 0;
    }
    .timer{
       transition: color 0.3 ease;
       margin-bottom: 0;
    }
    .pulse{
        color:#ff6b6b;
        animation: pulse 1s infinite ease;
    }
    @keyframes pulse {
        to{
            scale: 1.2;
        }
    }

/* CSS */

/* CSS */
.button-56 {
  align-items: center;
  background-color:#FD9745 ;
  border: 2px solid #111;
  border-radius: 8px;
  box-sizing: border-box;
  color: #111;
  cursor: pointer;
  display: flex;
  font-family: Inter,sans-serif;
  font-size: 16px;
  height: 48px;
  justify-content: center;
  line-height: 24px;
  max-width: 100%;
  padding: 0 25px;
  position: relative;
  text-align: center;
  text-decoration: none;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
}

.button-56:after {
  background-color: #111;
  border-radius: 8px;
  content: "";
  display: block;
  height: 48px;
  left: 0;
  width: 100%;
  position: absolute;
  top: -2px;
  transform: translate(8px, 8px);
  transition: transform .2s ease-out;
  z-index: -1;
}

.button-56:hover:after {
  transform: translate(0, 0);
}

.button-56:active {
  background-color: #ffdeda;
  outline: 0;
}

.button-56:hover {
  outline: 0;
}

@media (min-width: 768px) {
  .button-56 {
    padding: 0 40px;
  }
}
.custom-text {
  font-size: 0.875rem; /* text-sm */
  color: #6b7280; /* text-muted-foreground, assuming a grey color */
}

.custom-kbd {
  pointer-events: none;
  display: inline-flex;
  height: 1.25rem; /* h-5 */
  user-select: none; /* select-none */
  align-items: center; /* items-center */
  gap: 0.25rem; /* gap-1 */
  border-radius: 0.375rem; /* rounded */
  border: 1px solid #e5e7eb; /* border, assuming a light border color */
  background-color: #f3f4f6; /* bg-muted, assuming a muted background color */
  padding: 0 0.375rem; /* px-1.5 */
  font-family: monospace; /* font-mono */
  font-size: 0.625rem; /* text-[10px] */
  font-weight: 500; /* font-medium */
  color: #6b7280; /* text-muted-foreground, assuming a grey color */
  opacity: 1; /* opacity-100 */
}

.custom-kbd-span {
  font-size: 0.75rem; /* text-xs */
}

</style>