<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Tributo - Página conmemorativa</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { transition: background-color .4s, color .3s; }
    .fade { transition: opacity .6s, transform .6s; opacity:0; transform:translateY(12px); }
    .show { opacity:1; transform:none; }
    .img-thumb { max-width:100%; border-radius:8px; box-shadow:0 6px 18px rgba(0,0,0,.12); }
    .card { background: rgba(255,255,255,.9); padding:1rem; border-radius:12px; box-shadow:0 6px 20px rgba(0,0,0,.06); }
  </style>
</head>
<body class="bg-gray-100 text-gray-900">

  <div class="max-w-6xl mx-auto p-6">
    <header class="flex items-center justify-between mb-6">
      <h1 id="siteTitle" class="text-2xl font-bold">Página conmemorativa</h1>
      <div class="space-x-2">
        <button id="btnAdmin" class="px-3 py-1 bg-gray-800 text-white rounded">Entrar como administrador</button>
        <button id="btnPublish" class="px-3 py-1 bg-blue-600 text-white rounded">Generar enlace público</button>
      </div>
    </header>

    <main class="grid grid-cols-1 md:grid-cols-3 gap-6">
      <!-- Left: Visual -->
      <section class="md:col-span-2 space-y-4">
        <div id="hero" class="card p-6" style="background-size:cover; background-position:center;">
          <div class="flex items-start gap-4">
            <img id="mainPhoto" src="" alt="Foto principal" class="w-36 h-36 rounded-lg object-cover shadow" />
            <div>
              <h2 id="personName" class="text-3xl font-semibold">Nombre Apellido</h2>
              <p id="personDates" class="text-gray-600">01/01/1950 — 01/01/2025</p>
              <p id="personShort" class="mt-3 text-gray-800">Breve frase o epitafio.</p>
            </div>
          </div>
        </div>

        <div id="bioCard" class="card p-6">
          <h3 class="text-xl font-semibold mb-2">Biografía</h3>
          <div id="bioText" class="prose text-gray-800">Aquí va la biografía larga: vida, anécdotas, fechas, logros...</div>
        </div>

        <div id="gallery" class="card p-6">
          <h3 class="text-xl font-semibold mb-3">Galería</h3>
          <div id="galleryGrid" class="grid grid-cols-2 sm:grid-cols-3 gap-3"></div>
          <div class="mt-4">
            <h4 class="font-medium mb-2">Videos</h4>
            <div id="videoList" class="space-y-3"></div>
          </div>
        </div>

        <div id="visitorUploads" class="card p-6">
          <h3 class="text-xl font-semibold mb-3">Fotos que compartieron con la persona</h3>
          <div id="sharedGrid" class="grid grid-cols-2 sm:grid-cols-3 gap-3"></div>
          <div class="mt-4">
            <label class="block mb-2 font-medium">Sube tu foto (visitante)</label>
            <input id="visitorPhotoInput" type="file" accept="image/*" />
            <input id="visitorNameInput" class="mt-2 p-2 border rounded w-full" placeholder="Tu nombre (opcional)" />
            <button id="btnUploadVisitor" class="mt-2 px-4 py-2 bg-green-600 text-white rounded">Subir foto</button>
            <p class="text-sm text-gray-500 mt-2">*Sin backend: estas fotos se almacenan localmente en tu navegador. Para que se compartan entre visitantes, activa la integración con un servicio (ver abajo).</p>
          </div>
        </div>

        <div id="commentsCard" class="card p-6">
          <h3 class="text-xl font-semibold mb-3">Mensajes y recuerdos</h3>
          <div id="commentsList" class="space-y-3 mb-4"></div>
          <textarea id="commentInput" rows="3" class="w-full p-2 border rounded" placeholder="Escribe tu recuerdo..."></textarea>
          <input id="commentName" class="mt-2 p-2 border rounded w-full" placeholder="Tu nombre" />
          <div class="flex gap-2 mt-2">
            <button id="btnComment" class="px-4 py-2 bg-blue-600 text-white rounded">Enviar</button>
            <button id="btnClearComments" class="px-4 py-2 bg-red-500 text-white rounded">Borrar comentarios locales</button>
          </div>
          <p class="text-sm text-gray-500 mt-2">*Sin backend: los comentarios se almacenan localmente y no se sincronizan. Puedes activar Firebase para persistencia real (opcional).</p>
        </div>
      </section>

      <!-- Right: Mini panel de info / ajustes -->
      <aside class="space-y-4">
        <div class="card p-4">
          <h4 class="font-semibold mb-2">Detalles rápidos</h4>
          <p><strong>URL actual:</strong> <span id="currentUrl" class="break-all text-sm"></span></p>
          <p class="mt-2 text-sm text-gray-600">Usa "Generar enlace público" para crear un enlace que contenga la página editable y compártelo con familiares.</p>
        </div>

        <div class="card p-4">
          <h4 class="font-semibold mb-2">Acciones</h4>
          <button id="btnEdit" class="w-full px-3 py-2 bg-indigo-600 text-white rounded mb-2">Editar contenido (Admin)</button>
          <button id="btnExport" class="w-full px-3 py-2 bg-gray-700 text-white rounded mb-2">Exportar JSON</button>
          <button id="btnImport" class="w-full px-3 py-2 bg-gray-300 rounded">Importar JSON</button>
          <input type="file" id="importFile" accept="application/json" style="display:none" />
        </div>

        <div class="card p-4">
          <h4 class="font-semibold mb-2">Estado</h4>
          <p id="statusText" class="text-sm text-gray-700">Cargando...</p>
        </div>
      </aside>
    </main>
  </div>

  <!-- Modal editor -->
  <div id="adminModal" class="fixed inset-0 bg-black/50 hidden items-center justify-center p-4">
    <div class="bg-white rounded-xl max-w-3xl w-full p-6 overflow-auto max-h-[90vh]">
      <div class="flex justify-between items-center mb-4">
        <h3 class="text-xl font-semibold">Panel administrador</h3>
        <div class="space-x-2">
          <button id="btnSaveAdmin" class="px-3 py-1 bg-green-600 text-white rounded">Guardar</button>
          <button id="btnCloseAdmin" class="px-3 py-1 bg-gray-300 rounded">Cerrar</button>
        </div>
      </div>

      <div class="space-y-4">
        <label class="block">Nombre completo
          <input id="editName" class="w-full p-2 border rounded" />
        </label>

        <div class="grid grid-cols-2 gap-3">
          <label>Fecha de nacimiento
            <input id="editBorn" type="date" class="w-full p-2 border rounded" />
          </label>
          <label>Fecha de fallecimiento
            <input id="editDied" type="date" class="w-full p-2 border rounded" />
          </label>
        </div>

        <label>Frase corta / epitafio
          <input id="editShort" class="w-full p-2 border rounded" />
        </label>

        <label>Biografía (html permitido)
          <textarea id="editBio" rows="6" class="w-full p-2 border rounded"></textarea>
        </label>

        <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
          <div>
            <label>Foto principal
              <input id="editMainPhoto" type="file" accept="image/*" class="w-full" />
            </label>
          </div>
          <div>
            <label>Fondo (imagen)
              <input id="editBackground" type="file" accept="image/*" class="w-full" />
            </label>
          </div>
        </div>

        <div>
          <h4 class="font-medium mb-2">Galería - subir imágenes</h4>
          <input id="editGalleryInput" type="file" accept="image/*" multiple />
          <div id="galleryPreview" class="grid grid-cols-3 gap-2 mt-3"></div>
        </div>

        <div>
          <h4 class="font-medium mb-2">Videos (enlaza YouTube — pega sólo el ID o URL)</h4>
          <input id="editVideoInput" placeholder="https://www.youtube.com/watch?v=VIDEOID o VIDEOID" class="w-full p-2 border rounded" />
          <button id="btnAddVideo" class="mt-2 px-3 py-1 bg-blue-600 text-white rounded">Agregar video</button>
          <div id="videoPreview" class="mt-3 space-y-2"></div>
        </div>

        <div>
          <h4 class="font-medium mb-2">Colores / estilos</h4>
          <label>Color de texto principal <input id="editTextColor" type="color" class="ml-2" /></label>
          <label class="ml-4">Color de fondo <input id="editBgColor" type="color" class="ml-2" /></label>
        </div>

        <div>
          <h4 class="font-medium mb-2">Seguridad</h4>
          <label>Nueva contraseña de admin (déjalo vacío para no cambiar)
            <input id="editPassword" type="password" class="w-full p-2 border rounded" />
          </label>
          <p class="text-sm text-gray-600 mt-1">La contraseña se guarda en tu navegador (localStorage). Para seguridad real usa un servidor.</p>
        </div>

      </div>
    </div>
  </div>

