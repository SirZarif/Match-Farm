# Match-Animal-Farm

<!doctype html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Farm Animal Match - SPED Friendly</title>

<link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;700&display=swap" rel="stylesheet">
<script src="https://cdn.tailwindcss.com"></script>

<style>
  body { 
    font-family: 'Fredoka', sans-serif; 
    background: #FFF9C4; /* Cheerful Sunny Yellow Background */
    touch-action: manipulation;
  }

  .animal-card {
    background: white;
    border-radius: 30px;
    border: 8px solid #FFD54F;
    padding: 15px;
    text-align: center;
    transition: all 0.3s ease;
    box-shadow: 0 10px 0 rgba(0,0,0,0.05);
  }

  /* Vibrant Color coding for matching */
  .border-horse { border-color: #A1887F; }
  .border-chicken { border-color: #FF8A65; }
  .border-duck { border-color: #4DD0E1; }
  .border-sheep { border-color: #BA68C8; }
  .border-cow { border-color: #64B5F6; }

  .drop-zone {
    min-height: 90px;
    border: 5px dashed #BDBDBD;
    border-radius: 20px;
    margin-top: 12px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.5rem;
    font-weight: bold;
    background: #FFFDE7;
    color: #795548;
  }

  .drop-zone.correct { 
    background: #4caf50; 
    color: white; 
    border-style: solid;
    border-color: #2e7d32;
    animation: celebrate 0.5s ease;
  }

  .drop-zone.incorrect { 
    background: #f44336; 
    color: white;
    border-color: #c62828;
    animation: shake 0.4s ease;
  }

  .sound-chip {
    padding: 25px 40px;
    border-radius: 60px;
    cursor: grab;
    font-weight: 700;
    font-size: 2rem; /* Even larger text for buttons */
    box-shadow: 0 8px 0 rgba(0,0,0,0.15);
    transition: transform 0.1s;
    border: 4px solid rgba(255,255,255,0.6);
    animation: pulse 2s infinite;
  }

  .sound-chip:active { transform: translateY(6px); box-shadow: none; }

  /* Instruction Box Styling */
  .instruction-box {
    background: white;
    border: 6px solid #4FC3F7; /* Bright Blue */
    border-radius: 40px;
    padding: 30px;
    box-shadow: 0 12px 0 rgba(79, 195, 247, 0.2);
  }

  @keyframes pulse {
    0% { transform: scale(1); }
    50% { transform: scale(1.03); }
    100% { transform: scale(1); }
  }

  @keyframes celebrate {
    0% { transform: scale(1); }
    50% { transform: scale(1.15); }
    100% { transform: scale(1); }
  }

  @keyframes shake {
    0%, 100% { transform: translateX(0); }
    25% { transform: translateX(-12px); }
    75% { transform: translateX(12px); }
  }

  .btn-restart {
    background: #4CAF50;
    color: white;
    padding: 20px 50px;
    border-radius: 60px;
    font-size: 2rem;
    font-weight: bold;
    box-shadow: 0 8px 0 #2E7D32;
  }
</style>
</head>

<body class="p-4 md:p-8">

<div class="max-w-6xl mx-auto">
  <header class="text-center mb-8">
    <h1 class="text-6xl font-bold text-orange-600 mb-4 drop-shadow-sm">🚜 Farm Animal Game 🐴</h1>
    <div class="inline-block bg-white px-10 py-4 rounded-full shadow-lg border-4 border-orange-400">
       <p class="text-3xl text-orange-600 font-bold">Stars: <span id="score">0 / 5</span> ⭐</p>
    </div>
  </header>

  <div class="instruction-box mb-12">
    <p class="text-center text-4xl md:text-5xl font-bold mb-8 text-blue-600">
        🔊 Click to hear, then Drag!
    </p>
    <div id="chips" class="flex flex-wrap gap-8 justify-center"></div>
  </div>

  <div id="grid" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-5 gap-8"></div>

  <div id="win-screen" class="hidden fixed inset-0 bg-orange-500/95 flex flex-center items-center justify-center text-center p-6 z-50">
    <div class="bg-white p-12 rounded-[60px] shadow-2xl border-8 border-yellow-400">
      <h2 class="text-9xl mb-6">🏆</h2>
      <h3 class="text-5xl font-bold mb-8 text-orange-600">YOU DID IT!</h3>
      <button onclick="location.reload()" class="btn-restart transform hover:scale-110 transition">Play Again!</button>
    </div>
  </div>
</div>

<script>
const animals = [
  { id:'horse', name:'Horse', sound:'Neigh!', color:'D7CCC8', image:'https://images.unsplash.com/photo-1553284965-83fd3e82fa5a?w=400', audio:'https://cdn.pixabay.com/audio/2022/03/10/audio_c3c39634e5.mp3' },
  { id:'chicken', name:'Chicken', sound:'Cluck!', color:'FFCCBC', image:'https://images.unsplash.com/photo-1548550023-2bdb3c5beed7?w=400', audio:'https://pixabay.com/sound-effects/people-applause-75314/' },
  { id:'duck', name:'Duck', sound:'Quack!', color:'B2EBF2', image:'https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTrVG5Kj8W0Z6a97SIA54N5Fp5Vtd2GJYGQNw&s', audio:'https://www.orangefreesounds.com/wp-content/uploads/2015/04/Duck-quack-sound.mp3' },
  { id:'sheep', name:'Sheep', sound:'Baa!', color:'E1BEE7', image:'https://assets.farmsanctuary.org/content/uploads/2025/05/15062015/2024_04-19_FSNY_Margaretta_lamb_LH_4565-scaled.jpg', audio:'https://freeanimalsounds.org/wp-content/uploads/2017/07/schafe.mp3' },
  { id:'cow', name:'Cow', sound:'Moo!', color:'BBDEFB', image:'https://upload.wikimedia.org/wikipedia/commons/b/b8/Holstein_Cow_in_Mont%C3%A9r%C3%A9gie%2C_Quebec.jpg', audio:'https://www.orangefreesounds.com/wp-content/uploads/2014/05/Cow-moo-sound.mp3' }
];

let dragged = null;
let score = 0;

function speak(text){
  const msg = new SpeechSynthesisUtterance(text);
  msg.rate = 0.8; 
  speechSynthesis.cancel();
  speechSynthesis.speak(msg);
}

function init(){
  const chipBox = document.getElementById('chips');
  const grid = document.getElementById('grid');
  
  [...animals].sort(()=>Math.random()-0.5).forEach(a=>{
    const chip = document.createElement('div');
    chip.className = `sound-chip`;
    chip.style.backgroundColor = `#${a.color}`;
    chip.innerText = a.sound;
    chip.draggable = true;
    
    chip.ondragstart = () => { dragged = a.id; chip.classList.add('dragging'); };
    chip.ondragend = () => chip.classList.remove('dragging');
    
    chip.onclick = () => {
        new Audio(a.audio).play();
        speak(a.sound);
    };

    chipBox.appendChild(chip);
  });

  [...animals].sort(()=>Math.random()-0.5).forEach(a=>{
    const card = document.createElement('div');
    card.className = `animal-card border-${a.id}`;
    card.innerHTML = `
      <div class="h-48 mb-4 overflow-hidden rounded-3xl bg-gray-100 flex items-center justify-center border-4 border-gray-50">
        <img src="${a.image}" class="w-full h-full object-cover">
      </div>
      <p class="text-4xl font-bold text-gray-800 mb-2">${a.name}</p>
      <div class="drop-zone" id="drop-${a.id}">Drag Here</div>
    `;

    const zone = card.querySelector('.drop-zone');
    zone.ondragover = e => e.preventDefault();
    zone.ondrop = () => handleDrop(a.id);
    
    grid.appendChild(card);
  });
  
  speak("Let's play! Click a sound, then drag it to the animal.");
}

function handleDrop(target){
  if(!dragged) return;

  const zone = document.getElementById(`drop-${target}`);
  const animal = animals.find(x => x.id === target);

  if(target === dragged){
    zone.innerHTML = "⭐GOOD!⭐";
    zone.classList.add('correct');
    new Audio(animal.audio).play();
    speak(`Good! This is a ${animal.name}.`);
    
    score++;
    document.getElementById('score').innerText = `${score} / 5`;
    
    const chips = document.querySelectorAll('.sound-chip');
    chips.forEach(c => { if(c.innerText === animal.sound) c.style.display = 'none'; });

    if(score === 5) {
      setTimeout(() => {
        document.getElementById('win-screen').style.display = 'flex';
        speak("You did a wonderful job! You matched them all!");
      }, 1000);
    }
  } else {
    zone.innerHTML = "❌ Try Again";
    zone.classList.add('incorrect');
    speak("Not that one. Let's try again!");
    setTimeout(() => {
      zone.innerHTML = "Drag Here";
      zone.classList.remove('incorrect');
    }, 1200);
  }
  dragged = null;
}

init();
</script>
</body>
</html>
