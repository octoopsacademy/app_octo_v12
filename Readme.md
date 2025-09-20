
# How files are organized (suggested)

* `app-v1/index.html` — Version 1 (colorful, welcome note, simple calculator)
* `app-v2/index.html` — Version 2 (same as V1 + calendar)
* `Dockerfile` — single Dockerfile you can build per-version (use `--build-arg APP=app-v1` or copy correct folder)
* `deployment.yaml` — Kubernetes Deployment (set image)
* `service-nodeport.yaml` — Kubernetes NodePort service

---

# app-v1/index.html

Save this as `app-v1/index.html`.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>OcotoOps Academy - V1</title>
  <style>
    :root{
      --bg: linear-gradient(135deg,#ff9a9e 0%, #fad0c4 50%, #fbc2eb 100%);
      --card: rgba(255,255,255,0.9);
      --accent: #5b21b6;
      --accent-2: #f59e0b;
    }
    body{
      margin:0;
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      min-height:100vh;
      display:flex;
      align-items:center;
      justify-content:center;
      background:var(--bg);
    }
    .container{
      width:min(920px,95%);
      background:var(--card);
      border-radius:16px;
      box-shadow: 0 12px 30px rgba(16,24,40,0.15);
      padding:28px;
      backdrop-filter: blur(6px);
    }
    header{
      display:flex;
      align-items:center;
      gap:16px;
    }
    .logo{
      width:72px;
      height:72px;
      border-radius:14px;
      background:linear-gradient(135deg,var(--accent),#06b6d4);
      display:flex;
      align-items:center;
      justify-content:center;
      color:white;
      font-weight:700;
      font-size:20px;
      box-shadow: 0 6px 18px rgba(59,130,246,0.15);
    }
    h1{
      margin:0;
      font-size:28px;
      color:#111827;
      letter-spacing: -0.5px;
    }
    p.lead{
      margin:6px 0 20px 0;
      color:#374151;
    }

    .card{
      background:linear-gradient(180deg, rgba(255,255,255,0.65), rgba(255,255,255,0.85));
      padding:18px;
      border-radius:12px;
      margin-top:12px;
      box-shadow: 0 6px 18px rgba(2,6,23,0.05);
    }

    /* Calculator */
    .calculator{
      display:flex;
      gap:18px;
      flex-wrap:wrap;
      align-items:center;
      justify-content:flex-start;
    }
    .calc-input{
      display:flex;
      gap:8px;
      align-items:center;
    }
    input[type="number"]{
      width:140px;
      padding:10px 12px;
      border-radius:8px;
      border:1px solid #e5e7eb;
      font-size:16px;
    }
    select{
      padding:10px 12px;
      border-radius:8px;
      border:1px solid #e5e7eb;
      font-size:16px;
    }
    button{
      padding:10px 14px;
      border-radius:10px;
      border:none;
      cursor:pointer;
      font-weight:600;
      box-shadow: 0 6px 12px rgba(99,102,241,0.12);
    }
    .btn-primary{ background: linear-gradient(90deg,var(--accent),#06b6d4); color:white; }
    .btn-clear{ background:#f3f4f6; color:#111827; }

    .result{
      padding:12px;
      border-radius:10px;
      min-width:170px;
      text-align:center;
      font-weight:700;
      font-size:20px;
      color:#111827;
      background: linear-gradient(90deg, rgba(245,158,11,0.12), rgba(91,33,182,0.06));
    }

    footer{
      margin-top:18px;
      color:#6b7280;
      font-size:13px;
    }
    @media (max-width:600px){
      .calc-input{flex-direction:column; align-items:stretch;}
      input[type="number"]{width:100%;}
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div class="logo">OA</div>
      <div>
        <h1>Welcome To OcotoOps Academy</h1>
        <p class="lead">A colourful UI demo with a simple calculator — (Version 1)</p>
      </div>
    </header>

    <div class="card" id="calculatorCard">
      <h3>Simple Calculator</h3>
      <div class="calculator">
        <div class="calc-input">
          <input id="a" type="number" placeholder="Number A" />
          <select id="op">
            <option value="+">+</option>
            <option value="-">−</option>
            <option value="*">×</option>
            <option value="/">÷</option>
          </select>
          <input id="b" type="number" placeholder="Number B" />
        </div>

        <div style="display:flex;gap:10px;align-items:center;">
          <button class="btn-primary" id="calcBtn">Calculate</button>
          <button class="btn-clear" id="clearBtn">Clear</button>
        </div>

        <div class="result" id="result">Result: —</div>
      </div>

      <footer>Tip: Try decimals and negative numbers. Division by zero is handled.</footer>
    </div>
  </div>

  <script>
    function calculate(){
      const a = parseFloat(document.getElementById('a').value) || 0;
      const b = parseFloat(document.getElementById('b').value) || 0;
      const op = document.getElementById('op').value;
      let res;
      switch(op){
        case '+': res = a + b; break;
        case '-': res = a - b; break;
        case '*': res = a * b; break;
        case '/': res = (b === 0) ? 'Error (div by 0)' : (a / b); break;
        default: res = 'N/A';
      }
      document.getElementById('result').textContent = 'Result: ' + res;
    }

    document.getElementById('calcBtn').addEventListener('click', calculate);
    document.getElementById('clearBtn').addEventListener('click', ()=> {
      document.getElementById('a').value = '';
      document.getElementById('b').value = '';
      document.getElementById('result').textContent = 'Result: —';
    });

    // allow Enter on inputs
    ['a','b'].forEach(id => {
      document.getElementById(id).addEventListener('keyup', (e) => {
        if(e.key === 'Enter') calculate();
      });
    });
  </script>
</body>
</html>
```

---

# app-v2/index.html (V1 + Calendar)

Save as `app-v2/index.html`. This builds on V1 and adds a lightweight JS calendar above the calculator.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>OcotoOps Academy - V2 (Calendar)</title>
  <style>
    :root{
      --bg: linear-gradient(135deg,#a18cd1 0%, #fbc2eb 100%);
      --card: rgba(255,255,255,0.95);
      --accent: #0ea5e9;
    }
    body{ margin:0; font-family:Inter,system-ui,-apple-system,"Segoe UI",Roboto,Arial; display:flex; min-height:100vh; align-items:center; justify-content:center; background:var(--bg); }
    .container{ width:min(980px,96%); background:var(--card); border-radius:14px; padding:22px; box-shadow: 0 12px 28px rgba(2,6,23,0.08); }
    header{ display:flex; gap:14px; align-items:center; }
    .logo{ width:70px; height:70px; border-radius:12px; background:linear-gradient(90deg,#06b6d4,#7c3aed); color:white; display:flex; align-items:center; justify-content:center; font-weight:800; font-size:20px; }
    h1{ margin:0; font-size:26px; color:#0f172a; }
    p.lead{ margin:6px 0 10px 0; color:#374151; }

    .card{ background:linear-gradient(180deg, rgba(255,255,255,0.85), rgba(255,255,255,0.95)); padding:14px; border-radius:12px; margin-top:12px; }

    /* calendar styles */
    .calendar-wrap{ display:flex; gap:18px; flex-wrap:wrap; align-items:flex-start; }
    .calendar{
      min-width:280px;
      border-radius:12px;
      padding:12px;
      background: linear-gradient(180deg, rgba(14,165,233,0.06), rgba(124,58,237,0.03));
      box-shadow: 0 6px 16px rgba(14,165,233,0.06);
    }
    .cal-header{ display:flex; justify-content:space-between; align-items:center; gap:8px; margin-bottom:8px; }
    .cal-controls button{ padding:6px 10px; border-radius:8px; border:none; cursor:pointer; font-weight:600; }
    .month-year{ font-weight:700; font-size:16px; }
    table.calendar-grid{ width:100%; border-collapse:collapse; text-align:center; }
    table.calendar-grid th{ font-size:12px; padding:6px; color:#374151; }
    table.calendar-grid td{ padding:8px; height:40px; border-radius:6px; }
    td.other{ color:#9ca3af; }
    td.today{ background:linear-gradient(90deg,#06b6d4,#7c3aed); color:white; font-weight:700; border-radius:8px; }

    /* calculator - reuse v1 styles */
    .calculator{ display:flex; gap:18px; flex-wrap:wrap; align-items:center; }
    .calc-input{ display:flex; gap:8px; align-items:center; }
    input[type="number"]{ width:130px; padding:10px; border-radius:8px; border:1px solid #e5e7eb; font-size:16px; }
    select{ padding:10px; border-radius:8px; border:1px solid #e5e7eb; font-size:16px; }
    .result{ padding:12px; border-radius:10px; min-width:170px; text-align:center; font-weight:700; font-size:20px; color:#111827; background: linear-gradient(90deg, rgba(14,165,233,0.08), rgba(124,58,237,0.06)); }

    .btn-primary{ background: linear-gradient(90deg,#06b6d4,#7c3aed); color:white; padding:10px 14px; border-radius:10px; border:none; cursor:pointer; font-weight:600; }
    .btn-clear{ background:#f3f4f6; color:#111827; padding:10px 12px; border-radius:10px; border:none; cursor:pointer; }

    @media (max-width:700px){
      .calendar-wrap{ flex-direction:column; }
      .calendar{ width:100%; }
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div class="logo">OA</div>
      <div>
        <h1>Welcome To OcotoOps Academy</h1>
        <p class="lead">Version 2 — Calendar + Calculator</p>
      </div>
    </header>

    <div class="card">
      <div class="calendar-wrap">
        <div class="calendar" id="calendar">
          <div class="cal-header">
            <div class="month-year" id="monthYear">Month Year</div>
            <div class="cal-controls">
              <button id="prevBtn">◀</button>
              <button id="nextBtn">▶</button>
            </div>
          </div>
          <table class="calendar-grid" id="calGrid">
            <thead><tr><th>Sun</th><th>Mon</th><th>Tue</th><th>Wed</th><th>Thu</th><th>Fri</th><th>Sat</th></tr></thead>
            <tbody id="calBody"></tbody>
          </table>
        </div>

        <div style="flex:1;">
          <h3>Simple Calculator</h3>
          <div class="calculator card" style="padding:12px;">
            <div class="calc-input">
              <input id="a" type="number" placeholder="Number A" />
              <select id="op">
                <option value="+">+</option>
                <option value="-">−</option>
                <option value="*">×</option>
                <option value="/">÷</option>
              </select>
              <input id="b" type="number" placeholder="Number B" />
            </div>

            <div style="display:flex;gap:10px;margin-top:10px;">
              <button class="btn-primary" id="calcBtn">Calculate</button>
              <button class="btn-clear" id="clearBtn">Clear</button>
            </div>

            <div class="result" id="result" style="margin-top:10px;">Result: —</div>
          </div>
        </div>
      </div>
    </div>
  </div>

<script>
  // Simple calendar
  (function(){
    const calBody = document.getElementById('calBody');
    const monthYear = document.getElementById('monthYear');
    let dt = new Date();

    function render(date){
      const year = date.getFullYear();
      const month = date.getMonth();
      monthYear.textContent = date.toLocaleString(undefined,{month:'long', year:'numeric'});

      // first day of month
      const first = new Date(year, month, 1);
      const last = new Date(year, month + 1, 0);
      const startDay = first.getDay();
      const totalDays = last.getDate();

      calBody.innerHTML = '';
      let row = document.createElement('tr');
      // blanks
      for(let i=0;i<startDay;i++){
        const td = document.createElement('td');
        td.classList.add('other');
        td.textContent = '';
        row.appendChild(td);
      }

      for(let d=1; d<=totalDays; d++){
        if(row.children.length === 0) row = document.createElement('tr');

        const td = document.createElement('td');
        td.textContent = d;

        const now = new Date();
        if(d === now.getDate() && month === now.getMonth() && year === now.getFullYear()){
          td.classList.add('today');
        }

        row.appendChild(td);

        if(row.children.length === 7){
          calBody.appendChild(row);
          row = document.createElement('tr');
        }
      }

      // fill last row
      if(row.children.length > 0){
        while(row.children.length < 7){
          const td = document.createElement('td');
          td.classList.add('other');
          td.textContent = '';
          row.appendChild(td);
        }
        calBody.appendChild(row);
      }
    }

    document.getElementById('prevBtn').addEventListener('click', ()=> {
      dt = new Date(dt.getFullYear(), dt.getMonth() - 1, 1);
      render(dt);
    });
    document.getElementById('nextBtn').addEventListener('click', ()=> {
      dt = new Date(dt.getFullYear(), dt.getMonth() + 1, 1);
      render(dt);
    });

    render(dt);
  })();

  // Calculator (same as v1)
  (function(){
    function calculate(){
      const a = parseFloat(document.getElementById('a').value) || 0;
      const b = parseFloat(document.getElementById('b').value) || 0;
      const op = document.getElementById('op').value;
      let res;
      switch(op){
        case '+': res = a + b; break;
        case '-': res = a - b; break;
        case '*': res = a * b; break;
        case '/': res = (b === 0) ? 'Error (div by 0)' : (a / b); break;
        default: res = 'N/A';
      }
      document.getElementById('result').textContent = 'Result: ' + res;
    }

    document.getElementById('calcBtn').addEventListener('click', calculate);
    document.getElementById('clearBtn').addEventListener('click', ()=> {
      document.getElementById('a').value = '';
      document.getElementById('b').value = '';
      document.getElementById('result').textContent = 'Result: —';
    });
    ['a','b'].forEach(id => {
      document.getElementById(id).addEventListener('keyup', (e) => {
        if(e.key === 'Enter') calculate();
      });
    });
  })();
</script>
</body>
</html>
```

---

# Dockerfile

Place this `Dockerfile` in the parent directory next to `app-v1/` and `app-v2/` (one Dockerfile can build either by specifying build arg).

```dockerfile
# Build a very small static site image using nginx
FROM nginx:alpine AS base
# Remove default html
RUN rm -rf /usr/share/nginx/html/*

# Copy chosen app into nginx html directory using build-arg
ARG APP_DIR=app-v1
COPY ${APP_DIR} /usr/share/nginx/html

# expose port 80
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Usage: set `--build-arg APP_DIR=app-v1` or `app-v2`.

---

# Build & run commands (local)

Assuming you are in the parent directory containing `Dockerfile`, `app-v1/`, `app-v2/`.

1. Build V1 image locally:

```bash
docker build --no-cache -t ocotoops-app:v1 --build-arg APP_DIR=app-v1 .
```

2. Run V1 container (map port 8080 on host):

```bash
docker run --rm -p 8080:80 --name ocotoops-v1 ocotoops-app:v1
# Open http://localhost:8080
```

3. Build V2:

```bash
docker build --no-cache -t ocotoops-app:v2 --build-arg APP_DIR=app-v2 .
```

4. Run V2:

```bash
docker run --rm -p 8081:80 --name ocotoops-v2 ocotoops-app:v2
# Open http://localhost:8081
```

5. To push images to a registry (example Docker Hub):

```bash
docker tag ocotoops-app:v1 yourdockerhubusername/ocotoops-app:v1
docker push yourdockerhubusername/ocotoops-app:v1
# same for v2
```

---

# Kubernetes manifests

Update the `image:` fields to your registry image (e.g., `yourdockerhubusername/ocotoops-app:v1`).

## deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ocotoops-app
  labels:
    app: ocotoops
spec:
  replicas: 2
  selector:
    matchLabels:
      app: ocotoops
  template:
    metadata:
      labels:
        app: ocotoops
    spec:
      containers:
      - name: ocotoops-container
        # Replace with your image (v1 or v2)
        image: yourdockerhubusername/ocotoops-app:v1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 10
```

> To switch to V2, change `image:` to `.../ocotoops-app:v2` and `kubectl apply -f deployment.yaml`.

## service-nodeport.yaml

This creates a NodePort service exposing the app on node port `30080`. You can change `nodePort` as needed (range depends on cluster, typical is 30000–32767).

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ocotoops-nodeport
spec:
  type: NodePort
  selector:
    app: ocotoops
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    nodePort: 30080
```

---

# Kubernetes deploy commands

1. Apply deployment and service:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service-nodeport.yaml
```

2. Verify pods:

```bash
kubectl get pods -l app=ocotoops -w
```

3. Access:

* If running on a local minikube cluster:

  ```bash
  minikube service ocotoops-nodeport --url
  # or open http://<minikube-ip>:30080
  ```
* If on any k8s node, access `http://<node-ip>:30080`.

4. To update to v2:

```bash
# Update image in deployment.yaml or use kubectl set image
kubectl set image deployment/ocotoops-app ocotoops-container=yourdockerhubusername/ocotoops-app:v2 --record
# wait for rollout
kubectl rollout status deployment/ocotoops-app
```