<script>
/* -------------------------
  Estructura de datos en memoria
  ------------------------- */
const DEFAULT = {
  meta: { title: "Página conmemorativa", created: new Date().toISOString() },
  person: {
    name: "Nombre Apellido",
    born: "1950-01-01",
    died: "2025-01-01",
    short: "Siempre en nuestros corazones",
    bio: "Aquí va la biografía completa. Puedes usar <strong>HTML</strong> simple.",
    mainPhoto: "",      // base64 dataURL
    background: "",     // base64 dataURL
    textColor: "#111827",
    bgColor: "#f3f4f6"
  },
  gallery: [],          // array of base64 images
  videos: [],           // array of youtube IDs or embeddable urls
  visitorUploads: [],   // {name, dataURL, date}
  comments: []          // {name, text, date}
};

/* CACHES y LLAVES */
const STORAGE_KEY = "tributo_state_v1";
const ADMIN_PW_KEY = "tributo_admin_pw";
const PERMALINK_PARAM = "state"; // we'll use url hash encoding

/* Firebase variables (opcional)
  Si quieres persistencia real entre usuarios activa USE_FIREBASE=true e
  introduce tu config abajo. Debes crear un proyecto en Firebase, activar Realtime DB o Firestore y Storage.
*/
const USE_FIREBASE = false; // cambiar a true si activas Firebase
const FIREBASE_CONFIG = {
  // copiar aquí tu firebase config si lo activas
  // apiKey: "...", authDomain: "...", projectId: "...", storageBucket: "...", messagingSenderId: "...", appId: "..."
};

