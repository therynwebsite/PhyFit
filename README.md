# PhyFit
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>PhyFit Level System</title>

<style>
body {
    margin: 0;
    font-family: Arial, sans-serif;
}

/* SCREEN SYSTEM */
.screen {
    display: none;
    height: 100vh;
    width: 100%;
    position: relative;
    justify-content: center;
    align-items: center;
    background-size: cover;
    background-position: center;
}

.screen.active {
    display: flex;
}

/* DARK OVERLAY */
.screen::before {
    content: "";
    position: absolute;
    inset: 0;
    background: rgba(0,0,0,0.55);
}

/* CONTENT */
.content {
    position: relative;
    text-align: center;
    color: white;
}

/* HOME */
#home {
    background-image: url("https://images.unsplash.com/photo-1506744038136-46273834b3fb");
}

/* PLAN BACKGROUND */
.plan {
    background-image: url("https://images.unsplash.com/photo-1554284126-aa88f22d8b74");
}

/* TITLE */
h1 {
    font-size: 80px;
    color: red;
    margin: 0;
}

/* BUTTON ROW */
.btn-row {
    display: flex;
    gap: 25px;
    justify-content: center;
    margin-top: 20px;
}

/* HOME BUTTONS */
.home-btn {
    width: 160px;
    height: 210px;
    background: rgba(255,0,0,0.78);
    border: 1px solid rgba(255,255,255,0.25);
    color: white;
    font-size: 24px;
    font-weight: bold;
    border-radius: 18px;
    cursor: pointer;
}

/* BOX */
.box {
    background: rgba(255,0,0,0.85);
    padding: 20px;
    border-radius: 15px;
    width: 420px;
}

/* INPUTS */
input {
    width: 120px;
    padding: 5px;
    border-radius: 5px;
    border: none;
}

/* BUTTONS */
button {
    margin-top: 8px;
    padding: 10px;
    border-radius: 8px;
    border: none;
    cursor: pointer;
}

.home {
    background: black;
    color: white;
}

.action {
    background: #1f1f1f;
    color: white;
}

/* RESULT BOX */
.result {
    margin-top: 10px;
    background: rgba(0,0,0,0.7);
    padding: 10px;
    border-radius: 10px;
    white-space: pre-line;
}
</style>
</head>

<body>

<!-- HOME -->
<div id="home" class="screen active">
<div class="content">

<h1>PhyFit</h1>

<div class="btn-row">
<button class="home-btn" onclick="show('basic')">Basic</button>
<button class="home-btn" onclick="show('fit')">Fit</button>
<button class="home-btn" onclick="show('athletic')">Athletic</button>
</div>

</div>
</div>

<!-- BASIC -->
<div id="basic" class="screen plan">
<div class="content box">

<h2>Basic Plan</h2>

<p>Calories <input data-key="basic_cal"></p>
<p>Cardio <input data-key="basic_cardio"></p>
<p>Water <input data-key="basic_water"></p>

<button class="action" onclick="saveToFile()">Save</button>
<button class="action" onclick="showLevel('basic')">See Level</button>
<button class="home" onclick="show('home')">Home</button>

<div id="basic_out" class="result"></div>

</div>
</div>

<!-- FIT -->
<div id="fit" class="screen plan">
<div class="content box">

<h2>Fit Plan</h2>

<p>Calories <input data-key="fit_cal"></p>
<p>Cardio <input data-key="fit_cardio"></p>
<p>Calisthenics <input data-key="fit_calisthenics"></p>
<p>Water <input data-key="fit_water"></p>

<button class="action" onclick="saveToFile()">Save</button>
<button class="action" onclick="showLevel('fit')">See Level</button>
<button class="home" onclick="show('home')">Home</button>

<div id="fit_out" class="result"></div>

</div>
</div>

<!-- ATHLETIC -->
<div id="athletic" class="screen plan">
<div class="content box">

<h2>Athletic Plan</h2>

<p>Calories <input data-key="ath_cal"></p>
<p>Cardio <input data-key="ath_cardio"></p>
<p>Calisthenics <input data-key="ath_calisthenics"></p>
<p>Water <input data-key="ath_water"></p>

<button class="action" onclick="saveToFile()">Save</button>
<button class="action" onclick="showLevel('athletic')">See Level</button>
<button class="home" onclick="show('home')">Home</button>

<div id="ath_out" class="result"></div>

</div>
</div>

<script>

function show(id){
    document.querySelectorAll(".screen").forEach(s=>s.classList.remove("active"));
    document.getElementById(id).classList.add("active");
}

/* SAFE INPUT GETTER */
function getVal(key){
    let el = document.querySelector(`[data-key="${key}"]`);
    return el && el.value !== "" ? parseFloat(el.value) : 0;
}

/* SAVE FILE */
async function saveToFile(){

    const inputs=document.querySelectorAll("input");

    let data="PhyFit Data\n\n";

    inputs.forEach(i=>{
        data+=i.getAttribute("data-key")+": "+i.value+"\n";
    });

    const handle=await window.showSaveFilePicker({
        suggestedName:"phyfit.txt",
        types:[{accept:{"text/plain":[".txt"]}}]
    });

    const writable=await handle.createWritable();
    await writable.write(data);
    await writable.close();

    alert("Saved ✔");
}

/* ================= LEVELING SYSTEM ================= */
function showLevel(plan){

let good=0, okay=0, bad=0;

let cal = getVal(plan+"_cal");
let cardio = getVal(plan+"_cardio");
let water = getVal(plan+"_water");
let calisthenics = getVal(plan+"_calisthenics");

/* helper scoring */
function score(goodCond, okayCond){
    if(goodCond) good++;
    else if(okayCond) okay++;
    else bad++;
}

/* BASIC */
if(plan==="basic"){
    score(cal>=2400 && cal<=2500, cal>=2050 && cal<=2300);
    score(cardio>=53 && cardio<=70, cardio>=40 && cardio<=50);
    score(water>=2.3 && water<=3.2, water>=2 && water<=2.25);
}

/* FIT */
if(plan==="fit"){
    score(cal>=2200 && cal<=2600, cal>=2000 && cal<2200);
    score(cardio>=60 && cardio<=90, cardio>=40 && cardio<60);
    score(water>=2.5 && water<=3.2, water>=2.0 && water<2.5);
    score(calisthenics>=10 && calisthenics<=20, calisthenics>=5 && calisthenics<10);
}

/* ATHLETIC */
if(plan==="athletic"){
    score(cal>=2700 && cal<=3500, cal>=2400 && cal<2700);
    score(cardio>=80 && cardio<=120, cardio>=60 && cardio<80);
    score(water>=3.0 && water<=4.0, water>=2.5 && water<3.0);
    score(calisthenics>=20 && calisthenics<=40, calisthenics>=10 && calisthenics<20);
}

/* XP SYSTEM */
let xp = (good * 30) + (okay * 15);
let level = Math.floor(xp / 100) + 1;

/* OUTPUT */
document.getElementById(plan+"_out").innerText =
`GOOD ✔: ${good}
OKAY ⚠️: ${okay}
BAD ❌: ${bad}

XP ⭐: ${xp}
LEVEL 🏆: ${level}`;

}

</script>

</body>
</html>
