<template>
<div class=" flex  p-12   place-content-center">
  <div class="flex flex-col  ">
    <div class="flex flex-row place-content-center  py-3">
        <div class=" p-3">
          <span class="rounded-lg bg-blue-500 p-2 text-white border-blue-900 border-2">Turns : 
            <span class="rounded-md "
              :class="finished ? 'badge-success' : 'badge-light'">{{turns}}
            </span>
             </span></div>
        <div class=" p-3">
          <span class="rounded-lg bg-blue-500 p-2 text-white border-blue-900 border-2">Total Time : 
          <span class="rounded-md"
              :class="finished ? 'badge-success' : 'badge-light'">{{min}} : {{sec}}
            </span>
          </span>
        </div>
        <div class="p-3">
          <span class="rounded-lg bg-blue-500 p-2 text-white border-blue-900 border-2 cursor-pointer" @click="reset" :class="{'disable' : start}" >
              restart
          </span>
        </div>
      </div>
    <div class="flex flex-row  flex-wrap place-content-center max-w-lg  "  >
     
      <div v-for="card in memorycards" :key="card.id" class="w-12 sm:w-16 md:w-24 h-12 sm:h-16 md:h-24 mb-3 mx-2 flip-container  "
        :class="{'flipped' : card.isflipped, 'matched' : card.ismatched  }" @click="flipCard(card)">
        
        <div class="flipper   h-full w-full">
          <div class="front overflow-hidden rounded-xl border-dashed border-2  border-green-700 shadow-red-900 shadow-2xl opacity-80 hover:opacity-100">
              <img  src="./assets/images/memorycard/pattern3.jpg" class="h-full w-full">
          </div>
          <div class="back overflow-hidden rounded-xl  border-dashed border-2  border-green-700">
            <img  :src="require(`./assets/images/memorycard/${card.img}`)" class="h-full w-full">
          </div>

        </div>

       

        
      </div>
    
    </div>

  </div>
 
  
</div>
</template>

<script setup>
 
 
import { computed, onMounted, reactive, ref } from 'vue';
  

 

const cards = reactive([ {
    id: 1,
    name: 'Apple',
    img: 'apple.jpg',
    isflipped: false,
    ismatched: false
  },
  {
    id: 2,
    name: 'Banana',
    img: 'banana.jpg',
    isflipped: false,
    ismatched: false

  },
  {
    id: 3,
    name: 'Orange',
    img: 'orange.jpg',
    isflipped: false,
    ismatched: false

  },
  {
    id: 4,
    name: 'Pineapple',
    img: 'pineapple.jpg',
    isflipped: false,
    ismatched: false

  },
  {
    id: 5,
    name: 'Strawberry',
    img: 'strawberry.jpg',
    isflipped: false,
    ismatched: false

  },
  {
    id: 6,
    name: 'watermelon',
    img: 'watermelon.jpg',
    isflipped: false,
    ismatched: false

  },
] )

// double the cards
const memorycards = reactive([]);
const finished = ref(false);

const turns = ref(0)
const start = ref(false)
const totalTime = reactive({
  min: 0,
  sec: 0
})


var flippedcards = []
var interval;

function CreateMomroyCards(){
  // if(memorycards.length > 1) {
  //      memorycards.forEach(card => {
  //       card.isflipped = false;
  //       card.ismatched = false;
  //      })
  //       return
  //      }
  for( let s = 0; s < cards.length * 2 ; s++ ) {
     
       let card = {
         id: s ,
         name: cards[(s % cards.length)].name,
         img: cards[(s % cards.length)].img,
         isflipped: false,
         ismatched:false
       }
       console.log(card.id,card.isflipped)
       memorycards.push(card);
       
      
}
}





onMounted(()=> {
   
   
   memorycards.forEach(card => {
     card.isflipped = false;
      
 });
 CreateMomroyCards();
 shuffleCards();
 
})
 

 function flipCard(card){
     
      if(!start.value){
        startGame();
      }
      if(card.ismatched || card.isflipped ||flippedcards.length >= 2){
        return
      }
        
 
     card.isflipped = true;
      
     if (flippedcards.length < 2) {
       flippedcards.push(card)
     }
     if (flippedcards.length === 2) {
       matchcard(card)
     }
   

  }

 function matchcard(){
        turns.value++;
        if(flippedcards[0].name === flippedcards[1].name){
          setTimeout(() => {
            flippedcards.forEach(card => card.ismatched = true)
            flippedcards = []
              // finish game condition
            if(memorycards.every(card => card.ismatched === true)){
              clearInterval(interval)
              finished.value = true;
              start.value = false;
            }

          }, 400);
           
        } else {
            setTimeout(() => {
              flippedcards.forEach(card => card.isflipped = false)
              flippedcards = []
            }, 800);
            
        }
        
        
 }
 function shuffleCards(){
  for(let i = memorycards.length - 1; i >= 0; i--) {
        let randomIndex = Math.floor(Math.random() * memorycards.length ) ;
        
        let temp = memorycards[i];
        memorycards[i] = memorycards[randomIndex] ;
        memorycards[randomIndex] = temp;
        console.log('i: ' + i + temp.name+ ' randomindex: ' + randomIndex + memorycards[i].name)
        
      }
 }

 function startGame(){
    tick();
    //eslint-disable-next-line
    interval = setInterval(tick,1000);
    start.value = true;
}

function tick(){
    if(totalTime.sec !== 59){
         totalTime.sec++;
         return
     }

     totalTime.min++;
     totalTime.sec = 0;
}

const min = computed(()=>{
  if(totalTime.min < 10){
    return '0'+totalTime.min
  } 
   return totalTime.min
})
const sec = computed(()=>{
   if(totalTime.sec< 10){
    return '0'+ totalTime.sec;
   }
   return totalTime.sec;
})

function reset(){
  
  clearInterval(interval)
  memorycards.length = 0 ;

  setTimeout(() => {
    totalTime.min = 0;
    totalTime.sec = 0 ;
    start.value = false;
    finished.value = false;
    turns.value = 0;
    flippedcards = [];
    CreateMomroyCards();
    shuffleCards();
  }, 600);
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  
  color: #2c3e50;
 
}
</style>
