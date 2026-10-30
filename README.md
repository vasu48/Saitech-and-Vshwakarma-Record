# Saitech-and-Vshwakarma-Record

<html lang="hi">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>SaiTech &amp; Book Store – Khata / Ledger</title>
<style>
:root{--ink:#16202e;--mut:#5d6b7d;--bg:#eceff3;--card:#fff;--line:#d9dfe7;--acc:#0e6ba8;--red:#b3261e;--grn:#18794e}
@media(prefers-color-scheme:dark){:root{--ink:#e8edf3;--mut:#9aa8b8;--bg:#10151c;--card:#19212b;--line:#2a3542;--red:#ff8a80;--grn:#5fd49b}}
[data-acc="2"]{--acc:#1f7a4d}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--ink);font:15px/1.5 "Segoe UI",system-ui,-apple-system,Roboto,sans-serif}
header{background:var(--card);border-bottom:3px solid var(--acc);display:flex;flex-wrap:wrap;align-items:center;gap:12px;padding:10px 16px}
.brand{display:flex;align-items:center;gap:10px;margin-right:auto}
.brand b{font-size:18px;display:block;line-height:1.2}.brand small{color:var(--mut)}
nav{display:flex;flex-wrap:wrap;gap:4px}
nav a{padding:7px 12px;border-radius:6px;color:var(--ink);text-decoration:none;font-weight:600}
nav a.on,nav a:hover{background:var(--acc);color:#fff}
main{max-width:1100px;margin:0 auto;padding:16px}
h2{margin:4px 0 14px;font-size:22px}
.card{background:var(--card);border:1px solid var(--line);border-radius:8px;padding:16px;margin-bottom:16px}
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(160px,1fr));gap:12px}
.stat{background:var(--card);border:1px solid var(--line);border-left:4px solid var(--acc);border-radius:6px;padding:12px}
.stat span{color:var(--mut);font-size:13px}.stat b{display:block;font-size:22px;font-variant-numeric:tabular-nums}
.stat.big{border-left-width:8px}
button,.btn{background:var(--acc);color:#fff;border:0;border-radius:6px;padding:9px 16px;font:inherit;font-weight:600;cursor:pointer;text-decoration:none;display:inline-block}
button.alt,.btn.alt{background:transparent;color:var(--ink);border:1px solid var(--line)}
button.del{background:var(--red);color:#fff}
button.sm{padding:4px 10px;font-size:13px}
button:focus-visible,input:focus-visible,select:focus-visible,textarea:focus-visible,a:focus-visible{outline:3px solid var(--acc);outline-offset:1px}
label{display:block;font-weight:600;margin:10px 0 4px}
input,select,textarea{width:100%;padding:9px 10px;border:1px solid var(--line);border-radius:6px;background:var(--card);color:var(--ink);font:inherit}
.row{display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:12px}
.bar{display:flex;flex-wrap:wrap;gap:8px;margin:10px 0}
.scroll{overflow-x:auto}
table{width:100%;border-collapse:collapse;font-size:14px}
th,td{padding:8px 10px;border-bottom:1px solid var(--line);text-align:left;white-space:nowrap}
th{background:rgba(127,127,127,.12)}
td.n,th.n{text-align:right;font-variant-numeric:tabular-nums}
tfoot td{font-weight:700}
.tag{padding:2px 8px;border-radius:10px;font-size:12px;font-weight:700;color:#fff}
.tag.liya{background:var(--grn);color:#052}.tag.diya{background:var(--red);color:#fff}
.pos{color:var(--grn)}.neg{color:var(--red)}
.ph{display:inline-grid;place-items:center;border-radius:8px;background:var(--acc);color:#fff;font-weight:700}
.logo{border-radius:8px;background:#fff}
.login{max-width:400px;margin:8vh auto}
.seg{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.seg label{margin:0;border:2px solid var(--line);border-radius:8px;padding:12px;text-align:center;cursor:pointer}
.seg input{display:none}.seg input:checked+span{color:var(--acc)}
.seg label:has(input:checked){border-color:var(--acc)}
.err{color:var(--red);font-weight:600;min-height:20px}
.pend{font-size:20px;font-weight:700;padding:8px 0}
.printhead{display:none;align-items:center;gap:12px;border-bottom:2px solid #000;padding-bottom:8px;margin-bottom:10px}
.empty{color:var(--mut);padding:20px;text-align:center}
@media print{header,.noprint{display:none!important}body{background:#fff;color:#000}.card{border:0;padding:0}.printhead{display:flex}th,td{white-space:normal}}
</style>
</head>
<body>
<div id="app"></div>
<script>
const K='ledger_v1',A=document.getElementById('app');
const esc=s=>String(s==null?'':s).replace(/[&<>"']/g,c=>({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c]));
const fm=n=>'₹'+(+n||0).toLocaleString('en-IN',{maximumFractionDigits:2});
const day=(d=new Date())=>d.toLocaleDateString('en-CA');
async function sha(p){try{const b=await crypto.subtle.digest('SHA-256',new TextEncoder().encode('lg$'+p));return[...new Uint8Array(b)].map(x=>x.toString(16).padStart(2,'0')).join('')}catch(e){return'x'+btoa(unescape(encodeURIComponent('lg$'+p)))}}
let db,uid=+sessionStorage.uid||0,tmpLogo=null;
let F={q:'',from:'',to:'',type:''},R={kind:'daily',d:day(),m:day().slice(0,7)};
const save=()=>{try{localStorage[K]=JSON.stringify(db)}catch(e){alert('Storage full / स्टोरेज भर गया')}};
const me=()=>db.users.find(u=>u.id==uid);
// DATA ISOLATION: every read/write goes through the logged-in user's id
const mine=()=>db.tx.filter(t=>t.user_id==uid).sort((a,b)=>(b.transaction_date+b.transaction_time).localeCompare(a.transaction_date+a.transaction_time)||b.id-a.id);
const sum=l=>{const s={total:0,paid:0,pend:0,liya:0,diya:0,lp:0,dp:0};l.forEach(t=>{s.total+=t.total_amount;s.paid+=t.paid_amount;s.pend+=t.pending_amount;if(t.transaction_type=='liya'){s.liya+=t.total_amount;s.lp+=t.paid_amount}else{s.diya+=t.total_amount;s.dp+=t.paid_amount}});s.bal=s.lp-s.dp;return s};
const logo=(u,s)=>u.logo?`<img class="logo" src="${u.logo}" alt="logo" style="width:${s}px;height:${s}px;object-fit:contain">`:`<span class="ph" style="width:${s}px;height:${s}px;font-size:${s/2}px">${esc(u.account_name[0])}</span>`;

(async()=>{
  try{db=JSON.parse(localStorage[K])}catch(e){}
  if(!db||!db.users){
    const now=new Date().toISOString();
    db={seq:1,tx:[],users:[
      {id:1,account_name:'SaiTech',username:'saitech',password_hash:await sha('saitech123'),logo:'',created_at:now},
      {id:2,account_name:'Book Store',username:'bookstore',password_hash:await sha('book123'),logo:'',created_at:now}]};
    save();
  }
  route();
})();
window.onhashchange=()=>db&&route();

function route(){
  if(!uid||!me()){document.body.dataset.acc='';return loginView()}
  document.body.dataset.acc=uid;
  const [p,id]=(location.hash.slice(1)||'dash').split('-');
  ({dash:dashView,add:()=>formView(),edit:()=>formView(id),hist:histView,rep:repView,prof:profView}[p]||dashView)();
  window.scrollTo(0,0);
}
function shell(html,on){
  const u=me(),L=[['dash','Dashboard / डैशबोर्ड'],['add','+ Add / जोड़ें'],['hist','History / इतिहास'],['rep','Reports / रिपोर्ट'],['prof','Profile / प्रोफ़ाइल']];
  A.innerHTML=`<header><div class="brand">${logo(u,44)}<div><b>${esc(u.account_name)}</b><small>खाता / Ledger</small></div></div>
  <nav>${L.map(l=>`<a href="#${l[0]}" class="${on==l[0]?'on':''}">${l[1]}</a>`).join('')}</nav>
  <button class="alt" onclick="logout()">Logout</button></header><main>${html}</main>`;
}

/* ---------- LOGIN ---------- */
function loginView(){
  A.innerHTML=`<div class="login card"><h2>Khata / खाता लॉगिन</h2>
  <form onsubmit="doLogin(event)"><div class="seg">${db.users.map((u,i)=>`<label><input type="radio" name="acc" value="${u.id}" ${i?'':'checked'}><span><b>${esc(u.account_name)}</b></span></label>`).join('')}</div>
  <label>Username</label><input name="user" autocomplete="username" required>
  <label>Password / पासवर्ड</label><input name="pass" type="password" autocomplete="current-password" required>
  <p class="err" id="err"></p><button style="width:100%">Login</button></form></div>`;
}
async function doLogin(e){
  e.preventDefault();const f=e.target,u=db.users.find(x=>x.id==f.acc.value);
  if(u&&u.username==f.user.value.trim()&&u.password_hash==await sha(f.pass.value)){uid=u.id;sessionStorage.uid=uid;location.hash='dash';route()}
  else document.getElementById('err').textContent='Galat username ya password / गलत यूज़रनेम या पासवर्ड';
}
function logout(){sessionStorage.removeItem('uid');uid=0;location.hash='';route()}

/* ---------- DASHBOARD ---------- */
function dashView(){
  const l=mine(),a=sum(l),t=sum(l.filter(x=>x.transaction_date==day())),m=sum(l.filter(x=>x.transaction_date.startsWith(day().slice(0,7))));
  const S=(n,v,c='')=>`<div class="stat"><span>${n}</span><b class="${c}">${fm(v)}</b></div>`;
  shell(`<h2>Dashboard</h2><div class="bar"><a class="btn" href="#add">+ Add Transaction / नया लेन-देन</a><a class="btn alt" href="#hist">Transaction History</a></div>
  <div class="grid"><div class="stat big"><span>Current Balance / वर्तमान बैलेंस<br><small>(Liya paid − Diya paid)</small></span><b class="${a.bal<0?'neg':'pos'}">${fm(a.bal)}</b></div>
  ${S('Total Amount',a.total)}${S('Total Paid',a.paid)}${S('Total Pending / बाकी',a.pend,'neg')}${S('Total Liya / Received',a.liya,'pos')}${S('Total Diya / Given',a.diya,'neg')}</div>
  <div class="card scroll" style="margin-top:16px"><table><thead><tr><th></th><th class="n">Total</th><th class="n">Paid</th><th class="n">Pending</th><th class="n">Liya</th><th class="n">Diya</th></tr></thead><tbody>
  ${[['Today’s / आज',t],['This month / इस महीने',m]].map(([n,s])=>`<tr><td><b>${n}</b></td><td class="n">${fm(s.total)}</td><td class="n">${fm(s.paid)}</td><td class="n">${fm(s.pend)}</td><td class="n">${fm(s.liya)}</td><td class="n">${fm(s.diya)}</td></tr>`).join('')}</tbody></table></div>`,'dash');
}

/* ---------- ADD / EDIT ---------- */
function formView(id){
  let t=null;
  if(id){t=db.tx.find(x=>x.id==id&&x.user_id==uid);if(!t){location.hash='hist';return}}
  const ty=t?t.transaction_type:'liya',full=!!(t&&(t.product_name||t.pending_amount>0));
  shell(`<h2>${t?'Edit Transaction / बदलें':'Add Transaction / नया लेन-देन'}</h2><form class="card" onsubmit="saveTx(event,${t?t.id:0})">
  <label>Type</label><div class="seg"><label><input type="radio" name="ttype" value="liya" onchange="tt(this.form)" ${ty=='liya'?'checked':''}><span><b>Liya / Received</b><br>लिया</span></label>
  <label><input type="radio" name="ttype" value="diya" onchange="tt(this.form)" ${ty=='diya'?'checked':''}><span><b>Diya / Paid</b><br>दिया</span></label></div>
  <label style="font-weight:400;display:flex;gap:8px;align-items:center;margin-top:12px"><input type="checkbox" name="full" style="width:auto" onchange="tt(this.form)" ${full?'checked':''}> Product ya aadha payment ke saath (Total, Paid, Pending) / सामान या आंशिक भुगतान</label>
  <div id="am"><label>Amount / रकम (₹)</label><input name="amount" type="number" step="0.01" min="0.01" value="${t&&!full?t.total_amount:''}"></div>
  <div id="pf"><label>Product Name / सामान का नाम</label><input name="product" maxlength="150" value="${esc(t&&t.product_name)}"></div>
  <div class="row" id="tp"><div><label>Total Amount / कुल रकम (₹)</label><input name="total" type="number" step="0.01" min="0" value="${t?t.total_amount:''}" oninput="calcP(this.form)"></div>
  <div><label>Paid Amount / दिया-लिया (₹)</label><input name="paid" type="number" step="0.01" min="0" value="${t?t.paid_amount:0}" oninput="calcP(this.form)"></div>
  <div><label>Pending / बाकी (auto)</label><div class="pend" id="pend">₹0</div></div></div>
  <div class="row"><div><label>Payment Type</label><input value="Cash" disabled></div>
  <div><label>Date / तारीख</label><input name="date" type="date" required value="${t?t.transaction_date:day()}"></div></div>
  <label>Note / Description (optional)</label><textarea name="note" rows="2" maxlength="500">${esc(t&&t.note)}</textarea>
  <div class="bar"><button>Save / सेव करें</button><a class="btn alt" href="#hist">Cancel</a></div></form>`,'add');
  tt(document.querySelector('form'));
}
function tt(f){
  const sm=!f.full.checked;
  document.getElementById('am').style.display=sm?'':'none';
  document.getElementById('pf').style.display=sm?'none':'';
  document.getElementById('tp').style.display=sm?'none':'';
  f.amount.disabled=!sm;f.amount.required=sm;
  [f.product,f.total,f.paid].forEach(i=>{i.disabled=sm;i.required=!sm});
  if(!sm)calcP(f);
}
function calcP(f){const p=(+f.total.value||0)-(+f.paid.value||0);const e=document.getElementById('pend');e.textContent=fm(p);e.className='pend '+(p<0?'neg':'')}
function saveTx(e,id){
  e.preventDefault();const f=e.target,liya=!f.full.checked,tot=liya?+f.amount.value:+f.total.value,pd=liya?tot:+f.paid.value;
  if(!(tot>=0)||!(pd>=0)||(liya&&!(tot>0)))return alert('Amount galat hai / रकम गलत है');
  if(pd>tot)return alert('Paid amount total se zyada nahi ho sakta / भुगतान कुल रकम से ज़्यादा नहीं हो सकता');
  const o={transaction_type:f.ttype.value,product_name:liya?'':f.product.value.trim(),total_amount:tot,paid_amount:pd,pending_amount:+(tot-pd).toFixed(2),payment_type:'Cash',transaction_date:f.date.value,note:f.note.value.trim()};
  if(id){const t=db.tx.find(x=>x.id==id&&x.user_id==uid);if(!t)return;Object.assign(t,o,{updated_at:new Date().toISOString()})}
  else db.tx.push(Object.assign({id:db.seq++,user_id:uid},o,{transaction_time:new Date().toTimeString().slice(0,8),created_at:new Date().toISOString(),updated_at:null}));
  save();location.hash='hist';
}
function delTx(id){
  if(!confirm('Is transaction ko delete karein? / क्या यह लेन-देन हटाएँ?'))return;
  db.tx=db.tx.filter(t=>!(t.id==id&&t.user_id==uid));save();drawHist();
}

/* ---------- HISTORY ---------- */
const pn=t=>t.product_name?esc(t.product_name):'<i style="color:var(--mut)">—</i>';
const tag=t=>`<span class="tag ${t.transaction_type}">${t.transaction_type=='liya'?'Liya':'Diya'}</span>`;
function histView(){
  shell(`<h2>Transaction History / इतिहास</h2><div class="card"><div class="row">
  <div><label>Search Product</label><input id="fq" placeholder="Product name…" value="${esc(F.q)}" oninput="F.q=this.value;drawHist()"></div>
  <div><label>From</label><input id="ff" type="date" value="${F.from}" oninput="F.from=this.value;drawHist()"></div>
  <div><label>To</label><input id="ft" type="date" value="${F.to}" oninput="F.to=this.value;drawHist()"></div>
  <div><label>Type</label><select onchange="F.type=this.value;drawHist()"><option value="">All / सभी</option><option value="liya" ${F.type=='liya'?'selected':''}>Liya / Received</option><option value="diya" ${F.type=='diya'?'selected':''}>Diya / Paid</option></select></div></div>
  <div class="bar"><button class="alt" onclick="F={q:'',from:'',to:'',type:''};histView()">Clear filters</button><a class="btn" href="#add">+ Add</a></div></div>
  <div class="card scroll" id="hl"></div>`,'hist');drawHist();
}
function drawHist(){
  const q=F.q.toLowerCase();
  const l=mine().filter(t=>(!q||t.product_name.toLowerCase().includes(q))&&(!F.from||t.transaction_date>=F.from)&&(!F.to||t.transaction_date<=F.to)&&(!F.type||t.transaction_type==F.type));
  const s=sum(l);
  document.getElementById('hl').innerHTML=l.length?`<table><thead><tr><th>Date</th><th>Time</th><th>Type</th><th>Product</th><th class="n">Total</th><th class="n">Paid</th><th class="n">Pending</th><th>Payment</th><th>Note</th><th></th></tr></thead><tbody>
  ${l.map(t=>`<tr><td>${t.transaction_date}</td><td>${t.transaction_time}</td><td>${tag(t)}</td><td>${pn(t)}</td><td class="n">${fm(t.total_amount)}</td><td class="n">${fm(t.paid_amount)}</td><td class="n">${fm(t.pending_amount)}</td><td>${t.payment_type}</td><td>${esc(t.note)}</td>
  <td><a class="btn alt sm" style="padding:4px 10px;font-size:13px" href="#edit-${t.id}">Edit</a> <button class="del sm" onclick="delTx(${t.id})">Delete</button></td></tr>`).join('')}</tbody>
  <tfoot><tr><td colspan="4">Total (${l.length})</td><td class="n">${fm(s.total)}</td><td class="n">${fm(s.paid)}</td><td class="n">${fm(s.pend)}</td><td colspan="3"></td></tr></tfoot></table>`:'<div class="empty">Koi transaction nahi mila / कोई लेन-देन नहीं मिला</div>';
}

/* ---------- REPORTS ---------- */
const RT={daily:'Daily Report / दैनिक',monthly:'Monthly Report / मासिक',received:'Total Received (Liya)',given:'Total Given (Diya)',paid:'Total Paid Report',pending:'Total Pending Report',product:'Product-wise Listing'};
function repData(){
  const l=mine(),k=R.kind;
  if(k=='daily')return l.filter(t=>t.transaction_date==R.d);
  if(k=='monthly')return l.filter(t=>t.transaction_date.startsWith(R.m));
  if(k=='received')return l.filter(t=>t.transaction_type=='liya');
  if(k=='given')return l.filter(t=>t.transaction_type=='diya');
  if(k=='paid')return l.filter(t=>t.paid_amount>0);
  if(k=='pending')return l.filter(t=>t.pending_amount>0);
  return l;
}
function repView(){
  shell(`<h2>Reports / रिपोर्ट</h2><div class="card noprint"><div class="row">
  <div><label>Report</label><select onchange="R.kind=this.value;repView()">${Object.keys(RT).map(k=>`<option value="${k}" ${R.kind==k?'selected':''}>${RT[k]}</option>`).join('')}</select></div>
  ${R.kind=='daily'?`<div><label>Date</label><input type="date" value="${R.d}" onchange="R.d=this.value||day();repView()"></div>`:''}
  ${R.kind=='monthly'?`<div><label>Month</label><input type="month" value="${R.m}" onchange="R.m=this.value||day().slice(0,7);repView()"></div>`:''}</div>
  <div class="bar"><button onclick="window.print()">Print / प्रिंट</button></div></div>
  <div class="card scroll"><div class="printhead">${logo(me(),56)}<div><b style="font-size:20px">${esc(me().account_name)}</b><br>${RT[R.kind]}${R.kind=='daily'?' – '+R.d:R.kind=='monthly'?' – '+R.m:''}<br><small>Generated: ${new Date().toLocaleString('en-IN')}</small></div></div>
  <h3 class="noprint" style="margin-top:0">${RT[R.kind]}</h3>${repTable(repData())}</div>`,'rep');
}
function repTable(l){
  if(!l.length)return '<div class="empty">Is report me koi data nahi / इस रिपोर्ट में कोई डेटा नहीं</div>';
  const s=sum(l);
  if(R.kind=='product'){
    const g={};l.forEach(t=>{const k=t.product_name.trim().toLowerCase();(g[k]=g[k]||{n:t.product_name||'(Udhar – no product)',l:[]}).l.push(t)});
    return `<table><thead><tr><th>Product</th><th class="n">Entries</th><th class="n">Liya</th><th class="n">Diya</th><th class="n">Total</th><th class="n">Paid</th><th class="n">Pending</th></tr></thead><tbody>
    ${Object.values(g).sort((a,b)=>a.n.localeCompare(b.n)).map(x=>{const y=sum(x.l);return `<tr><td>${esc(x.n)}</td><td class="n">${x.l.length}</td><td class="n">${fm(y.liya)}</td><td class="n">${fm(y.diya)}</td><td class="n">${fm(y.total)}</td><td class="n">${fm(y.paid)}</td><td class="n">${fm(y.pend)}</td></tr>`}).join('')}</tbody>
    <tfoot><tr><td>All</td><td class="n">${l.length}</td><td class="n">${fm(s.liya)}</td><td class="n">${fm(s.diya)}</td><td class="n">${fm(s.total)}</td><td class="n">${fm(s.paid)}</td><td class="n">${fm(s.pend)}</td></tr></tfoot></table>`;
  }
  return `<table><thead><tr><th>Date</th><th>Time</th><th>Type</th><th>Product</th><th class="n">Total</th><th class="n">Paid</th><th class="n">Pending</th><th>Note</th></tr></thead><tbody>
  ${l.map(t=>`<tr><td>${t.transaction_date}</td><td>${t.transaction_time}</td><td>${tag(t)}</td><td>${pn(t)}</td><td class="n">${fm(t.total_amount)}</td><td class="n">${fm(t.paid_amount)}</td><td class="n">${fm(t.pending_amount)}</td><td>${esc(t.note)}</td></tr>`).join('')}</tbody>
  <tfoot><tr><td colspan="4">Total (${l.length}) | Liya ${fm(s.liya)} | Diya ${fm(s.diya)}</td><td class="n">${fm(s.total)}</td><td class="n">${fm(s.paid)}</td><td class="n">${fm(s.pend)}</td><td></td></tr></tfoot></table>`;
}

/* ---------- PROFILE / LOGO ---------- */
function profView(){
  const u=me();tmpLogo=null;
  shell(`<h2>Profile / प्रोफ़ाइल</h2><div class="card"><div style="display:flex;gap:16px;align-items:center;flex-wrap:wrap"><div id="prev">${logo(u,120)}</div>
  <div><b style="font-size:20px">${esc(u.account_name)}</b><br><span style="color:var(--mut)">Username: ${esc(u.username)}</span></div></div>
  <label>Upload Logo (JPG, PNG, WEBP – max 2 MB)</label><input type="file" id="lf" accept=".jpg,.jpeg,.png,.webp,image/jpeg,image/png,image/webp" onchange="pickLogo(this)">
  <p class="err" id="lerr"></p><div class="bar"><button id="lb" disabled onclick="saveLogo()">Save Logo</button>${u.logo?'<button class="alt" onclick="rmLogo()">Remove Logo</button>':''}</div></div>
  <form class="card" onsubmit="chPass(event)"><h3 style="margin-top:0">Change Password / पासवर्ड बदलें</h3><div class="row">
  <div><label>Current</label><input name="old" type="password" required autocomplete="current-password"></div>
  <div><label>New (min 6)</label><input name="nw" type="password" minlength="6" required autocomplete="new-password"></div></div>
  <p class="err" id="perr"></p><button>Update Password</button></form>`,'prof');
}
function pickLogo(inp){
  const f=inp.files[0],er=document.getElementById('lerr');er.textContent='';tmpLogo=null;document.getElementById('lb').disabled=true;
  if(!f)return;
  if(!['image/jpeg','image/png','image/webp'].includes(f.type))return er.textContent='Sirf JPG, PNG ya WEBP allowed hai / केवल JPG, PNG, WEBP';
  if(f.size>2*1024*1024)return er.textContent='File 2 MB se badi hai / फ़ाइल 2 MB से बड़ी है';
  const r=new FileReader();
  r.onload=()=>{const im=new Image();im.onload=()=>{
    const k=Math.min(1,320/Math.max(im.width,im.height)),c=document.createElement('canvas');
    c.width=Math.round(im.width*k);c.height=Math.round(im.height*k);c.getContext('2d').drawImage(im,0,0,c.width,c.height);
    tmpLogo=c.toDataURL(f.type=='image/jpeg'?'image/jpeg':'image/png',.9);
    document.getElementById('prev').innerHTML=logo({logo:tmpLogo,account_name:'?'},120);document.getElementById('lb').disabled=false};
    im.onerror=()=>er.textContent='Image kharab hai / इमेज खराब है';im.src=r.result};
  r.readAsDataURL(f);
}
function saveLogo(){if(!tmpLogo)return;me().logo=tmpLogo;save();profView()}
function rmLogo(){if(confirm('Logo hata dein? / लोगो हटाएँ?')){me().logo='';save();profView()}}
async function chPass(e){
  e.preventDefault();const f=e.target,u=me(),er=document.getElementById('perr');
  if(u.password_hash!=await sha(f.old.value))return er.textContent='Purana password galat hai / पुराना पासवर्ड गलत है';
  u.password_hash=await sha(f.nw.value);save();f.reset();er.style.color='var(--grn)';er.textContent='Password badal gaya / पासवर्ड बदल गया';
}
</script>
</body>
</html>