let state = { ...DEFAULT };

/* -------------------------
  Utilidades: base64 encoder/decoder para JSON (compacto)
  ------------------------- */
function encodeStateToHash(obj){
  const s = JSON.stringify(obj);
  return btoa(unescape(encodeURIComponent(s)));
}
function decodeStateFromHash(hash){
  try {
    const s = decodeURIComponent(escape(atob(hash)));
    return JSON.parse(s);
  } catch(e){ return null; }
}

/* -------------------------
  Guardar / Cargar local
  ------------------------- */
function saveLocal(){
  localStorage.setItem(STORAGE_KEY, JSON.stringify(state));
  document.getElementById("statusText").textContent = "Guardado localmente.";
}
function loadLocal(){
  const raw = localStorage.getItem(STORAGE_KEY);
  if(raw){
    try { state = JSON.parse(raw); return true; }
    catch(e){ console.warn("JSON parse error", e); }
  }
  return false;
}

/* -------------------------
  Render: actualiza DOM desde state
  ------------------------- */
function renderAll(){
  document.getElementById("siteTitle").textContent = state.meta.title || "Página conmemorativa";
  document.getElementById("personName").textContent = state.person.name;
  const born = state.person.born ? new Date(state.person.born).toLocaleDateString() : "";
  const died = state.person.died ? new Date(state.person.died).toLocaleDateString() : "";
  document.getElementById("personDates").textContent = born + (born && died ? " — " : "") + died;
  document.getElementById("personShort").textContent = state.person.short;
  document.getElementById("bioText").innerHTML = state.person.bio;
  document.getElementById("mainPhoto").src = state.person.mainPhoto || "data:image/svg+xml;base64," + btoa(`<svg xmlns='http://www.w3.org/2000/svg' width='400' height='400'><rect width='100%' height='100%' fill='#e5e7eb'/><text x='50%' y='50%' dominant-baseline='middle' text-anchor='middle' fill='#9ca3af' font-size='24'>Sin foto</text></svg>`);
  document.getElementById("hero").style.background = state.person.background ? `url(${state.person.background})` : state.person.bgColor;
  document.body.style.backgroundColor = state.person.bgColor || "#f3f4f6";
  document.body.style.color = state.person.textColor || "#111827";
  document.getElementById("currentUrl").textContent = location.href;

  // gallery
  const g = document.getElementById("galleryGrid"); g.innerHTML = "";
  state.gallery.forEach((img, i)=>{
    const el = document.createElement("div");
    el.innerHTML = `<img src="${img}" class="img-thumb" alt="Foto ${i+1}" />`;
    g.appendChild(el);
  });

  // videos
  const vlist = document.getElementById("videoList"); vlist.innerHTML = "";
  state.videos.forEach(v=>{
    const id = extractYouTubeID(v);
    const iframe = document.createElement("iframe");
    iframe.width = "100%"; iframe.height = "250";
    iframe.src = `https://www.youtube.com/embed/${id}`;
    iframe.className = "rounded-lg shadow";
    vlist.appendChild(iframe);
  });

  // visitor shared images
  const sg = document.getElementById("sharedGrid"); sg.innerHTML = "";
  state.visitorUploads.slice().reverse().forEach(u=>{
    const div = document.createElement("div");
    div.innerHTML = `<img alt="${u.name||'foto'}" src="${u.data}" class="img-thumb" /><p class="text-sm mt-1">${u.name||'Anónimo'} <span class="text-xs text-gray-400"> · ${new Date(u.date).toLocaleString()}</span></p>`;
    sg.appendChild(div);
  });

  // comments
  renderComments();
}

