var interval = {
    "test": "",
    "click": "",
    "deltest": "",
} 
var bool = {
    "bot": true,
    "on": true,
    "skip": true,
} 
var object = {
    "div": "",
    "button1": "",
}

object.div = document.createElement("DIV");
object.div.style.width = "202px";
object.div.style.height = "26px";
object.div.style.border = "3px solid black";
object.div.id = "div";
object.div.style.background = "gray";

object.button2 = document.createElement("BUTTON");
object.button2.style.width = "100px";
object.button2.style.height = "25px";
object.button2.style.border = "3px solid black";
object.button2.style.background = "white";
object.button2.id = "button2";
object.button2.innerHTML = "Skip: On";
object.button2.setAttribute("onclick", "skip()");

object.button1 = document.createElement("BUTTON");
object.button1.style.width = "100px";
object.button1.style.height = "25px";
object.button1.style.border = "3px solid black";
object.button1.style.background = "white";
object.button1.id = "button1";
object.button1.innerHTML = "On";
object.button1.setAttribute("onclick", "start()");

document.body.appendChild(object.div);
document.getElementById("div").appendChild(object.button1);
document.getElementById("div").appendChild(object.button2);

var accu_reduction = 0
var targ_accu = 0
var disp_accu = 0
while (targ_accu == 0){
    accu_reduction = (Math.random() * 15);
    targ_accu = 97 - accu_reduction;
    disp_accu = targ_accu.toFixed(0);
    targ_accu = targ_accu / 100;
    console.log("Session Accuracy Target is " + disp_accu + "%");
}


function test() {
    if(bool.bot == true) {
        try{
            if(document.Pass) {
                bool.bot = false;
                set("click");
            } 
            else if (document.getElementById("next-btn")) {
                setTimeout(() => {set("study")}, 60000);
                setTimeout(() => {newWordLogs("45 seconds left")}, 15000);
                setTimeout(() => {newWordLogs("30 seconds left")}, 30000);
                setTimeout(() => {newWordLogs("15 seconds left")}, 45000);
                console.log("New word - will wait for 1 minute before continuing");
                setTimeout(() => {set("newWord")}, 1500);
                set("stop")
            } 
            else {
                setTimeout(() => {set("test")}, 1000);
                console.log("Scanning for question");
                set("stop");
            }
        }
        catch {TypeError}
    }
} 


function set(type) {
    if(type == "click") {
        try{
            if(Math.random() > targ_accu && bool.skip == true && alotted_time()) { // To skip or not the skip
                // Will skip if it rolls to do so, the user hasn't disabled skipping, and the question has a time limit
                let skipTime = alotted_time() * 1000;
                console.log("Skipping question");
                setTimeout(() => {set("skippedWord")}, skipTime);
                set("stop");
            }
            else if (alotted_time()) { // Answers the question 5 seconds before you run out of time
                let ansTime = alotted_time() - 5;
                console.log("This question will be answered in", ansTime, "seconds");
                ansTime *= 1000;
                interval.click = setTimeout(press, ansTime);
            }
            else {  // If the question has no time limit it answers in 15 seconds
                console.log("This question will be answered in", 15, "seconds");
                interval.click = setTimeout(press, 30000);
            }
        }
        catch{TypeError}
    } 
    else if (type == "test") { // starts testing after 2 seconds
        interval.deltest = setTimeout(function x(){bool.bot = true;}, 2000);
    } 
    else if (type == "stop") { // Clear answering and testing timeouts
        clearTimeout(interval.click);
        clearTimeout(interval.deltest);
        bool.bot = false; 
    }
    else if (type == "newWord") { // Constantly checks to make sure that the user hasn't manually skipped the new word
        if (document.getElementById("next-btn")){
            setTimeout(() => {set("newWord")}, 1500);
        }
        else {
            set("test");
        }
    }
    else if (type == "skippedWord") { // Constantly looks for the next button, clicks it when it's found
        if (document.getElementById("next-btn")){
            set("study");
            set("test");
        }
        else {
            setTimeout(() => {set("skippedWord")}, 1000);
        }
    }
    else if (type == "study") { // Skips thru the study screen
        if (document.getElementById("next-btn")){
            let x = document.querySelectorAll("#choice-section li");
            console.log("Next button clicked");
            x[0].click();
            x[1].click();
            x[2].click();
            document.getElementById("next-btn").click();
        }
    }
}

function newWordLogs(timeLeft){
    if (document.getElementById("next-btn")){
    console.log(timeLeft);
    }
}

function alotted_time(){ // Gets the alotted time for the question
    try{
        const timerContainer = document.getElementById('timer-container');
        const timeoutValue = timerContainer.getAttribute('data-timeout');
        return parseInt(timeoutValue);
    }
    catch {TypeError}
}

function press() { // Answers the question, avoids the button that detects cheating
    try{
        document.activeElement.blur()
    }
    catch {TypeError}
    if(document.getElementById("pass__pass")){
        document.getElementById("pass__pass").click();
        console.log("Question answered");
        set("test");
    }
    else{
        try{
            document.Pass.click();
            console.log("Question answered");
            set("test");
        }
        catch {TypeError}{
            set("test");
        }    
    }    
}

function start() {
    if(bool.on == true) {
        set("stop");
        bool.on = false;
        bool.bot = false;
        set("stop");
        console.log("Bot Inactive");
        document.getElementById("button1").innerHTML = "Off";
    } else if (bool.on == false) {
        bool.on = true;
        bool.bot = true;
        console.log("Bot Active");
        document.getElementById("button1").innerHTML = "On";
    }
} 

function skip() {
    if(bool.skip == true) {
        bool.skip = false;
        console.log("Answering all questions (Will still wait for 1 minute on new words)");
        document.getElementById("button2").innerHTML = "Skip: Off";
    
    } else if (bool.skip == false) {
        bool.skip = true;
        console.log("Skipping some questions");
        document.getElementById("button2").innerHTML = "Skip: On";
    }
}

interval.test = setInterval(test, 1);
