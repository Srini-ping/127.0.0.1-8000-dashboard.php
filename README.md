<?php
session_start();
if(!isset($_SESSION['login'])){
header("Location: login.php");
exit();
}
?>

<!DOCTYPE html>
<html>
<head>

<title>BMS Dashboard</title>

<meta name="viewport" content="width=device-width, initial-scale=1">

<link href="https://cdn.jsdelivr.net/npm/bootstrap@4.6.2/dist/css/bootstrap.min.css" rel="stylesheet">

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>

body{
background:linear-gradient(270deg,#1d2671,#c33764);
color:white;
font-family:Arial;
}

#sidebar{
width:220px;
background:#111;
position:fixed;
height:100%;
padding:20px;
}

#sidebar.hide{
left:-220px;
}

#content{
margin-left:220px;
padding:20px;
}

.card{
color:black;
}

</style>

</head>

<body>

<div id="sidebar">

<h4>BMS PANEL</h4>
<hr>

<p>UPS Monitoring</p>
<p>EB Monitoring</p>

<a href="logout.php" style="color:red">Logout</a>

</div>

<div id="content">

<button class="btn btn-dark mb-3" onclick="toggleSidebar()">☰ Menu</button>

<div class="row">

<div class="col-md-6">

<div class="card p-3">

<h5>UPS Monitoring</h5>

<select id="upsSelect" class="form-control mb-2"></select>

<div class="form-row mb-2">
<input id="l1" class="form-control col" placeholder="L1">
<input id="l2" class="form-control col" placeholder="L2">
<input id="l3" class="form-control col" placeholder="L3">
</div>

<button class="btn btn-primary btn-block" onclick="calcUPS()">Update</button>

<hr>

<p>Total Load: <b><span id="ut">0</span> kVA</b></p>
<p>Load %: <b><span id="ul">0%</span></b></p>

<canvas id="upsChart"></canvas>

</div>

</div>

<div class="col-md-6">

<div class="card p-3">

<h5>EB Monitoring</h5>

<input id="eb" class="form-control mb-2" placeholder="Enter kWh">

<button class="btn btn-success btn-block" onclick="addEB()">Add</button>

<hr>

<p>Today: <b><span id="day">0</span></b></p>
<p>Total: <b><span id="month">0</span></b></p>

</div>

</div>

</div>

<div class="card p-3 mt-3">

<button class="btn btn-warning" onclick="sendMail()">📧 Email Report</button>

</div>

</div>

<script>

const sidebar=document.getElementById("sidebar");

function toggleSidebar(){
sidebar.classList.toggle("hide");
}

const UPS_LIST=[
{name:"UPS-1",cap:200},
{name:"UPS-2",cap:200},
{name:"UPS-3",cap:200},
{name:"UPS-4",cap:200},
{name:"UPS-5",cap:80}
];

UPS_LIST.forEach((u,i)=>{
upsSelect.innerHTML+=`<option value="${i}">${u.name}</option>`;
});

const upsChart=new Chart(
document.getElementById("upsChart"),
{
type:"line",
data:{labels:[],datasets:[{label:"UPS Load",data:[]}]}
}
);

function calcUPS(){

const cap=UPS_LIST[upsSelect.value].cap;

const total=(+l1.value||0)+(+l2.value||0)+(+l3.value||0);

ut.innerText=total;

ul.innerText=((total/cap)*100).toFixed(1)+"%";

upsChart.data.labels.push(new Date().toLocaleTimeString());

upsChart.data.datasets[0].data.push(total);

upsChart.update();

}

let EB=[];

function addEB(){

const val=+eb.value||0;

if(val<=0)return;

EB.push(val);

day.innerText=val;

month.innerText=EB.reduce((a,b)=>a+b,0);

eb.value="";

}

function sendMail(){

const body=
`UPS Load ${ut.innerText} kVA
Load ${ul.innerText}
EB Total ${month.innerText}`;

window.location="mailto:?subject=BMS Report&body="+encodeURIComponent(body);

}

</script>

</body>
</html>