/* -------------------------
  Comentarios (local / firebase opcional)
  ------------------------- */
function renderComments(){
  const list = document.getElementById("commentsList");
  list.innerHTML = "";
  (state.comments.slice().reverse()).forEach(c=>{
    const div = document.createElement("div");
    div.className = "p-3 bg-gray-50 rounded";
    div.innerHTML = `<strong>${escapeHtml(c.name||"Anónimo")}</strong> <span class="text-xs text-gray-400">· ${new Date(c.date).toLocaleString()}</span><div class="mt-1">${escapeHtml(c.text)}</div>`;
    list.appendChild(div);
  });
}
function addLocalComment(name, text){
  state.comments.push({ name, text, date: new Date().toISOString() });
  saveLocal(); renderComments();
}

/* -------------------------
  Helpers pequeños
  ------------------------- */
function escapeHtml(s){ return (s||"").replace(/[&<>'"]/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;',"'":"&#39;",'"':'&quot;'})[c]); }
function fileToDataURL(file){ return new Promise((res,rej)=>{ const fr=new FileReader(); fr.onload=()=>res(fr.result); fr.onerror=rej; fr.readAsDataURL(file); }); }
function extractYouTubeID(s){
  if(!s) return null;
  // si es solo ID
  if(/^[A-Za-z0-9_-]{6,}$/.test(s)) return s;
  const m = s.match(/(?:v=|\/embed\/|youtu\.be\/|\/v\/)([A-Za-z0-9_-]{6,})/);
  return m ? m[1] : s;
}

/* -------------------------
  Event Listeners DOM load
  ------------------------- */
