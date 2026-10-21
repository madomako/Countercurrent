# Countercurrent
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8"><meta name="viewport" content="width=device-width,initial-scale=1">
<title>Countercurrent and Concurrent Heat Exchange</title>
<style>
:root{color-scheme:light;--red:#bb514b;--blue:#347bb9;--border:#dedede;--ink:#282828}
*{box-sizing:border-box}body{font-family:system-ui,-apple-system,Segoe UI,Arial,sans-serif;margin:0;background:white;color:var(--ink)}main{max-width:720px;margin:auto;padding:18px 20px 24px}h1{font-size:21px;margin:0 0 8px;font-weight:650}p{font-size:13px;color:#686868;margin:5px 0 18px}.where{display:flex;justify-content:space-between;font-size:12px;font-weight:650;letter-spacing:.02em;margin:0 0 8px}.where span:last-child{text-align:right}label{font-size:14px;font-weight:600}input[type=range]{width:100%;accent-color:#687e9b;margin:12px 0 5px}.sliderrow{display:flex;justify-content:space-between;gap:12px;align-items:baseline}.panel{border:1px solid var(--border);border-radius:11px;padding:13px 15px 12px;margin-top:14px}h2{font-size:15px;font-weight:650;margin:0 0 8px}.graphic{width:100%;display:block;overflow:visible}.metrics{display:flex;gap:8px;flex-wrap:wrap;margin-top:2px}.metric{background:#f5f5f5;border-radius:7px;padding:8px 11px;flex:1;min-width:130px}.metric small{display:block;color:#646464;font-size:11px}.metric strong{font-size:17px;font-weight:600;font-variant-numeric:tabular-nums}.red{color:var(--red)}.blue{color:var(--blue)}.footer{font-size:12px;color:#686868;line-height:1.45;margin-top:15px}svg text{font-family:system-ui,-apple-system,Segoe UI,Arial,sans-serif}svg .pointer{stroke:#555;stroke-width:1.3;stroke-dasharray:4 4}svg .heat{stroke:#9b7b53;stroke-width:1.2;stroke-dasharray:2 3;opacity:.7}@media(max-width:400px){main{padding:12px}.panel{padding:10px}}
</style>
</head>
<body>
<main>
<h1>Countercurrent vs. concurrent heat exchange</h1>
<p>Move the slider to compare the temperature difference between adjacent artery and vein segments.</p>
<div class="where"><span>BODY CORE</span><span>BODY EXTREMITY</span></div>
<div class="sliderrow"><label for="position">Position along the extremity</label><output id="positionLabel" for="position">0%</output></div>
<input id="position" type="range" min="0" max="5" step="1" value="0" aria-label="Position along the extremity">
<section class="panel" aria-labelledby="countertitle"><h2 id="countertitle">Countercurrent — small gradients are maintained</h2><svg id="countergraphic" class="graphic" viewBox="0 0 340 111" role="img" aria-label="Arterial blood flows from the body core to the extremity, while venous blood flows toward the core. At each location arterial blood is one degree warmer."></svg><div class="metrics" id="countermetrics" aria-live="polite"></div></section>
<section class="panel" aria-labelledby="concurrenttitle"><h2 id="concurrenttitle">Concurrent — gradients disappear</h2><svg id="concurrentgraphic" class="graphic" viewBox="0 0 340 111" role="img" aria-label="Artery and vein flow in the same direction, and temperatures become equal along the exchanger."></svg><div class="metrics" id="concurrentmetrics" aria-live="polite"></div></section>
<div class="footer">Illustrative temperatures from the supplied teaching diagram (°C). Countercurrent exchange maintains a local gradient; concurrent flow loses it as the two streams approach the same temperature. Concurrent flow is a hypothetical comparison, not the usual anatomy of arterial and venous return.</div>
</main>
<script>
(()=>{
'use strict';
const sets={counter:{artery:[33,32,31,30,29,28],vein:[32,31,30,29,28,27],veinDirection:'left'},concurrent:{artery:[33,31,30,30,30,30],vein:[27,29,30,30,30,30],veinDirection:'right'}};
const ns='http://www.w3.org/2000/svg';
const slider=document.getElementById('position');
function el(tag,attrs={},text){const node=document.createElementNS(ns,tag);Object.entries(attrs).forEach(([k,v])=>node.setAttribute(k,String(v)));if(text!==undefined)node.textContent=text;return node}
function path(svg,d,color){svg.append(el('path',{d,stroke:color,'stroke-width':2.5,fill:'none','stroke-linecap':'round','stroke-linejoin':'round'}))}
function renderGraphic(id,info,i){const svg=document.getElementById(id+'graphic');svg.replaceChildren();
svg.append(el('rect',{x:15,y:15,width:310,height:33,rx:5,fill:'#bb514b','fill-opacity':'.15'}));
svg.append(el('rect',{x:15,y:64,width:310,height:33,rx:5,fill:'#347bb9','fill-opacity':'.15'}));
// Flow direction arrows
path(svg,'M24 41 H316 M309 35 L316 41 L309 47','#bb514b');
if(info.veinDirection==='left')path(svg,'M316 90 H24 M31 84 L24 90 L31 96','#347bb9');
else path(svg,'M24 90 H316 M309 84 L316 90 L309 96','#347bb9');
const xs=[27,84,141,198,255,313];
for(let n=0;n<6;n++){
 const anchor=n===0?'start':n===5?'end':'middle';
 svg.append(el('text',{x:xs[n],y:28,'font-size':11.5,'text-anchor':anchor,fill:'#9a3f3a','font-weight':600},info.artery[n]+'°'));
 svg.append(el('text',{x:xs[n],y:77,'font-size':11.5,'text-anchor':anchor,fill:'#236fa9','font-weight':600},info.vein[n]+'°'));
}
for(let n=0;n<6;n++){let x=xs[n];svg.append(el('path',{d:`M${x} 49 V62`,class:'heat'}))}
const x=xs[i];svg.append(el('line',{x1:x,x2:x,y1:7,y2:102,class:'pointer'}));
svg.append(el('circle',{cx:x,cy:41,r:4.4,fill:'#bb514b',stroke:'white','stroke-width':1}));
svg.append(el('circle',{cx:x,cy:90,r:4.4,fill:'#347bb9',stroke:'white','stroke-width':1}));
}
function render(){const i=Number(slider.value);document.getElementById('positionLabel').textContent=Math.round(i/5*100)+'%';
for(const [id,d] of Object.entries(sets)){
 renderGraphic(id,d,i);
 const diff=d.artery[i]-d.vein[i];
 document.getElementById(id+'metrics').innerHTML=`<div class="metric"><small>Artery</small><strong class="red">${d.artery[i]}°C</strong></div><div class="metric"><small>Vein</small><strong class="blue">${d.vein[i]}°C</strong></div><div class="metric"><small>Local difference</small><strong>${diff}°C</strong></div>`;
}}
slider.addEventListener('input',render);render();
})();
</script>
</body></html>
