<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Villa Patricia Rondines</title>
</head>
<body style="font-family:system-ui;padding:12px;background:#f4f6f8">
<h2>Villa Patricia - Perimetro Principal</h2>
<p>Prioritario + Fotos en cruces</p>
<input id="guardia" placeholder="Nombre: Ej. Hunter" style="width:100%;padding:12px;border-radius:8px">
<button onclick="iniciar()" style="width:100%;padding:14px;background:#0f2a44;color:#fff;border-radius:12px;margin-top:8px;font-weight:700">INICIAR RECORRIDO PERIMETRAL</button>
<video id="video" autoplay playsinline style="width:100%;margin-top:10px;border-radius:12px;display:none"></video>
<button onclick="tomarFoto()" style="width:100%;padding:14px;background:#ff7a00;color:#fff;border-radius:12px;margin-top:8px;font-weight:700">📸 FOTO EN CRUCE</button>
<div id="preview"></div>
<div id="log" style="background:#0f2a44;color:#c8f5c8;padding:12px;border-radius:12px;margin-top:12px;white-space:pre-wrap;font-size:12px">Bitacora lista...</div>
<script>
let registros=[], lastPos="sin GPS", guardia="", stream;
async function iniciar(){
 guardia=document.getElementById('guardia').value||'Guardia';
 registros.push('INICIO PERIMETRO - '+new Date().toLocaleString()+' - '+guardia);
 document.getElementById('log').textContent=registros.join('\n');
 try{
  stream=await navigator.mediaDevices.getUserMedia({video:{facingMode:'environment'}});
  let v=document.getElementById('video'); v.srcObject=stream; v.style.display='block';
 }catch(e){alert('No camara: '+e)}
 navigator.geolocation.watchPosition(p=>{lastPos=p.coords.latitude.toFixed(6)+','+p.coords.longitude.toFixed(6)});
}
function tomarFoto(){
 let v=document.getElementById('video'); if(!v.srcObject){alert('Primero inicia recorrido');return}
 let c=document.createElement('canvas'); c.width=v.videoWidth; c.height=v.videoHeight;
 let ctx=c.getContext('2d'); ctx.drawImage(v,0,0);
 ctx.fillStyle='rgba(0,0,0,0.65)'; ctx.fillRect(0,c.height-100,c.width,100);
 ctx.fillStyle='white'; ctx.font='bold 18px sans-serif';
 ctx.fillText(guardia+' | '+new Date().toLocaleTimeString(),12,c.height-65);
 ctx.font='13px sans-serif';
 ctx.fillText(new Date().toLocaleDateString()+' | Villa Patricia Leon Gto | '+lastPos,12,c.height-35);
 let img=document.createElement('img'); img.src=c.toDataURL('image/jpeg',0.85);
 img.style.width='100%'; img.style.marginTop='8px'; img.style.borderRadius='12px';
 document.getElementById('preview').prepend(img);
 registros.push('FOTO CRUCE - '+new Date().toLocaleString()+' - '+lastPos);
 document.getElementById('log').textContent=registros.join('\n');
 localStorage.setItem('vp',JSON.stringify(registros));
}
</script>
</body>
</html>