document.addEventListener("DOMContentLoaded", async ()=>{
  // carga desde URL hash si existe
  const hash = location.hash && location.hash.startsWith("#") ? location.hash.slice(1) : "";
  if(hash){
    const hobj = decodeStateFromHash(hash);
    if(hobj){ state = hobj; document.getElementById("statusText").textContent = "Cargado desde enlace compartido."; saveLocal(); }
  } else {
    loadLocal() || saveLocal();
    document.getElementById("statusText").textContent = "Cargado desde almacenamiento local.";
  }
  renderAll();
  document.querySelectorAll(".fade").forEach((el,i)=>setTimeout(()=>el.classList.add("show"), i*120));

  // botones
  document.getElementById("btnAdmin").addEventListener("click", enterAdminFlow);
  document.getElementById("btnEdit").addEventListener("click", enterAdminFlow);
  document.getElementById("btnCloseAdmin").addEventListener("click", ()=>document.getElementById("adminModal").classList.add("hidden"));
  document.getElementById("btnSaveAdmin").addEventListener("click", saveAdminChanges);
  document.getElementById("btnPublish").addEventListener("click", generatePermalink);
  document.getElementById("btnExport").addEventListener("click", exportJSON);
  document.getElementById("btnImport").addEventListener("click", ()=>document.getElementById("importFile").click());
  document.getElementById("importFile").addEventListener("change", handleImportFile);

  document.getElementById("editGalleryInput").addEventListener("change", async (e)=>{
    const files = [...e.target.files];
    const container = document.getElementById("galleryPreview"); container.innerHTML = "";
    for(const f of files){
      const d = await fileToDataURL(f);
      const img = document.createElement("img"); img.src = d; img.className="img-thumb";
      container.appendChild(img);
    }
  });

  document.getElementById("btnAddVideo").addEventListener("click", ()=>{
    const val = document.getElementById("editVideoInput").value.trim();
    if(!val) return;
    const id = extractYouTubeID(val);
    if(id){ state.videos.push(id); document.getElementById("videoPreview").innerHTML += `<div class="p-2 bg-gray-100 rounded">https://youtu.be/${id}</div>`; document.getElementById("editVideoInput").value=""; saveLocal(); renderAll(); }
  });

  document.getElementById("editMainPhoto").addEventListener("change", async e=>{
    const f = e.target.files[0]; if(!f) return;
    state.person.mainPhoto = await fileToDataURL(f); saveLocal(); renderAll();
  });
  document.getElementById("editBackground").addEventListener("change", async e=>{
    const f = e.target.files[0]; if(!f) return;
    state.person.background = await fileToDataURL(f); saveLocal(); renderAll();
  });

  document.getElementById("btnUploadVisitor").addEventListener("click", async ()=>{
    const inp = document.getElementById("visitorPhotoInput");
    if(!inp.files[0]) return alert("Selecciona una foto.");
    const d = await fileToDataURL(inp.files[0]);
    const name = document.getElementById("visitorNameInput").value.trim();
    state.visitorUploads.push({ name, data: d, date: new Date().toISOString() });
    saveLocal(); renderAll();
    inp.value=""; document.getElementById("visitorNameInput").value="";
    alert("Gracias por compartir tu foto. (Almacenada localmente)");
  });

  document.getElementById("btnComment").addEventListener("click", ()=>{
    const text = document.getElementById("commentInput").value.trim();
    const name = document.getElementById("commentName").value.trim();
    if(!text) return;
    addLocalComment(name, text);
    document.getElementById("commentInput").value=""; document.getElementById("commentName").value="";
  });

  document.getElementById("btnClearComments").addEventListener("click", ()=>{
    if(confirm("¿Borrar todos los comentarios locales? Esto solo afecta a este navegador.")){
      state.comments = []; saveLocal(); renderComments();
    }
  });

  document.getElementById("btnExport").addEventListener("click", exportJSON);

  // Import / export file basic
  function exportJSON(){
    const blob = new Blob([JSON.stringify(state)], { type: "application/json" });
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url; a.download = "tributo_export.json"; a.click();
    URL.revokeObjectURL(url);
  }
  async function handleImportFile(e){
    const f = e.target.files[0]; if(!f) return;
    const txt = await f.text();
    try {
      const obj = JSON.parse(txt);
      if(confirm("Importar datos sustituirá el estado actual. ¿Continuar?")){
        state = obj; saveLocal(); renderAll(); alert("Importado.");
      }
    } catch(err){ alert("Archivo JSON inválido."); }
    e.target.value="";
  }
});

