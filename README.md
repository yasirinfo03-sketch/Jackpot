<!DOCTYPE html>
<html>
<head>
<title>Project Farming - Boss</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
body{font-family:Arial;margin:0;padding:0;text-align:center;background:#f2f2f2;}
.box{background:white;width:300px;margin:15px auto;padding:15px;border-radius:10px;box-shadow:0 0 10px #ccc;}
button{padding:8px 15px;background:green;color:white;border:none;border-radius:5px;cursor:pointer;margin:5px;}
input{width:90%;padding:8px;margin:8px 0;border-radius:5px;border:1px solid #ccc;}
.top{background:green;color:white;padding:15px;position:relative;}
.menu{position:absolute;right:15px;top:15px;font-size:22px;cursor:pointer;}
.dropdown{display:none;position:absolute;right:15px;top:50px;background:white;box-shadow:0 0 10px #ccc;border-radius:5px;}
.dropdown div{padding:10px 20px;cursor:pointer;}
.dropdown div:hover{background:#eee;}
</style>
</head>
<body>

<!-- REGISTER / LOGIN -->
<div id="authBox" class="box">
<div id="loginBox">
<h3>Login</h3>
<input type="text" id="loginUser" placeholder="User ID"><br>
<input type="password" id="loginPass" placeholder="Password"><br>
<button onclick="login()">Login</button><br><br>
<span style="color:blue;cursor:pointer;" onclick="showRegister()">New user? Register</span>
</div>

<div id="registerBox" style="display:none;">
<h3>Register</h3>
<input type="text" id="regUser" placeholder="User ID"><br>
<input type="password" id="regPass" placeholder="Password"><br>
<input type="text" id="refCode" placeholder="Referral Code (Optional)"><br>
<button onclick="register()">Register</button><br><br>
<span style="color:blue;cursor:pointer;" onclick="showLogin()">Already have account? Login</span>
</div>
</div>

<!-- MAIN PAGE -->
<div id="mainPage" style="display:none;">
<div class="top">
🌾 Project Farming <br>
Balance: <span id="balance">0</span> Tk
<span class="menu" onclick="toggleMenu()">⋮</span>
<div class="dropdown" id="menuBox">
<div onclick="goWithdraw()">Withdraw</div>
<div onclick="changePass()">Change Password</div>
<div onclick="logout()">Logout</div>
</div>
</div>

<div class="box">
<h3>🥈 Silver - 250 Tk</h3>
10% Cashback<br>Daily 11 Tk<br>Referral 20 Tk<br>
<button onclick="buy(250,11,20)">Buy</button>
</div>

<div class="box">
<h3>🥇 Gold - 550 Tk</h3>
80 Tk Cashback<br>Daily 25 Tk<br>Referral 35 Tk<br>
<button onclick="buy(550,25,35)">Buy</button>
</div>

<div class="box">
<h3>🏆 Platinum - 1050 Tk</h3>
200 Tk Cashback<br>Daily 40 Tk<br>Referral 50 Tk<br>
<button onclick="buy(1050,40,50)">Buy</button>
</div>

<div class="box">
<h3>💎 Diamond - 1550 Tk</h3>
350 Tk Cashback<br>Daily 50 Tk<br>Referral 100 Tk<br>
<button onclick="buy(1550,50,100)">Buy</button>
</div>

<div class="box">
<h3>👑 Premium - 2250 Tk</h3>
500 Tk Cashback<br>Daily 80 Tk<br>Referral 150 Tk<br>
<button onclick="buy(2250,80,150)">Buy</button>
</div>

<div class="box">
<h3>Deposit Money</h3>
<select id="depositMethod">
<option>bKash</option>
<option>Nagad</option>
<option>Rocket</option>
</select><br>
<input type="number" id="depositAmount" placeholder="Amount"><br>
<button onclick="deposit()">Deposit</button>
</div>
</div>

<script>

// REGISTER / LOGIN Toggle
function showRegister(){document.getElementById("loginBox").style.display="none";document.getElementById("registerBox").style.display="block";}
function showLogin(){document.getElementById("registerBox").style.display="none";document.getElementById("loginBox").style.display="block";}

// REGISTER
function register(){
let user=regUser.value,pass=regPass.value,ref=refCode.value;
if(!user||!pass){alert("Fill all fields");return;}
if(localStorage.getItem(user)){alert("User exists!");return;}
let data={password:pass,balance:0,dailyIncome:0,referral:ref,lastClaim:Date.now()};
localStorage.setItem(user,JSON.stringify(data));
alert("Registered! Now login.");showLogin();
}

// LOGIN
function login(){
let user=loginUser.value,pass=loginPass.value;
let data=localStorage.getItem(user);
if(!data){alert("User not found!");return;}
data=JSON.parse(data);
if(data.password!==pass){alert("Wrong password!");return;}
localStorage.setItem("currentUser",user);
loadMain();
}

// LOAD MAIN PAGE
function loadMain(){
document.getElementById("authBox").style.display="none";
document.getElementById("mainPage").style.display="block";

let user=localStorage.getItem("currentUser");
let data=JSON.parse(localStorage.getItem(user));

// Daily income add (once per day)
let now=Date.now();
if(now-data.lastClaim>=86400000){data.balance+=data.dailyIncome;data.lastClaim=now;localStorage.setItem(user,JSON.stringify(data));}

document.getElementById("balance").innerText=data.balance;
}

// BUY PACKAGE
function buy(price,daily,refBonus){
let user=localStorage.getItem("currentUser");
let data=JSON.parse(localStorage.getItem(user));
if(data.balance>=price){
data.balance-=price;
data.dailyIncome+=daily;
localStorage.setItem(user,JSON.stringify(data));
alert("Package Purchased!");document.getElementById("balance").innerText=data.balance;
}else{alert("Not Enough Balance!");}
}

// MENU
function toggleMenu(){let m=document.getElementById("menuBox");m.style.display=m.style.display==="block"?"none":"block";}
function goWithdraw(){
let amt=prompt("Enter withdraw amount (min 100):");
if(!amt||amt<100){alert("Minimum withdraw 100!");return;}
let user=localStorage.getItem("currentUser");
let data=JSON.parse(localStorage.getItem(user));
if(amt>data.balance){alert("Not enough balance!");return;}
data.balance-=parseInt(amt);
localStorage.setItem(user,JSON.stringify(data));
alert("Withdraw Successful!");document.getElementById("balance").innerText=data.balance;
}
function changePass(){let np=prompt("Enter new password:");if(np){let user=localStorage.getItem("currentUser");let data=JSON.parse(localStorage.getItem(user));data.password=np;localStorage.setItem(user,JSON.stringify(data));alert("Password Changed!");}}
function logout(){localStorage.removeItem("currentUser");location.reload();}

// INITIALIZE IF ALREADY LOGGED IN
if(localStorage.getItem("currentUser")){loadMain();}

function deposit(){
let amt=document.getElementById("depositAmount").value;
if(!amt||amt<=0){alert("Enter valid amount");return;}
let user=localStorage.getItem("currentUser");
let data=JSON.parse(localStorage.getItem(user));
data.balance+=parseInt(amt);
localStorage.setItem(user,JSON.stringify(data));
document.getElementById("balance").innerText=data.balance;
alert("Deposit Successful!");
}

</script>

</body>
</html># Jackpot
