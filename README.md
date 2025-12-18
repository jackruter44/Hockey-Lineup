# Hockey-Lineup
Easy way to create and change hockey lines 
!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <title>Hockey Roster - Drag & Drop Lines</title>
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <style>
        :root{
            --bg:#0f1724; --card:#071233; --accent:#0ea5a4; --muted:#94a3b8;
        }
        *{box-sizing:border-box;font-family:system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial}
        body{margin:24px;background:linear-gradient(180deg,#071233 0%, #041025 100%); color:#e6eef8}
        h1{font-size:18px;margin:0 0 16px}
        .container{display:grid;gap:18px;grid-template-columns:repeat(3,minmax(200px,1fr));align-items:start}
        .panel{background:linear-gradient(180deg,var(--card),#03102a);padding:12px;border-radius:8px;border:1px solid rgba(255,255,255,0.03)}
        .panel h2{font-size:14px;margin:0 0 8px;color:var(--accent)}
        .lines{display:flex;flex-direction:column;gap:8px}
        .line{display:flex;gap:8px}
        .slot{
            background:rgba(255,255,255,0.02);
            border:1px dashed rgba(255,255,255,0.03);
            padding:6px;border-radius:6px;flex:1;
            display:flex;align-items:center;
            min-height:40px;
            transition:box-shadow .12s,transform .12s;
            cursor:grab;
        }
        .slot.dragging{opacity:0.6;transform:scale(0.98);box-shadow:0 6px 18px rgba(0,0,0,0.6)}
        .slot.over{outline:2px dashed var(--accent)}
        .slot input{
            width:100%;background:transparent;border:0;color:inherit;font-size:14px;outline:none;
        }
        .help{font-size:12px;color:var(--muted);margin-top:8px}
        @media (max-width:860px){ .container{grid-template-columns:1fr} }
    </style>
</head>
<body>
    <h1>Hockey Roster — Drag to swap slots</h1>
    <div class="container">
        <div class="panel" id="forwards">
            <h2>Forwards (4 lines × 3)</h2>
            <div class="lines" id="f-lines">
                <!-- 4 forward lines -->
                <div class="line">
                    <div class="slot" draggable="true" id="f1p1"><input placeholder="F1 LW" /></div>
                    <div class="slot" draggable="true" id="f1p2"><input placeholder="F1 C" /></div>
                    <div class="slot" draggable="true" id="f1p3"><input placeholder="F1 RW" /></div>
                </div>
                <div class="line">
                    <div class="slot" draggable="true" id="f2p1"><input placeholder="F2 LW" /></div>
                    <div class="slot" draggable="true" id="f2p2"><input placeholder="F2 C" /></div>
                    <div class="slot" draggable="true" id="f2p3"><input placeholder="F2 RW" /></div>
                </div>
                <div class="line">
                    <div class="slot" draggable="true" id="f3p1"><input placeholder="F3 LW" /></div>
                    <div class="slot" draggable="true" id="f3p2"><input placeholder="F3 C" /></div>
                    <div class="slot" draggable="true" id="f3p3"><input placeholder="F3 RW" /></div>
                </div>
                <div class="line">
                    <div class="slot" draggable="true" id="f4p1"><input placeholder="F4 LW" /></div>
                    <div class="slot" draggable="true" id="f4p2"><input placeholder="F4 C" /></div>
                    <div class="slot" draggable="true" id="f4p3"><input placeholder="F4 RW" /></div>
                </div>
            </div>
        </div>

        <div class="panel" id="defense">
            <h2>Defense (4 lines × 2)</h2>
            <div class="lines" id="d-lines">
                <div class="line">
                    <div class="slot" draggable="true" id="d1p1"><input placeholder="D1 L" /></div>
                    <div class="slot" draggable="true" id="d1p2"><input placeholder="D1 R" /></div>
                </div>
                <div class="line">
                    <div class="slot" draggable="true" id="d2p1"><input placeholder="D2 L" /></div>
                    <div class="slot" draggable="true" id="d2p2"><input placeholder="D2 R" /></div>
                </div>
                <div class="line">
                    <div class="slot" draggable="true" id="d3p1"><input placeholder="D3 L" /></div>
                    <div class="slot" draggable="true" id="d3p2"><input placeholder="D3 R" /></div>
                </div>
                <div class="line">
                    <div class="slot" draggable="true" id="d4p1"><input placeholder="D4 L" /></div>
                    <div class="slot" draggable="true" id="d4p2"><input placeholder="D4 R" /></div>
                </div>
            </div>
        </div>

        <div class="panel" id="goalies">
            <h2>Goalies (3)</h2>
            <div class="lines" id="g-lines">
                <div class="line">
                    <div class="slot" draggable="true" id="g1"><input placeholder="G1" /></div>
                </div>
                <div class="line">
                    <div class="slot" draggable="true" id="g2"><input placeholder="G2" /></div>
                </div>
                <div class="line">
                    <div class="slot" draggable="true" id="g3"><input placeholder="G3" /></div>
                </div>
            </div>
            <div class="help">Drag a box onto another to swap their positions (input values move with the box).</div>
        </div>
    </div>

    <script>
        // Add drag/drop handlers to all .slot elements
        (function(){
            let dragged = null;
            const slots = document.querySelectorAll('.slot');

            slots.forEach(s=>{
                s.addEventListener('dragstart', e=>{
                    dragged = s;
                    e.dataTransfer.setData('text/plain', s.id);
                    s.classList.add('dragging');
                    // small transparent drag image for smoother visuals
                    const img = document.createElement('canvas'); img.width=1; img.height=1;
                    e.dataTransfer.setDragImage(img,0,0);
                });

                s.addEventListener('dragend', e=>{
                    dragged = null;
                    s.classList.remove('dragging');
                    document.querySelectorAll('.slot.over').forEach(x=>x.classList.remove('over'));
                });

                s.addEventListener('dragover', e=>{
                    e.preventDefault();
                });

                s.addEventListener('dragenter', e=>{
                    if (!dragged || dragged === s) return;
                    s.classList.add('over');
                });

                s.addEventListener('dragleave', e=>{
                    s.classList.remove('over');
                });

                s.addEventListener('drop', e=>{
                    e.preventDefault();
                    s.classList.remove('over');
                    const id = e.dataTransfer.getData('text/plain');
                    const source = document.getElementById(id);
                    const target = s;
                    if (!source || source === target) return;
                    swapNodes(source, target);
                });
            });

            function swapNodes(a, b){
                // Swap two DOM nodes (preserves their children/input state)
                const aParent = a.parentNode;
                const bParent = b.parentNode;
                const aSibling = a.nextSibling === b ? a : a.nextSibling;

                bParent.replaceChild(a, b);
                aParent.insertBefore(b, aSibling);
            }

            // Allow clicking/tapping the slot to focus its input
            document.addEventListener('click', e=>{
                const slot = e.target.closest('.slot');
                if (!slot) return;
                const inp = slot.querySelector('input');
                if (inp) inp.focus();
            });
        })();
    </script>
</body>
</html>