/* -------------------------
  ADMIN: flujo de entrada y guardado
  ------------------------- */
async function enterAdminFlow(){
  // preguntar contraseña (si existe) o configurarla
  const existing = localStorage.getItem(ADMIN_PW_KEY);
  if(!existing){
    // establecer contraseña inicial
    const pw = prompt("No hay contraseña de administrador. Crea una ahora (mínimo 4 caracteres):", "admin123");
    if(!pw || pw.length<4){ alert("Contraseña no válida."); return; }
    localStorage.setItem(ADMIN_PW_KEY, btoa(pw)); alert("Contraseña guardada en tu navegador. Ahora entra con ella.");
  }
  const input = prompt("Introduce la contraseña de administrador:");
  if(!input) return;
  if(btoa(input) !== localStorage.getItem(ADMIN_PW_KEY)){ alert("Contraseña incorrecta."); return; }
  // mostrar modal y llenar campos
  showAdminModal();
}
function showAdminModal(){
  document.getElementById("adminModal").classList.remove("hidden");
  // rellenar campos
  document.getElementById("editName").value = state.person.name;
  document.getElementById("editBorn").value = state.person.born || "";
  document.getElementById("editDied").value = state.person.died || "";
  document.getElementById("editShort").value = state.person.short || "";
  document.getElementById("editBio").value = state.person.bio || "";
  document.getElementById("galleryPreview").innerHTML = "";
  state.gallery.forEach(g=>{
    const img = document.createElement("img"); img.src = g; img.className = "img-thumb";
    document.getElementById("galleryPreview").appendChild(img);
  });
  document.getElementById("videoPreview").innerHTML = state.videos.map(v=>`<div class="p-2 bg-gray-100 rounded">https://youtu.be/${v}</div>`).join("");
  document.getElementById("editTextColor").value = state.person.textColor || "#111827";
  document.getElementById("editBgColor").value = state.person.bgColor || "#f3f4f6";
}

async function saveAdminChanges(){
  // tomar valores
  state.person.name = document.getElementById("editName").value || state.person.name;
  state.person.born = document.getElementById("editBorn").value || state.person.born;
  state.person.died = document.getElementById("editDied").value || state.person.died;
  state.person.short = document.getElementById("editShort").value || state.person.short;
  state.person.bio = document.getElementById("editBio").value || state.person.bio;
  state.person.textColor = document.getElementById("editTextColor").value;
  state.person.bgColor = document.getElementById("editBgColor").value;

  // Gallery new files
  const gfiles = document.getElementById("editGalleryInput").files;
  for(const f of gfiles){ const d = await fileToDataURL(f); state.gallery.push(d); }

  // new password
  const newpw = document.getElementById("editPassword").value.trim();
  if(newpw && newpw.length>=4){ localStorage.setItem(ADMIN_PW_KEY, btoa(newpw)); alert("Contraseña actualizada."); }
  // cerrar modal y guardar
  saveLocal(); renderAll();
  document.getElementById("editPassword").value="";
  document.getElementById("adminModal").classList.add("hidden");
  alert("Cambios guardados.");
}

/* -------------------------
  Generar enlace compartible (estado en hash)
  ------------------------- */
function generatePermalink(){
  const encoded = encodeStateToHash(state);
  const url = location.origin + location.pathname + "#" + encoded;
  // Pedimos copiar al portapapeles
  navigator.clipboard?.writeText(url).then(()=>{
    alert("Enlace generado y copiado al portapapeles. Pégalo donde quieras compartirlo.");
    document.getElementById("currentUrl").textContent = url;
  }).catch(()=>{
    prompt("Copia este enlace manualmente:", url);
  });
}

/* -------------------------
  Import/Export JSON (ya implementado arriba)
  ------------------------- */

/* -------------------------
  Inicial: si hay hash en URL se muestra (ya hecho) y renderAll
  ------------------------- */

</script>

</body>
</html>
