Test v3
[index.html](https://github.com/user-attachments/files/22098870/index.html)<!DOCTYPE html>

let order=[]; let answered=new Map(); let currentPage=1; const pageSize=2;
const quizEl=document.getElementById('quiz');
const scoreBox=document.getElementById('scoreBox');
const resultsEl=document.getElementById('results');
const btnPrev=document.getElementById('btnPrev');
const btnNext=document.getElementById('btnNext');
const btnEnd=document.getElementById('btnEnd');
const btnRestart=document.getElementById('btnRestart');
const meta=document.getElementById('meta');

function shuffle(arr){for(let i=arr.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[arr[i],arr[j]]=[arr[j],arr[i]];}return arr;}

function renderAll(){
const total=QUESTIONS.length; const totalPages=Math.ceil(total/pageSize);
if(currentPage<1) currentPage=1; if(currentPage>totalPages) currentPage=totalPages;
const start=(currentPage-1)*pageSize; const end=Math.min(start+pageSize,total);
quizEl.innerHTML="";
for(let i=start;i<end;i++){ quizEl.appendChild(renderCard(QUESTIONS[order[i]])); }
meta.textContent=`Preguntas totales: ${total} · Página ${currentPage}/${totalPages}`;
btnPrev.disabled=currentPage<=1; btnNext.disabled=currentPage>=totalPages;
}

function renderCard(q){
const card=document.createElement('div'); card.className='card';
const title=document.createElement('h3'); title.textContent=q.id+". "+q.q; card.appendChild(title);
q.options.forEach((opt,i)=>{
const label=document.createElement('label'); label.className='option';
const input=document.createElement('input'); input.type='radio'; input.name='q'+q.id; input.value=i;
input.addEventListener('change',()=>handleAnswer(q,i,label,card));
label.appendChild(input); label.appendChild(document.createTextNode(opt));
card.appendChild(label);
});
const fb=document.createElement('div'); fb.id='fb'+q.id; card.appendChild(fb);
return card;
}

function handleAnswer(q,selected,label,card){
const correct=selected===q.answer; answered.set(q.id,{correct});
card.querySelectorAll('.option').forEach(l=>l.classList.remove('correct','incorrect'));
label.classList.add(correct?'correct':'incorrect');
const fb=card.querySelector('#fb'+q.id);
fb.className='feedback '+(correct?'ok':'err');
fb.textContent=(correct?'✅ Correcto. ':'❌ Incorrecto. ')+q.explanation;
updateScore();
}

function updateScore(){
let ok=0,bad=0; for(const [,r] of answered){if(r.correct) ok++; else bad++;}
scoreBox.textContent=`✅ ${ok} correctas · ❌ ${bad} incorrectas`;
}

function finishNow(){
const total=QUESTIONS.length; let ok=0,bad=0; for(const [,r] of answered){if(r.correct) ok++; else bad++;}
const grade=Math.round(ok/total*100);
resultsEl.classList.remove('hidden');
resultsEl.innerHTML=`<h2>Resultado</h2><p>Calificación: ${grade}/100</p><p>Correctas: ${ok} · Incorrectas: ${bad}</p>`;
}

function restart(){ answered.clear(); order=shuffle([...Array(QUESTIONS.length).keys()]); currentPage=1; renderAll(); updateScore(); resultsEl.classList.add('hidden'); }

btnPrev.addEventListener('click',()=>{currentPage--; renderAll();});
btnNext.addEventListener('click',()=>{currentPage++; renderAll();});
btnEnd.addEventListener('click',finishNow);
btnRestart.addEventListener('click',restart);

restart();
})();
</script>
</body>
</html>
