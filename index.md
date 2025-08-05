<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>背部+手臂专项</title>
  <style>
    body{
      margin:0;
      font-family:-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,"Noto Sans",sans-serif;
      background:linear-gradient(135deg,#e0c3ff 0%,#ffccf9 100%);
      min-height:100vh;
    }
    .container{max-width:400px;margin:auto;padding:12px}
    .card{background:#fff;border-radius:12px;padding:16px;margin-bottom:12px;box-shadow:0 2px 8px rgba(0,0,0,.1)}
    .back-btn{
      display:inline-flex;
      align-items:center;
      gap:8px;
      color:#8b5cf6;
      text-decoration:none;
      font-size:14px;
      margin-bottom:16px;
      padding:8px 12px;
      background:#fff;
      border-radius:8px;
      box-shadow:0 1px 4px rgba(0,0,0,.1);
    }
    .hdr{display:flex;justify-content:space-between;align-items:center;cursor:pointer;padding:4px 0;}
    .hdr:hover{background:#f9fafb;border-radius:8px;padding:8px;}
    .notes{background:#fef3c7;padding:12px;border-radius:8px;font-size:13px;color:#92400e;margin:12px 0}
    .set{display:flex;align-items:center;gap:8px;margin:8px 0;padding:8px;background:#f9fafb;border-radius:8px}
    .set span{min-width:45px;font-size:13px;color:#6b7280}
    .set input{width:60px;padding:6px;border:1px solid #d1d5db;border-radius:6px;font-size:14px}
    .set input:focus{outline:none;border-color:#8b5cf6;box-shadow:0 0 0 2px rgba(139,92,246,.1)}
    .btn{width:28px;height:28px;border:none;border-radius:50%;cursor:pointer;font-size:16px;transition:all 0.2s}
    .done{background:#10b981;color:#fff}
    .todo{background:#e5e7eb;color:#9ca3af}
    .todo:hover{background:#d1d5db}
    #progress{margin-top:12px}
    #bar{background:#8b5cf6;height:6px;border-radius:3px;transition:width .3s}
    .exercise-title{font-weight:bold;color:#1f2937;margin-bottom:4px}
    .exercise-target{font-size:13px;color:#8b5cf6}
    .counter{background:#8b5cf6;color:#fff;padding:4px 8px;border-radius:12px;font-size:12px;font-weight:bold}
  </style>
</head>
<body>
<div class="container">
  <a href="index.html" class="back-btn">
    ← 返回主页
  </a>
  
  <div class="card">
    <h2>💪 背部+手臂专项</h2>
    <p>99块健身房训练</p>
    <div id="progress">
      <span id="counter" class="counter">0/21</span>
      <div style="background:#e5e7eb;height:6px;border-radius:3px;margin-top:8px">
        <div id="bar" style="width:0%"></div>
      </div>
    </div>
  </div>

  <div id="list"></div>

  <div class="card" style="text-align:center;font-size:13px;color:#6b7280">
    记得热身！左肩不适立即调整重量<br/>
    <span style="color:#8b5cf6;font-weight:bold;margin-top:8px;display:block">练完记得反馈给阿焱～</span>
  </div>
</div>

<script>
// 修复：创建独立的对象数组，避免共享引用
function createSets(count, reps) {
  return Array.from({length: count}, () => ({reps, w: "", done: false}));
}

const workout = {
  "单臂哑铃划船": {
    target: "背阔肌、菱形肌、中斜方肌",
    notes: "左侧用轻重量，背部收紧，肘部贴身体",
    sets: createSets(4, "10-12")
  },
  "坐姿绳索划船": {
    target: "背阔肌中下部、菱形肌", 
    notes: "肩胛骨后收，胸挺直，拉到最后挤压背部",
    sets: createSets(4, "12-15")
  },
  "器械坐姿划船": {
    target: "背阔肌、中斜方肌",
    notes: "胸贴垫子，动作慢一点，感受背部收缩",
    sets: createSets(3, "12-15")
  },
  "高位下拉": {
    target: "背阔肌上部",
    notes: "轻重量！左肩有感觉就停，重量别贪心",
    sets: createSets(2, "15")
  },
  "坐姿哑铃弯举": {
    target: "肱二头肌",
    notes: "控制节奏，顶峰收缩",
    sets: createSets(3, "12-15")
  },
  "绳索三头下压": {
    target: "肱三头肌",
    notes: "肘部固定，小臂发力",
    sets: createSets(3, "15-20")
  },
  "锤式弯举": {
    target: "肱二头肌、肱桡肌",
    notes: "解决小臂握不住的问题",
    sets: createSets(2, "12")
  }
};

function save(){
  try {
    localStorage.setItem('workout-back-arms', JSON.stringify(workout));
  } catch(e) {
    console.log('保存失败:', e);
  }
}

function load(){
  try {
    const saved = localStorage.getItem('workout-back-arms');
    if(saved) {
      const data = JSON.parse(saved);
      // 合并保存的数据到当前workout
      Object.keys(workout).forEach(key => {
        if(data[key] && data[key].sets) {
          workout[key].sets = data[key].sets;
        }
      });
    }
  } catch(e) {
    console.log('加载失败:', e);
  }
}

function render(){
  load();
  let done = 0, total = 0;
  const list = document.getElementById('list');
  list.innerHTML = '';
  
  Object.entries(workout).forEach(([name, info]) => {
    total += info.sets.length;
    done += info.sets.filter(s => s.done).length;
    
    const card = document.createElement('div');
    card.className = 'card';
    card.innerHTML = `
      <div class="hdr" onclick="toggle('${name}')">
        <div>
          <div class="exercise-title">${name}</div>
          <div class="exercise-target">${info.target}</div>
        </div>
        <span class="counter">${info.sets.filter(s => s.done).length}/${info.sets.length}</span>
      </div>
      <div id="${name}-sets" style="display:none">
        <div class="notes">${info.notes}</div>
        ${info.sets.map((s, i) => `
          <div class="set">
            <span>第${i+1}组</span>
            <span>${s.reps}次</span>
            <input type="text" placeholder="kg" value="${s.w}" 
              onchange="setWeight('${name}', ${i}, this.value)">
            <button class="btn ${s.done ? 'done' : 'todo'}" 
              onclick="toggleDone('${name}', ${i})">✓</button>
          </div>
        `).join('')}
      </div>
    `;
    list.appendChild(card);
  });
  
  document.getElementById('counter').textContent = `${done}/${total}`;
  document.getElementById('bar').style.width = `${total ? (done/total)*100 : 0}%`;
}

function toggle(name){
  const el = document.getElementById(`${name}-sets`);
  el.style.display = el.style.display === 'none' ? 'block' : 'none';
}

function setWeight(name, index, value){
  workout[name].sets[index].w = value;
  save();
}

function toggleDone(name, index){
  workout[name].sets[index].done = !workout[name].sets[index].done;
  save();
  render();
}

// 初始化
render();
</script>
</body>
</html>
