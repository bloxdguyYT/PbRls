# PbRls
Code and example of The PbRl system

# Library
In the Worldcode Editor click on the Green button with a + symbol on the right side of the text: "Files".
Call the new file PbRls.ts
Press Create and copy this code into the Document:
```ts
/*
Credits to [Kinetix_] for the RLS Code.
License under CC BY 4.0
Credits to [_bloxd_guy] for the PbLb Code.
License under CC BY 4.0
*/

const SP=["head","skin","body","legs","shoes","cape","eyes","hair","eyebrows","hat"],LS=3,C=["rgba(255, 215, 0, 1)","rgba(192, 192, 192, 1)","rgba(205, 127, 50, 1)"],ST={},E=s=>String(s==null?"":s).replace(/[% ,|]/g,c=>"%"+c.charCodeAt(0).toString(16)),U=s=>s.replace(/%(25|20|2c|7c)/g,(_,h)=>String.fromCharCode(parseInt(h,16))),Q=p=>({point:p,lerpMode:"catmull-rom-spline"}),HA={animationDurationMs:1000,loop:!0,nodeAnimations:{ArmLeftMesh:{timeline:[{timeFraction:0,position:Q([.1,0,0]),rotation:Q([0,0,2.7925])},{timeFraction:.5,rotation:Q([0,0,2.618])},{timeFraction:1,rotation:Q([0,0,2.7925])}]},ArmRightMesh:{timeline:[{timeFraction:0,position:Q([0,0,0]),rotation:Q([0,0,-.1745])},{timeFraction:.5,rotation:Q([0,0,-.2618])},{timeFraction:1,rotation:Q([0,0,-.1745])}]}}},AH=e=>api.animateEntity(e,HA,0,.1),BD=(c,s)=>({colour:c,style:s,width:"3px",applyTo:"nametag"}),NT=(e,o)=>api.setTargetedPlayerSettingForEveryone(e,"nameTagInfo",Object.assign({minLighting:1,healthbar:{display:"never"}},o),!0),IB=()=>api.attemptCreateMeshEntity("BloxdBlock",{blockName:"Invisible Solid",size:0},"RLS"),PE=o=>api.attemptCreateMeshEntity("ParticleEmitter",Object.assign({blendMode:0,dir1:[-1,-1,-1],dir2:[1,1,1],height:.5,width:1,depth:1,emitterShape:"ring",meshOffset:[0,0,0],autoRotate:!1,followTargetMeshOffset:!0},o),"RLS"),sv=v=>{let s=arrToStr(v);s!=(api.getLobbyDbValue("LB")||"")&&api.setLobbyDbValue("LB",s);for(let q of api.getPlayerIds())api.getPlayerDbValue(q,"LB")!=s&&api.setPlayerDbValue(q,"LB",s)};

export function strToArr(s:String){return(s||"").split(",").filter(p=>p).map(p=>{let[a,b,c]=p.split(" ");return{player:a,value:+b,skin:c?c.split("|").map(U):null}}).filter(x=>x.player!="empty")}
export function arrToStr(a:[]){return a.map(x=>x.player+" "+x.value+(x.skin?" "+x.skin.map(E).join("|"):"")+",").join("")}
export function getLb(){return strToArr(api.getLobbyDbValue("LB"))}
export function getSkin(id:String){return SP.map(p=>api.getPlayerCosmetic(id,p))}
export function updLb(id:String,p=0){let a=strToArr(api.getPlayerDbValue(id,"LB")),b=getLb(),v=[...new Map((p?[...b,...a]:[...a,...b]).map(x=>[x.player,x])).values()].sort((x,y)=>y.value-x.value).slice(0,LS);sv(v);return v}
export function setup(id:String){updLb(id)}
export function rankPlayers(l:[]){let m=new Map(getLb().map(x=>[x.player,x]));for(let[i,w]of l){let n=api.getEntityName(i);m.set(n,{player:n,value:+w||0,id:i})}let v=[...m.values()].sort((x,y)=>y.value-x.value).slice(0,LS);for(let x of v)if(x.id!=null){x.skin=getSkin(x.id);delete x.id}sv(v);return v}
export function rankPlayer(id:String,v:Number){return rankPlayers([[id,v]])}  
export function feedLb(g){return rankPlayers(api.getPlayerIds().map(i=>[i,g(i)]))}

export function removeNametag(id:String){api.setTargetedPlayerSettingForEveryone(id,"nameTagInfo",{content:[{str:"",style:{color:"rgba(0, 0, 0, 0)"}}],backgroundColor:"rgba(0, 0, 0, 0)"},!0)}
export function deleteAllEntities(){for(let e of api.getEntitiesInRect([-1e9,-1e9,-1e9],[1e9,1e9,1e9]))try{api.getEntityName(e)==="RLS"&&api.deleteEntity(e)}catch(_){}}

export function updateLbStages(k){let c=lbStageConfig[k],I=lbStageConfig.lbStageNtIds[k];if(!c||!I)return;feedLb(c.getValue);let b=getLb();for(let i=1;i<4;i++){let e=I[i];if(!e)continue;let o=C[i-1],d=b[i-1];NT(e,{content:[{str:d?"#"+i+"  "+d.player:"NONE",style:{color:o}}],subtitle:[{str:k+". "+(d?d.value:0),style:{color:o}}],border:BD(o,"glow")});if(d){let s=d.skin?d.skin.join("|"):"d";if(ST[e]!==s){let t={};d.skin&&SP.forEach((p,j)=>{d.skin[j]&&(t[p]=d.skin[j])});api.updateMeshEntity(e,"Person",{textures:t,size:1,pose:"standing"});c.trophy==="held"&&AH(e);ST[e]=s}}else if(ST[e]!="n"){api.updateMeshEntity(e,"BloxdBlock",{size:1,blockName:"Lucky Block"});ST[e]="n"}}}
export function createLbStages(k){let c=lbStageConfig[k];if(!c)return;let g=[[255,215,0,1],[192,192,192,1],[205,127,50,1]],T=["Gold","Iron","Wood"],I=lbStageConfig.lbStageNtIds[k]={};for(let i=1;i<4;i++){let p=c["p"+i];if(!p)continue;let e=api.attemptCreateMeshEntity("Person",{size:1,pose:"standing"},"RLS");api.setPosition(e,p);c.trophy==="held"&&AH(e);let q=PE({texture:"glint",minLifeTime:.5,maxLifeTime:.5,minEmitPower:1.5,maxEmitPower:2,minSize:.15,maxSize:.25,gravity:[0,1,0],velocityGradients:[{timeFraction:0,factor:.1,factor2:.1},{timeFraction:1,factor:.5,factor2:.5}],colorGradients:[{timeFraction:0,minColor:g[i-1],maxColor:g[i-1]}],hideDist:100,emitRate:30});api.setPosition(q,[p[0],p[1]+.3,p[2]]);removeNametag(q);I[i]=e;if(c.trophy==="none")continue;let t=api.attemptCreateMeshEntity("BloxdBlock",{blockName:"INTERNAL_MESH_"+T[i-1]+" Trophy",size:3.5},"RLS");removeNametag(t);api.setPosition(t,c.trophy==="held"?[p[0]-1,p[1]-.4,p[2]]:[p[0],p[1]+.5,p[2]])}}

export function initLbNts(){if(globalThis.isInit)return;globalThis.isInit=!0;deleteAllEntities();for(let k of Object.keys(lbNtConfig))k!="lbNtIds"&&(lbNtConfig.lbNtIds[k]={},createLbNts(k));for(let k of Object.keys(lbStageConfig))k!="lbStageNtIds"&&k!="animated"&&createLbStages(k)}
export function createLbNts(k){let c=lbNtConfig[k];if(!c)return;let n=c.topPC,P=[...c.pos],h=IB();P[1]+=n*.4;NT(h,{content:[{str:"TOP PLAYERS - ",style:{color:"cyan",fontSize:"60px"}},{str:k,style:{color:"cyan",fontSize:"60px"}}],border:BD("cyan","glow")});api.setPosition(h,P);let y=P[1]-.4;for(let i=1;i<=n;i++){let e=IB();api.setPosition(e,[P[0],y,P[2]]);lbNtConfig.lbNtIds[k][i]=e;y-=.4}let q=PE({texture:"square_particle",minLifeTime:Math.floor(n/10),maxLifeTime:Math.ceil(n/7.5),minEmitPower:3,maxEmitPower:3,minSize:.1,maxSize:.15,gravity:[0,20,0],velocityGradients:[{timeFraction:0,factor:.07,factor2:.5},{timeFraction:1,factor:.5,factor2:.15}],colorGradients:[{timeFraction:0,minColor:[0,255,255,1],maxColor:[73,216,230,1]},{timeFraction:1,minColor:[50,54,64,1],maxColor:[29,31,26,1]}],emitRate:50});api.setPosition(q,[P[0],y+.3,P[2]]);removeNametag(q)}
export function updateLbNts(k){let c=lbNtConfig[k];if(!c)return;feedLb(c.getValue);let b=getLb();for(let i=1;i<=c.topPC;i++){let e=lbNtConfig.lbNtIds[k][i],d=b[i-1];e&&NT(e,{content:[d?{str:"#"+i+" "+d.player+" - "+d.value,style:{color:C[i-1]||"white"}}:{str:"#"+i+" NONE"}],backgroundColor:"rgba(0, 0, 0, 0.7)",border:BD("gold","solid")})}}
export function lbTick(d=!1){globalThis.isInit||initLbNts();globalThis.lbCnt=globalThis.lbCnt||0;if(d&&++globalThis.lbCnt%100)return;for(let k of Object.keys(lbNtConfig))k!="lbNtIds"&&updateLbNts(k);for(let k of Object.keys(lbStageConfig))k!="lbStageNtIds"&&k!="animated"&&updateLbStages(k)}
```
