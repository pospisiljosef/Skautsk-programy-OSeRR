# Skautsk-programy-OSeRR
<!DOCTYPE html>
<html lang="cs">
<head>
  <meta charset="utf-8">
  <title>Skautské programy (Google Drive)</title>
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
  <!-- Google Identity Services Script -->
  <script src="https://accounts.google.com/gsi/client" async defer></script>
  <style>
    :root {
      --bg: #f6f7f9; --card: #fff; --tx: #1d2330; --mut: #667; --bd: #dde1e8; --ac: #6a1b9a;
      box-sizing: border-box; padding-top: env(safe-area-inset-top, 0px); padding-bottom: env(safe-area-inset-bottom, 0px);
    }
    @media (prefers-color-scheme: dark) {
      :root:not([data-theme="light"]) { --bg: #15171c; --card: #1f232b; --tx: #eceef3; --mut: #9aa2b1; --bd: #333946; --ac: #c58be0; }
    }
    * { box-sizing: border-box; } html, body { margin: 0; }
    body { background: var(--bg); color: var(--tx); font: 16px/1.5 system-ui, sans-serif; }
    main { max-width: 760px; margin: 0 auto; padding: 16px; }
    h1 { font-size: 22px; margin: 0 0 4px; } .sub { color: var(--mut); margin: 0 0 14px; font-size: 14px; }
    .card { background: var(--card); border: 1px solid var(--bd); border-radius: 12px; padding: 14px; margin-bottom: 12px; }
    button, input, select, textarea { font: inherit; color: inherit; }
    input, select, textarea { width: 100%; padding: 9px; border: 1px solid var(--bd); border-radius: 8px; background: var(--bg); margin: 4px 0 10px; }
    button { border: 1px solid var(--bd); background: var(--card); padding: 8px 12px; border-radius: 8px; cursor: pointer; }
    button.pri { background: var(--ac); color: #fff; border-color: var(--ac); }
    @media (prefers-color-scheme: dark) {
      :root:not([data-theme="light"]) button.pri { color: #111; }
    }
    .row { display: flex; gap: 8px; flex-wrap: wrap; align-items: center; }
    .chip { display: inline-flex; align-items: center; gap: 5px; padding: 3px 9px; border-radius: 99px; font-size: 12px; border: 1px solid var(--bd); background: var(--bg); cursor: pointer; user-select: none; }
    .chip.on { color: #fff; border-color: transparent; }
    .dot { width: 10px; height: 10px; border-radius: 50%; display: inline-block; }
    .tag { font-size: 12px; padding: 2px 8px; border-radius: 99px; background: var(--ac); color: #fff; }
    @media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) .tag { color: #111; } }
    .meta { color: var(--mut); font-size: 13px; }
    .stars button { border: 0; background: none; font-size: 22px; padding: 0 2px; color: #e0a800; }
    .body { white-space: pre-wrap; margin: 8px 0; }
    .hide { display: none; } .grid { display: grid; grid-template-columns: 1fr 1fr; gap: 0 10px; }
    .grid label { font-size: 13px; color: var(--mut); } .grid select { margin-bottom: 8px; }
    .sec { font-size: 13px; color: var(--mut); margin: 8px 0 2px; } a { color: var(--ac); }
    .el { display: flex; gap: 8px; align-items: center; margin: 2px 0; } .el input { width: auto; margin: 0; }
  </style>
</head>
<body>
<main>
  <h1>⚜️ Skautské programy</h1>
  <p class="sub">Sdílejte a hodnoťte programy oddílů propojené s vaším Google Diskem.</p>

  <!-- KARTA PŘIHLÁŠENÍ / ODHLÁŠENÍ -->
  <div class="card" id="authCard">
    <div id="loggedOutState">
      <b>Nejste přihlášen ke Google Účtu</b>
      <p class="meta">Pro přidávání, úpravy a hodnocení se přihlaste pomocí Google.</p>
      <button class="pri" id="loginBtn">🔑 Přihlásit se přes Google</button>
    </div>
    <div id="loggedInState" class="hide">
      <b>Přihlášený uživatel: <span id="userEmail"></span></b>
      <div class="row" style="margin-top: 8px;">
        <button id="logoutBtn">🚪 Odhlásit se</button>
      </div>
    </div>
  </div>

  <p class="meta" id="msg"></p>
  <div class="row" style="margin-bottom:12px">
    <button class="pri" id="addBtn">+ Přidat program</button>
    <button id="profBtn">👤 Můj profil</button>
  </div>

  <!-- PROFIL -->
  <div class="card hide" id="prof">
    <div class="meta">Tyto údaje se budou automaticky přidávat k vašim novým programům. Vidíte je jen vy.</div>
    <label>Přezdívka / jméno<input id="p-name" maxlength="60"></label>
    <label>Oddíl / středisko<input id="p-unit" maxlength="60"></label>
    <div class="row"><button class="pri" id="pSave">Uložit profil</button><span class="meta" id="pMsg"></span></div>
  </div>

  <!-- FORMULÁŘ PROGRAMU -->
  <div class="card hide" id="form">
    <label>Název programu<input id="f-title" maxlength="120"></label>
    <label>Kategorie<select id="f-cat"><option>Vlčata</option><option>Skauti</option><option>Roveři</option></select></label>
    <div class="grid">
      <label>Délka<select id="f-dur"></select></label>
      <label>Náročnost přípravy<select id="f-prep"><option value="1">Nízká</option><option value="2">Střední</option><option value="3">Vysoká</option></select></label>
      <label>Fyzická náročnost (1–10)<select id="f-phys" class="n10"></select></label>
      <label>Duševní náročnost (1–10)<select id="f-ment" class="n10"></select></label>
      <label>Tvořivost (1–10)<select id="f-crea" class="n10"></select></label>
      <label>Prostředí<select id="f-place"><option value="out">Venku</option><option value="in">Uvnitř</option><option value="both">Obojí</option></select></label>
    </div>
    <div class="meta">Vhodné počasí</div>
    <div id="f-wx" style="margin:4px 0 10px"></div>
    <label>Odkaz (PDF, Google dokument, Word…) – nepovinné<input id="f-link" placeholder="https://"></label>
    <label>Popis / program přímo textem<textarea id="f-text" rows="6"></textarea></label>
    <div class="meta">Které prvky výchovné metody program naplňuje?</div>
    <div id="f-els" style="margin:6px 0 10px"></div>
    <label>Autor (předvyplněno z profilu)<input id="f-author" maxlength="60"></label>
    <div class="row"><button class="pri" id="save">Uložit</button><button id="cancel">Zrušit</button></div>
    <div class="meta" id="err" style="color:#c0392b"></div>
  </div>

  <!-- FILTRY A SEZNAM -->
  <div class="card">
    <div class="row">
      <select id="q-cat" style="flex:1;margin:0"><option value="">Všechny kategorie</option><option>Vlčata</option><option>Skauti</option><option>Roveři</option></select>
      <select id="q-sort" style="flex:1;margin:0"><option value="new">Nejnovější</option><option value="rate">Nejlépe hodnocené</option></select>
    </div>
    <div class="sec">Délka (od – do)</div>
    <div class="grid"><select id="q-dmin"></select><select id="q-dmax"></select></div>
    <div class="grid">
      <label>Příprava<select id="q-prep"><option value="">Jakákoli</option><option value="1">Nízká</option><option value="2">Střední</option><option value="3">Vysoká</option></select></label>
      <label>Prostředí<select id="q-place"><option value="">Jakékoli</option><option value="out">Venku</option><option value="in">Uvnitř</option><option value="both">Obojí</option></select></label>
    </div>
    <div class="sec">Fyzická náročnost</div><select id="q-phys" class="rg" style="margin-bottom:8px"></select>
    <div class="sec">Duševní náročnost</div><select id="q-ment" class="rg" style="margin-bottom:8px"></select>
    <div class="sec">Tvořivost</div><select id="q-crea" class="rg" style="margin-bottom:8px"></select>
    <div class="sec">Vhodné počasí</div><div class="row" id="q-wx" style="margin-bottom:6px"></div>
    <div class="meta" style="margin:10px 0 4px">Filtr podle prvku metody:</div>
    <div class="row" id="q-els"></div>
  </div>
  <div id="list"></div>
  <p class="meta" id="status"></p>
</main>

<script>
// --- KONFIGURACE GOOGLE OAUTH ---
// Zde je nutné vložit váš Client ID z Google Cloud Console
const GOOGLE_CLIENT_ID = 'VASE_GOOGLE_CLIENT_ID.apps.googleusercontent.com';
const SCOPES = 'https://www.googleapis.com/auth/drive.file https://www.googleapis.com/auth/userinfo.email';

let accessToken = null;
let userEmail = null;
let fileId = null; // ID souboru na Google Disku

// Databázová struktura na Google Disku
let appData = {
  profile: { name: '', unit: '' },
  programs: [],
  ratings: {}
};

const ELS=[["slib","Skautský slib a zákon","#6a1b9a"],["spol","Zapojení do společnosti","#00aeef"],["uc","Učení se činností","#c8c000"],["os","Osobní rozvoj","#ff0090"],["dr","Družinový systém","#0082a6"],["dos","Podpora dospělých","#f00"],["sym","Symbolický rámec","#ff6a00"],["pri","Příroda","#009a44"]];
const E=Object.fromEntries(ELS.map(e=>[e[0],e]));
const $=id=>document.getElementById(id);
let editId=null, fEls=new Set(), fWx=new Set();
const WX=[["any","Jakékoli","#889"],["sun","Slunečno","#e0a800"],["rain","Déšť","#3b82c4"],["cold","Zima / sníh","#5bb"],["hot","Horko","#e5603b"]];
const PL={out:"Venku",in:"Uvnitř",both:"Venku i uvnitř"},PREP={1:"nízká",2:"střední",3:"vysoká"};

function fmtDur(m){const h=Math.floor(m/60),r=m%60;return (h?h+' h ':'')+(r?r+' min':'')||'0'}
function fillSel(el,vals,fmt,first){el.innerHTML=(first?'<option value="">'+first+'</option>':'')+vals.map(v=>'<option value="'+v+'">'+fmt(v)+'</option>').join('')}
const DURS=Array.from({length:12},(_,i)=>(i+1)*15),N10=Array.from({length:10},(_,i)=>i+1);
fillSel($('f-dur'),DURS,fmtDur);fillSel($('q-dmin'),DURS,fmtDur,'Od: libovolně');fillSel($('q-dmax'),DURS,fmtDur,'Do: libovolně');
document.querySelectorAll('.n10').forEach(e=>{fillSel(e,N10,String);e.value=5});
document.querySelectorAll('.rg').forEach(e=>fillSel(e,N10,String,'Libovolná'));
WX.forEach(w=>{$('q-wx').appendChild(chip(w,fWx,render));
 const l=document.createElement('label');l.className='el';l.innerHTML='<input type="checkbox" value="'+w[0]+'">'+esc(w[1]);$('f-wx').appendChild(l)});
document.querySelectorAll('#q-dmin,#q-dmax,#q-prep,#q-place,.rg').forEach(e=>e.onchange=render);
function esc(s){const d=document.createElement('div');d.textContent=s==null?'':String(s);return d.innerHTML}
function chip(e,set,onchg){const c=document.createElement('span');c.className='chip';c.innerHTML='<span class="dot" style="background:'+e[2]+'"></span>'+esc(e[1]);
 c.onclick=()=>{set.has(e[0])?set.delete(e[0]):set.add(e[0]);c.classList.toggle('on',set.has(e[0]));c.style.background=set.has(e[0])?e[2]:'';onchg&&onchg()};return c}
ELS.forEach(e=>{$('q-els').appendChild(chip(e,fEls,render));
 const l=document.createElement('label');l.className='el';l.innerHTML='<input type="checkbox" value="'+e[0]+'"><span class="dot" style="background:'+e[2]+'"></span>'+esc(e[1]);$('f-els').appendChild(l)});

function authorStr(){return [appData.profile.name,appData.profile.unit].filter(Boolean).join(' · ')}

// --- GOOGLE AUTHENTICATION ---
let tokenClient;

function initGoogleAuth() {
  tokenClient = google.accounts.oauth2.initTokenClient({
    client_id: GOOGLE_CLIENT_ID,
    scope: SCOPES,
    callback: async (response) => {
      if (response.error) return;
      accessToken = response.access_token;
      await fetchUserInfo();
      await loadDriveData();
      updateAuthUI();
    },
  });
}

$('loginBtn').onclick = () => tokenClient.requestAccessToken();

$('logoutBtn').onclick = () => {
  if (accessToken) {
    google.accounts.oauth2.revoke(accessToken, () => {
      accessToken = null;
      userEmail = null;
      appData = { profile: { name: '', unit: '' }, programs: [], ratings: {} };
      updateAuthUI();
      render();
    });
  }
};

async function fetchUserInfo() {
  const res = await fetch('https://www.googleapis.com/oauth2/v2/userinfo', {
    headers: { Authorization: `Bearer ${accessToken}` }
  });
  const info = await res.json();
  userEmail = info.email;
}

function updateAuthUI() {
  if (accessToken) {
    $('loggedOutState').classList.add('hide');
    $('loggedInState').classList.remove('hide');$('userEmail').textContent = userEmail;
    $('msg').textContent = 'Přihlášeno. Vaše data se synchronizují s Google Diskem.';
  } else {
    $('loggedOutState').classList.remove('hide');
    $('loggedInState').classList.add('hide');$('msg').textContent = 'Nejste přihlášeni.';
  }
}

// --- GOOGLE DRIVE INTEGRACE ---
async function loadDriveData() {
  $('msg').textContent = 'Načítání dat z Google Disku...';
  // Hledání souboru skautske_programy.json
  const searchRes = await fetch("https://www.googleapis.com/drive/v3/files?q=name='skautske_programy.json' and trashed=false", {
    headers: { Authorization: `Bearer ${accessToken}` }
  });
  const searchData = await searchRes.json();

  if (searchData.files && searchData.files.length > 0) {
    fileId = searchData.files[0].id;
    const fileRes = await fetch(`https://www.googleapis.com/drive/v3/files/${fileId}?alt=media`, {
      headers: { Authorization: `Bearer ${accessToken}` }
    });
    appData = await fileRes.json();
  } else {
    // Soubor ještě neexistuje, vytvoříme nový
    await saveDriveData();
  }
  $('p-name').value = appData.profile.name || '';
  $('p-unit').value = appData.profile.unit \vert{}\vert{} '';$('msg').textContent = 'Data byla úspěšně načtena.';
  render();
}

async function saveDriveData() {
  $('msg').textContent = 'Ukládání na Google Disk...';
  const content = JSON.stringify(appData, null, 2);
  const blob = new Blob([content], { type: 'application/json' });

  if (fileId) {
    // Aktualizace stávajícího souboru
    await fetch(`https://www.googleapis.com/upload/drive/v3/files/${fileId}?uploadType=media`, {
      method: 'PATCH',
      headers: { Authorization: `Bearer ${accessToken}`, 'Content-Type': 'application/json' },
      body: content
    });
  } else {
    // Vytvoření nového souboru
    const metadata = { name: 'skautske_programy.json', mimeType: 'application/json' };
    const form = new FormData();
    form.append('metadata', new Blob([JSON.stringify(metadata)], { type: 'application/json' }));
    form.append('file', blob);

    const res = await fetch('https://www.googleapis.com/upload/drive/v3/files?uploadType=multipart', {
      method: 'POST',
      headers: { Authorization: `Bearer ${accessToken}` },
      body: form
    });
    const created = await res.json();
    fileId = created.id;
  }
  $('msg').textContent = 'Uloženo na Google Disk.';
}

// --- AKCE PROFILU A FORMULÁŘE ---
$('profBtn').onclick=()=>{if(!accessToken){$('msg').textContent='Profil si můžete nastavit po přihlášení.';return}$('prof').classList.toggle('hide')};$('pSave').onclick=async()=>{
  appData.profile = { name: $('p-name').value.trim(), unit:$('p-unit').value.trim() };
  await saveDriveData();
  $('pMsg').textContent='Uloženo.';
};

$('addBtn').onclick=()=>{if(!accessToken){$('msg').textContent='Pro přidání programu se musíte přihlásit.';return}editId=null;$('f-author').value=authorStr();$('save').textContent='Uložit';$('form').classList.toggle('hide')};
$('cancel').onclick=()=>{editId=null;$('form').classList.add('hide')};
$('q-cat').onchange=render;$('q-sort').onchange=render;

function edit(p){
  editId=p.id;$('f-title').value=p.title||'';$('f-cat').value=p.cat;$('f-link').value=p.link||'';$('f-text').value=p.text\vert{}\vert{}'';$('f-author').value=p.author||'';
  if(p.dur){$('f-dur').value=p.dur;$('f-prep').value=p.prep;$('f-phys').value=p.phys;$('f-ment').value=p.ment;$('f-crea').value=p.crea;$('f-place').value=p.place}$('f-els').querySelectorAll('input').forEach(i=>i.checked=(p.els||[]).includes(i.value));
  $('f-wx').querySelectorAll('input').forEach(i=>i.checked=(p.wx\vert{}\vert{}[]).includes(i.value));$('save').textContent='Uložit změny';$('form').classList.remove('hide');$('form').scrollIntoView({behavior:'smooth'});
}

function avg(id){const r=Object.values(appData.ratings[id]||{});return r.length?[r.reduce((a,b)=>a+b,0)/r.length,r.length]:[0,0]}
function safeUrl(u){try{const x=new URL(u);return /^https?:$/.test(x.protocol)?x.href:null}catch(e){return null}}

function render(){
  const V=id=>$(id).value,inR=(v,x)=>!x||v===+x;
  let l=(appData.programs||[]).filter(p=>(!V('q-cat')||p.cat===V('q-cat'))&&[...fEls].every(k=>(p.els||[]).includes(k))
    &&(!V('q-dmin')||(p.dur!=null&&p.dur>=+V('q-dmin')))&&(!V('q-dmax')||(p.dur!=null&&p.dur<=+V('q-dmax')))
    &&(!V('q-prep')||p.prep==V('q-prep'))&&(!V('q-place')||p.place===V('q-place')||p.place==='both')
    &&inR(p.phys,V('q-phys'))&&inR(p.ment,V('q-ment'))&&inR(p.crea,V('q-crea'))
    &&[...fWx].every(k=>(p.wx||[]).includes(k)||(p.wx||[]).includes('any')));
  
  $('status').textContent=l.length+' z '+(appData.programs||[]).length+' programů';
  l.sort($('q-sort').value==='rate'?(a,b)=>avg(b.id)[0]-avg(a.id)[0]:(a,b)=>(b.ts||0)-(a.ts||0));
  const box=$('list');box.innerHTML='';
  if(!l.length){box.innerHTML='<div class="card meta">Zatím tu nic není. Přidejte první program!</div>';return}

  l.forEach(p=>{
    const [a,n]=avg(p.id),mine=(appData.ratings[p.id]||{})[userEmail]||0,url=p.link&&safeUrl(p.link);
    const d=document.createElement('div');d.className='card';
    d.innerHTML='<div class="row" style="justify-content:space-between"><b>'+esc(p.title)+'</b><span class="tag">'+esc(p.cat)+'</span></div>'
     +'<div class="meta">'+esc(p.author||'anonym')+' · '+(p.ts?new Date(p.ts).toLocaleDateString('cs'):'')+' · ★ '+(n?a.toFixed(1)+' ('+n+')':'bez hodnocení')+'</div>'
     +'<div class="row" style="margin:8px 0">'+(p.els||[]).map(k=>E[k]?'<span class="chip" style="cursor:default"><span class="dot" style="background:'+E[k][2]+'"></span>'+esc(E[k][1])+'</span>':'').join('')+'</div>'
     +(p.dur?'<div class="meta" style="line-height:1.7">⏱ '+fmtDur(p.dur)+' · 🧰 příprava: '+(PREP[p.prep]||'?')+' · '+(PL[p.place]||'')+'<br>💪 fyzická '+p.phys+'/10 · 🧠 duševní '+p.ment+'/10 · 🎨 tvořivost '+p.crea+'/10'+((p.wx||[]).length?'<br>🌤 '+p.wx.map(k=>(WX.find(w=>w[0]===k)||[0,k])[1]).join(', '):'')+'</div>':'')
     +(url?'<div><a href="'+esc(url)+'" target="_blank" rel="noopener noreferrer">📎 Otevřít dokument</a></div>':'')
     +(p.text?'<div class="body">'+esc(p.text)+'</div>':'')
     +'<div class="stars" data-id="'+esc(p.id)+'">'+[1,2,3,4,5].map(i=>'<button data-s="'+i+'" title="Ohodnotit">'+(i<=mine?'★':'☆')+'</button>').join('')+'</div>';
    
    d.querySelectorAll('.stars button').forEach(b=>b.onclick=()=>rate(p.id,+b.dataset.s));
    
    // Možnost upravit/smazat (pokud je uživatel autor)
    if(accessToken && p.authorId === userEmail){
      const a=document.createElement('div');a.className='row';a.style.marginTop='8px';
      const eb=document.createElement('button');eb.textContent='✏️ Upravit';eb.onclick=()=>edit(p);
      const db2=document.createElement('button');db2.textContent='🗑 Smazat';let armed=false;
      db2.onclick=async()=>{
        if(!armed){armed=true;db2.textContent='Opravdu smazat?';setTimeout(()=>{armed=false;db2.textContent='🗑 Smazat'},4000);return}
        appData.programs = appData.programs.filter(x=>x.id!==p.id);
        await saveDriveData();
        render();
      };
      a.append(eb,db2);d.appendChild(a);
    }
    box.appendChild(d);
  });
}

async function rate(pid,s){
  if(!accessToken){$('msg').textContent='Hodnotit můžete jen po přihlášení.';return}
  if(!appData.ratings[pid]) appData.ratings[pid] = {};
  appData.ratings[pid][userEmail] = s;
  await saveDriveData();
  render();
}

$('save').onclick=async()=>{
  const title=$('f-title').value.trim(),link=$('f-link').value.trim(),text=$('f-text').value.trim();
  if(!title||(!link&&!text)){$('err').textContent='Vyplňte název a buď odkaz, nebo text programu.';return}
  if(link&&!safeUrl(link)){$('err').textContent='Odkaz musí začínat http:// nebo https://';return}
  if(!accessToken){$('err').textContent='Nejste přihlášen.';return}

  const p={
    id: editId || ('p' + Date.now() + Math.random().toString(36).slice(2,6)),
    title, cat:$('f-cat').value, link, text, author:$('f-author').value.trim(), ts:Date.now(), authorId:userEmail,
    dur:+$('f-dur').value, prep:+$('f-prep').value, phys:+$('f-phys').value, ment:+$('f-ment').value, crea:+$('f-crea').value, place:$('f-place').value,
    wx:[...$('f-wx').querySelectorAll('input:checked')].map(i=>i.value),
    els:[...$('f-els').querySelectorAll('input:checked')].map(i=>i.value)
  };

  $('err').textContent='';
  if(editId){
    const idx = appData.programs.findIndex(x=>x.id===editId);
    if(idx !== -1) appData.programs[idx] = { ...appData.programs[idx], ...p, editedAt: Date.now() };
    editId = null;
  } else {
    appData.programs.push(p);
  }

  await saveDriveData();
  $('form').classList.add('hide');
  ['f-title','f-link','f-text'].forEach(i=>$(i).value='');
  $('f-els').querySelectorAll('input').forEach(i=>i.checked=false);$('f-wx').querySelectorAll('input').forEach(i=>i.checked=false);
  render();
};

window.onload = () => {
  initGoogleAuth();
  render();
};
</script>
</body>
</html>
