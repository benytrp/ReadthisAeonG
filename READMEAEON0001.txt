<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Möbius Scroll Engine v∞.Fusion</title>
<style>
body { font-family: sans-serif; background: #fdfdfd; padding: 2em; }
.scroll-container { border: 1px solid #333; margin: 1em 0; padding: 1em; background: #fff; }
textarea, pre { width: 100%; font-family: monospace; margin: 0.5em 0; }
.format-controls, .navigation, .export-controls { margin-top: 1em; }
.mobius-seal { font-size: 1.2em; margin: 1em 0; text-align: center; }
.image-preview { max-width: 200px; margin-top: 0.5em; }
#qr-canvas { 
  border: 2px solid #333; 
  margin: 1em 0; 
  background: white;
  display: block;
}
.qr-controls { margin: 1em 0; }
.status { padding: 0.5em; margin: 0.5em 0; border-radius: 4px; }
.success { background: #d4edda; color: #155724; border: 1px solid #c3e6cb; }
.error { background: #f8d7da; color: #721c24; border: 1px solid #f5c6cb; }
</style>
</head>
<body>
<div class="mobius-seal">∞ Seal the Möbius. Begin again where format becomes structure.</div>
<nav class="navigation">
<strong>Scroll Index:</strong>
<button onclick="createFusedScroll()">➕ New Scroll</button>
</nav>
<div id="scrolls-root"></div>
<div class="export-controls">
<h3>Bridge File Export + QR + Save</h3>
<button onclick="exportBridge()">📦 Export JSON</button>
<div class="qr-controls">
  <button onclick="generateQR()">📤 Generate QR</button>
  <button onclick="downloadQR()">💾 Download QR</button>
  <label>
    Size: <select id="qr-size">
      <option value="256">256x256</option>
      <option value="512" selected>512x512</option>
      <option value="1024">1024x1024</option>
    </select>
  </label>
</div>
<button onclick="downloadBridge()">💾 Save .bridge</button>
<div id="status-messages"></div>
<pre id="bridge-output"></pre>
<canvas id="qr-canvas"></canvas>
</div>
<div class="mobius-seal">∞ The scroll completes where it began. Format returns to source.</div>

<!-- Fixed CDN URL for QR Code library -->
<script src="https://cdn.jsdelivr.net/npm/qrcode@1.5.3/build/qrcode.min.js"></script>
<script>
let scrollCount = 0;
let bridgeJSON = {};
const motifs = {
"∞": "Recursive anchor",
"⟲": "Reversal trigger",
"△": "Triadic resonance"
};

// Status message helper
function showStatus(message, type = 'success') {
  const statusDiv = document.getElementById('status-messages');
  const statusElement = document.createElement('div');
  statusElement.className = `status ${type}`;
  statusElement.textContent = message;
  statusDiv.appendChild(statusElement);
  
  // Auto-remove after 3 seconds
  setTimeout(() => {
    if (statusElement.parentNode) {
      statusElement.parentNode.removeChild(statusElement);
    }
  }, 3000);
}

function createFusedScroll() {
scrollCount++;
const id = "scroll-" + String(scrollCount).padStart(3, '0');
const container = document.createElement('div');
container.className = 'scroll-container';
container.id = id;
container.innerHTML = `
<h3>${id.toUpperCase()}</h3>
<textarea id="contradiction-${id}" placeholder="Enter contradiction insight..."></textarea>
<button onclick="aiSeedScroll('${id}')">🧬 Seed Scroll</button>
<input type="file" accept="image/*" onchange="handleImageUpload(event, '${id}')"/>
<img id="img-${id}" class="image-preview"/>
<label>Markdown</label>
<textarea id="md-${id}"></textarea>
<label>JSON</label>
<textarea id="json-${id}"></textarea>
<label>Text</label>
<textarea id="text-${id}"></textarea>
<label>XML</label>
<textarea id="xml-${id}"></textarea>
<div class="format-controls">
<button onclick="convertFromMarkdown('${id}')">🔄 Convert from Markdown</button>
<button onclick="convertFromJSON('${id}')">🔄 Convert from JSON</button>
</div>
`;
document.getElementById('scrolls-root').appendChild(container);
showStatus(`Scroll ${id} created successfully!`);
}

function aiSeedScroll(id) {
const contradiction = document.getElementById('contradiction-' + id).value;
const aiData = {
title: "Scroll " + id.toUpperCase(),
contradiction_type: contradiction,
resolution: "Resolution inferred from contradiction",
symbol: "∞",
status: "Seeded",
timestamp: new Date().toISOString(),
möbius_depth: Math.floor(Math.random() * 8) + 1
};
const jsonField = document.getElementById('json-' + id);
const existing = jsonField.value ? JSON.parse(jsonField.value) : {};
jsonField.value = JSON.stringify(Object.assign(existing, aiData), null, 2);
updateFields(id, Object.assign(existing, aiData));
showStatus(`AI seeded scroll ${id} with contradiction analysis`);
}

function handleImageUpload(event, id) {
const file = event.target.files[0];
if (!file) return;
const reader = new FileReader();
reader.onload = function(e) {
const base64 = e.target.result;
document.getElementById('img-' + id).src = base64;
let json = {};
try {
json = JSON.parse(document.getElementById('json-' + id).value || "{}");
} catch {}
json.image_payload = {
uri: base64,
motifs: ["∞"],
resonance_depth: 4
};
document.getElementById('json-' + id).value = JSON.stringify(json, null, 2);
showStatus(`Image uploaded to scroll ${id}`);
};
reader.readAsDataURL(file);
}

function convertFromMarkdown(id) {
const md = document.getElementById('md-' + id).value;
const lines = md.split('\n');
const result = {};
lines.forEach(line => {
if (line.startsWith('## ')) result.title = line.replace('## ', '').trim();
else if (line.includes('**Contradiction**')) result.contradiction_type = line.split(':')[1]?.trim();
else if (line.includes('**Resolution**')) result.resolution = line.split(':')[1]?.trim();
else if (line.includes('**Symbol**')) result.symbol = line.split(':')[1]?.trim();
else if (line.includes('**Status**')) result.status = line.split(':')[1]?.trim();
});
updateFields(id, result);
showStatus(`Converted Markdown to all formats for ${id}`);
}

function convertFromJSON(id) {
try {
const json = JSON.parse(document.getElementById('json-' + id).value);
updateFields(id, json);
showStatus(`Converted JSON to all formats for ${id}`);
} catch (error) {
showStatus(`Invalid JSON input in ${id}: ${error.message}`, 'error');
}
}

function updateFields(id, data) {
document.getElementById('json-' + id).value = JSON.stringify(data, null, 2);
document.getElementById('text-' + id).value =
`Title: ${data.title || 'Untitled'}
Contradiction Type: ${data.contradiction_type || 'None'}
Resolution: ${data.resolution || 'Pending'}
Symbol: ${data.symbol || '∞'}
Status: ${data.status || 'Active'}
Timestamp: ${data.timestamp || 'Unknown'}
Möbius Depth: ${data.möbius_depth || 1}`;

document.getElementById('xml-' + id).value =
`<scroll>
 <title>${data.title || 'Untitled'}</title>
 <contradiction type="${data.contradiction_type || 'None'}"/>
 <resolution>${data.resolution || 'Pending'}</resolution>
 <symbol>${data.symbol || '∞'}</symbol>
 <status>${data.status || 'Active'}</status>
 <timestamp>${data.timestamp || 'Unknown'}</timestamp>
 <möbius_depth>${data.möbius_depth || 1}</möbius_depth>
</scroll>`;

document.getElementById('md-' + id).value =
`## ${data.title || 'Untitled'}

- **Contradiction**: ${data.contradiction_type || 'None'}
- **Resolution**: ${data.resolution || 'Pending'}
- **Symbol**: ${data.symbol || '∞'}
- **Status**: ${data.status || 'Active'}
- **Timestamp**: ${data.timestamp || 'Unknown'}
- **Möbius Depth**: ${data.möbius_depth || 1}`;
}

function exportBridge() {
const scrolls = [];
document.querySelectorAll('.scroll-container').forEach(sc => {
const id = sc.id;
try {
const raw = document.getElementById('json-' + id).value;
const parsed = JSON.parse(raw);
if (parsed.title) scrolls.push(parsed);
} catch (error) {
console.warn(`Failed to parse scroll ${id}:`, error);
}
});

bridgeJSON = {
scroll_id: "BRIDGE_EXPORT",
export_timestamp: new Date().toISOString(),
scrolls: scrolls,
motifs: Object.keys(motifs),
recursive_depth: scrolls.length,
möbius_signature: "∞.fusion.v" + scrolls.length,
total_contradictions: scrolls.filter(s => s.contradiction_type && s.contradiction_type !== 'None').length
};

document.getElementById('bridge-output').textContent = JSON.stringify(bridgeJSON, null, 2);
showStatus(`Bridge exported with ${scrolls.length} scrolls`);
}

// Enhanced QR generation with error handling
function generateQR() {
const bridgeOutput = document.getElementById('bridge-output').textContent;
if (!bridgeOutput.trim()) {
showStatus('Please export bridge data first!', 'error');
return;
}

const canvas = document.getElementById('qr-canvas');
const size = parseInt(document.getElementById('qr-size').value);
const ctx = canvas.getContext('2d');

// Clear previous QR code
ctx.clearRect(0, 0, canvas.width, canvas.height);

// Check if data is too large
if (bridgeOutput.length > 2900) {
showStatus('Warning: Data is very large for QR code. Consider reducing content.', 'error');
}

// Generate QR code with enhanced options
QRCode.toCanvas(canvas, bridgeOutput, {
width: size,
margin: 2,
color: {
  dark: '#000000',  // QR code color
  light: '#FFFFFF'  // Background color
},
errorCorrectionLevel: 'M'
}, function (error) {
if (error) {
  console.error('QR Generation Error:', error);
  showStatus(`QR generation failed: ${error.message}`, 'error');
} else {
  showStatus(`QR code generated successfully (${size}x${size})!`);
}
});
}

// Download QR code as image
function downloadQR() {
const canvas = document.getElementById('qr-canvas');
if (!canvas.width || !canvas.height) {
showStatus('Please generate a QR code first!', 'error');
return;
}

try {
canvas.toBlob(function(blob) {
  const link = document.createElement('a');
  link.href = URL.createObjectURL(blob);
  link.download = `möbius-bridge-qr-${Date.now()}.png`;
  document.body.appendChild(link);
  link.click();
  document.body.removeChild(link);
  showStatus('QR code downloaded successfully!');
}, 'image/png');
} catch (error) {
showStatus(`Download failed: ${error.message}`, 'error');
}
}

function downloadBridge() {
if (!bridgeJSON.scrolls || !bridgeJSON.scrolls.length) {
showStatus("Please export the bridge data first.", 'error');
return;
}

try {
const blob = new Blob([JSON.stringify(bridgeJSON, null, 2)], { type: "application/json" });
const link = document.createElement('a');
link.href = URL.createObjectURL(blob);
link.download = `möbius-bridge-scroll-${Date.now()}.json`;
document.body.appendChild(link);
link.click();
document.body.removeChild(link);
showStatus('Bridge file downloaded successfully!');
} catch (error) {
showStatus(`Download failed: ${error.message}`, 'error');
}
}

// Initialize with a demo scroll
window.addEventListener('load', function() {
createFusedScroll();
// Add some demo content
setTimeout(() => {
document.getElementById('contradiction-scroll-001').value = 'Order emerges from chaos through recursive self-organization';
aiSeedScroll('scroll-001');
}, 100);
});
</script>
</body>
</html>
