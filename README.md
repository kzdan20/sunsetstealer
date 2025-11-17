<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>SunsetStealer</title>
<style>
  body {
    margin: 0;
    padding: 0;
    font-family: "Poppins", sans-serif;
    background: black;
    color: white;
    min-height: 100vh;
    overflow-x: hidden; 
  }
  canvas {
    position: fixed;
    top: 0;
    left: 0;
    width: 100vw;
    height: 100vh;
    z-index: 0;
  }
  .container {
    position: relative;
    z-index: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px;
    min-height: 100vh;
  }
  h1 {
    font-style: italic;
    font-size: 2.5em;
    background: linear-gradient(90deg, #00ff00, #00ffff, #00ff00);
    background-size: 300% 300%;
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    animation: colorShift 4s infinite linear;
    margin-bottom: 1em;
    text-align: center;
  }
  @keyframes colorShift {
    0% { background-position: 0% 50%; }
    50% { background-position: 100% 50%; }
    100% { background-position: 0% 50%; }
  }
  .form-box {
    background: rgba(0, 0, 0, 0.7);
    border: 1px solid #00ff00;
    border-radius: 10px;
    padding: 1.5em;
    width: 100%;
    max-width: 400px;
    text-align: center;
    box-shadow: 0 0 20px #00ff00;
    margin-bottom: 20px;
  }
  input, textarea, select {
    width: 100%;
    padding: 10px;
    margin: 10px 0;
    background: black;
    border: 1px solid #00ff00;
    color: #00ff00;
    border-radius: 5px;
  }
  button {
    width: 100%;
    padding: 10px;
    background: #00ff00;
    border: none;
    color: black;
    font-weight: bold;
    cursor: pointer;
    border-radius: 5px;
    margin-top: 10px;
  }
  button:hover { background: #00cc00; }
  a { display: block; color: #00ffff; margin-top: 10px; text-decoration: none; }
  .hidden { display: none; }
  .admin-table { width: 100%; border-collapse: collapse; table-layout: fixed; word-break: break-word; }
  .admin-table th, .admin-table td { border: 1px solid #00ff00; padding: 5px; text-align: center; }
  .ad { margin-bottom: 20px; }
  video { border: 1px solid #00ff00; border-radius: 5px; }
</style>
</head>
<body>

<canvas id="matrix"></canvas>

<div class="container">
  <h1>SunsetStealer</h1>

  <!-- LOGIN -->
  <div id="login" class="form-box">
    <h2>Login</h2>
    <input type="text" id="loginEmail" placeholder="Username or Email" />
    <input type="password" id="loginPassword" placeholder="Password" />
    <button id="loginBtn">Login</button>
    <a href="#" id="toRegister">Create account</a>
    <a href="#" id="toRecover">Forgot password?</a>
  </div>

  <!-- REGISTER -->
  <div id="register" class="form-box hidden">
    <h2>Register</h2>
    <input type="text" id="regEmail" placeholder="Username or Email" />
    <input type="password" id="regPassword" placeholder="Password" />
    <button id="registerBtn">Create Account</button>
    <a href="#" id="backToLogin1">Back to login</a>
  </div>

  <!-- RECOVER -->
  <div id="recover" class="form-box hidden">
    <h2>Recover Password</h2>
    <input type="text" id="recEmail" placeholder="Username or Email" />
    <input type="text" id="recCode" placeholder="Recovery Code" />
    <input type="password" id="newPass" placeholder="New Password" />
    <button id="recoverBtn">Reset Password</button>
    <a href="#" id="backToLogin2">Back to login</a>
  </div>

  <!-- USER DASHBOARD -->
  <div id="dashboard" class="form-box hidden">
    <h2>User Dashboard</h2>
    <p>Balance: <span id="balance">0</span> KSH</p>
    <div id="messageBox" style="color:#00ffff;"></div>

    <!-- Withdraw Section (prominent, directly below balance) -->
    <div id="withdrawSection" style="margin:20px 0; padding:10px; border:1px solid #00ff00; border-radius:5px;">
      <h3>Withdraw Balance</h3>
      <input type="number" id="withdrawAmount" placeholder="Amount to withdraw" />
      <button id="withdrawBtn">Withdraw</button>
      <div id="withdrawMessage" style="color:#00ffff; margin-top:5px;"></div>
    </div>

    <!-- Ads Container -->
    <h3>Watch Ads</h3>
    <div id="adsContainer"></div>

    <button id="logoutBtn1" style="margin-top:20px;">Logout</button>
  </div>

  <!-- ADMIN DASHBOARD -->
  <div id="adminDashboard" class="form-box hidden" style="width:100%; max-width:900px;">
    <h2>Admin Dashboard</h2>
    <table class="admin-table" id="userTable">
      <tr>
        <th>User</th><th>Balance</th><th>Status</th><th>Actions</th>
      </tr>
    </table>

    <h3>Send Message</h3>
    <select id="msgUser"></select>
    <textarea id="msgText" placeholder="Enter message"></textarea>
    <button id="sendMsgBtn">Send</button>
    <button id="logoutBtn2">Logout</button>
  </div>
</div>

<script>
// MATRIX BACKGROUND
const canvas=document.getElementById("matrix");
const ctx=canvas.getContext("2d");
canvas.height=window.innerHeight;
canvas.width=window.innerWidth;
const letters="ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789@#$%^&*()";
const fontSize=16;
const columns=canvas.width/fontSize;
const drops=Array(Math.floor(columns)).fill(1);
function drawMatrix(){
  ctx.fillStyle="rgba(0,0,0,0.05)";
  ctx.fillRect(0,0,canvas.width,canvas.height);
  ctx.fillStyle="#0F0";
  ctx.font=fontSize+"px monospace";
  for(let i=0;i<drops.length;i++){
    const text=letters.charAt(Math.floor(Math.random()*letters.length));
    ctx.fillText(text,i*fontSize,drops[i]*fontSize);
    if(drops[i]*fontSize>canvas.height && Math.random()>0.975)drops[i]=0;
    drops[i]++;
  }
}
setInterval(drawMatrix,33);

// PAGE SWITCHING
function hideAll(){ ["login","register","recover","dashboard","adminDashboard"].forEach(id=>document.getElementById(id).classList.add("hidden")) }
function showLogin(){ hideAll(); document.getElementById("login").classList.remove("hidden") }
function showRegister(){ hideAll(); document.getElementById("register").classList.remove("hidden") }
function showRecover(){ hideAll(); document.getElementById("recover").classList.remove("hidden") }
document.getElementById("toRegister").onclick=showRegister;
document.getElementById("toRecover").onclick=showRecover;
document.getElementById("backToLogin1").onclick=showLogin;
document.getElementById("backToLogin2").onclick=showLogin;

// REGISTER
document.getElementById("registerBtn").onclick=function(){
  const email=document.getElementById("regEmail").value.trim().toLowerCase();
  const pass=document.getElementById("regPassword").value.trim();
  if(!email||!pass)return alert("Please fill all fields");
  if(email==="sunsetstealer")return alert("Reserved for admin");
  const code=Math.random().toString(36).substring(2,8).toUpperCase();
  const user={email,pass,code,balance:0,active:false,message:""};
  localStorage.setItem(email,JSON.stringify(user));
  alert("Account created! Your recovery code: "+code);
  showLogin();
}

// INITIALIZE ADMIN
if(!localStorage.getItem("sunsetstealer")){
  const admin={email:"sunsetstealer",pass:"lonasoka2025.",role:"admin"};
  localStorage.setItem("sunsetstealer",JSON.stringify(admin));
}

// LOGIN
document.getElementById("loginBtn").onclick=function(){
  const email=document.getElementById("loginEmail").value.trim().toLowerCase();
  const pass=document.getElementById("loginPassword").value.trim();
  const admin=JSON.parse(localStorage.getItem("sunsetstealer"));
  if(email===admin.email && pass===admin.pass){ sessionStorage.setItem("currentUser",email); showAdminDashboard(); return; }
  const userData=localStorage.getItem(email);
  if(!userData)return alert("No such account");
  const user=JSON.parse(userData);
  if(user.pass!==pass)return alert("Wrong password");
  user.active=true;
  localStorage.setItem(email,JSON.stringify(user));
  sessionStorage.setItem("currentUser",email);
  showDashboard();
}

// RECOVER
document.getElementById("recoverBtn").onclick=function(){
  const email=document.getElementById("recEmail").value.trim().toLowerCase();
  const code=document.getElementById("recCode").value.trim();
  const newPass=document.getElementById("newPass").value.trim();
  const userData=localStorage.getItem(email);
  if(!userData)return alert("User not found");
  const user=JSON.parse(userData);
  if(user.code!==code)return alert("Invalid recovery code");
  user.pass=newPass;
  localStorage.setItem(email,JSON.stringify(user));
  alert("Password reset!");
  showLogin();
}

// DASHBOARD
function showDashboard(){
  hideAll();
  document.getElementById("dashboard").classList.remove("hidden");
  const email=sessionStorage.getItem("currentUser");
  const user=JSON.parse(localStorage.getItem(email));
  document.getElementById("balance").innerText=user.balance;
  if(user.message){ document.getElementById("messageBox").innerHTML=`<strong>Message:</strong> ${user.message}`; user.message=""; localStorage.setItem(email,JSON.stringify(user)); }
  loadAds();
}

// WATCHABLE ADS DYNAMICALLY (10 ADS)
function loadAds(){
  const adsContainer=document.getElementById("adsContainer");
  adsContainer.innerHTML="";
  const videoSources=[
    "https://www.w3schools.com/html/mov_bbb.mp4",
    "https://www.w3schools.com/html/movie.mp4",
    "https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.mp4",
    "https://interactive-examples.mdn.mozilla.net/media/cc0-videos/flower.webm",
    "https://www.learningcontainer.com/wp-content/uploads/2020/05/sample-mp4-file.mp4"
  ];
  for(let i=1;i<=10;i++){
    const adDiv=document.createElement("div");
    adDiv.className="ad";
    const adTitle=document.createElement("p");
    adTitle.innerHTML=`<strong>Ad #${i}:</strong> Product ${i}`;
    adDiv.appendChild(adTitle);
    const video=document.createElement("video");
    video.className="watchAdVideo";
    video.width=400;
    video.controls=true;
    const source=document.createElement("source");
    source.src=videoSources[i % videoSources.length];
    source.type="video/mp4";
    video.appendChild(source);
    video.dataset.reward=Math.floor(Math.random()*21)+5;
    adDiv.appendChild(video);
    adsContainer.appendChild(adDiv);
  }
  document.querySelectorAll(".watchAdVideo").forEach(video=>{
    video.onended=function(){
      const reward=parseInt(video.dataset.reward);
      const email=sessionStorage.getItem("currentUser");
      const user=JSON.parse(localStorage.getItem(email));
      user.balance+=reward;
      localStorage.setItem(email,JSON.stringify(user));
      document.getElementById("balance").innerText=user.balance;
      alert(`You watched an ad and earned ${reward} KSH!`);
    };
  });
}

// LOGOUT
document.getElementById("logoutBtn1").onclick=logout;
document.getElementById("logoutBtn2").onclick=logout;
function logout(){
  const email=sessionStorage.getItem("currentUser");
  if(email && email!=="sunsetstealer"){ const user=JSON.parse(localStorage.getItem(email)); user.active=false; localStorage.setItem(email,JSON.stringify(user)); }
  sessionStorage.removeItem("currentUser");
  showLogin();
}

// ADMIN DASHBOARD
function showAdminDashboard(){
  hideAll();
  document.getElementById("adminDashboard").classList.remove("hidden");
  refreshUsers();
}
function refreshUsers(){
  const table=document.getElementById("userTable");
  const select=document.getElementById("msgUser");
  table.innerHTML=`<tr><th>User</th><th>Balance</th><th>Status</th><th>Actions</th></tr>`;
  select.innerHTML="";
  Object.keys(localStorage).forEach(key=>{
    if(key==="sunsetstealer")return;
    const user=JSON.parse(localStorage.getItem(key));
    if(!user.email)return;
    const tr=document.createElement("tr");
    tr.innerHTML=`<td>${user.email}</td><td>${user.balance}</td><td style="color:${user.active?'#00ff00':'#ff0000'};">${user.active?'Active':'Offline'}</td><td><button onclick="changeBalance('${user.email}',5)">+5</button><button onclick="changeBalance('${user.email}',-5)">-5</button></td>`;
    table.appendChild(tr);
    const opt=document.createElement("option");
    opt.value=user.email;
    opt.textContent=user.email;
    select.appendChild(opt);
  });
}
function changeBalance(email,amount){
  const user=JSON.parse(localStorage.getItem(email));
  user.balance+=amount;
  if(user.balance<0)user.balance=0;
  localStorage.setItem(email,JSON.stringify(user));
  refreshUsers();
}
document.getElementById("sendMsgBtn").onclick=function(){
  const email=document.getElementById("msgUser").value;
  const msg=document.getElementById("msgText").value.trim();
  if(!email||!msg)return alert("Fill all fields");
  const user=JSON.parse(localStorage.getItem(email));
  user.message=msg;
  localStorage.setItem(email,JSON.stringify(user));
  alert("Message sent!");
  document.getElementById("msgText").value="";
}

// WITHDRAW
document.getElementById("withdrawBtn").onclick=function(){
  const email=sessionStorage.getItem("currentUser");
  const user=JSON.parse(localStorage.getItem(email));
  const amount=parseInt(document.getElementById("withdrawAmount").value);
  const msgBox=document.getElementById("withdrawMessage");
  msgBox.textContent="";
  if(isNaN(amount)||amount<=0){ msgBox.textContent="Enter a valid amount"; return; }
  if(user.balance<100){ msgBox.textContent="Minimum balance to withdraw is 100 KSH"; return; }
  if(amount>user.balance){ msgBox.textContent="You don't have enough balance"; return; }
  user.balance-=amount;
  localStorage.setItem(email,JSON.stringify(user));
  document.getElementById("balance").innerText=user.balance;
  msgBox.textContent=`Successfully withdrew ${amount} KSH`;
  document.getElementById("withdrawAmount").value="";
};
</script>
</body>
</html>
