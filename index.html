/* ══════════════════════════════════════════════════════
   NOVALINK SMS — app.js
   ══════════════════════════════════════════════════════ */

/* ════════════════════════════════
   CONFIG — set your Google Apps Script URL here
   ════════════════════════════════ */
const CFG = {
    scriptUrl: 'https://script.google.com/macros/s/AKfycbyoV8iCduvVOCrjP6hHbtA4XynREahpmOhCKBd69QR9bok04jztWTCHkzDSbb2BplUp/exec',
    senderId: 'NovaLink',
    delay: 450,    // ms between sends (avoid rate limiting)
    retries: 2     // auto-retry failed sends
};

/* ════════════════════════════════
   BUILT-IN TEMPLATES
   ════════════════════════════════ */
const BUILTIN_TEMPLATES = [
    {
        id: 'tpl_wifi_voucher',
        name: 'WiFi Voucher',
        category: 'Voucher',
        body:
            `NovaLink WiFi Voucher
Package : {{package}}
Amount  : GH₵{{amount}}
Code    : {{voucher}}

Thank you for choosing NovaLink!`
    },
    {
        id: 'tpl_voucher_short',
        name: 'Voucher (Short)',
        category: 'Voucher',
        body:
            `NovaLink | {{package}} Package
Voucher: {{voucher}}
Amount: GH₵{{amount}}
Connect to NovaLink WiFi → novalink.wlan/login`
    },
    {
        id: 'tpl_renewal_reminder',
        name: 'Renewal Reminder',
        category: 'Reminder',
        body:
            `Hi {{name}}, your NovaLink {{package}} subscription is expiring soon.
Renew now to stay connected!
Call/WhatsApp us or visit novalink.wlan
Thank you!`
    },
    {
        id: 'tpl_welcome',
        name: 'Welcome Message',
        category: 'General',
        body:
            `Welcome to NovaLink, {{name}}!
You're now connected to our network.
For support, reply or visit novalink.wlan
Enjoy browsing!`
    },
    {
        id: 'tpl_promo',
        name: 'Promo Offer',
        category: 'Promo',
        body:
            `Hi {{name}}! Special offer from NovaLink!
Get double data on your next voucher this weekend.
Visit us or call to get yours now.
NovaLink – Stay Connected.`
    }
];

/* ════════════════════════════════
   STATE
   ════════════════════════════════ */
let contacts = [], sendLog = [], activeTag = 'all';
let conv = { headers: [], rows: [] }, convOut = [], convMap = {};
let templates = [...BUILTIN_TEMPLATES];
let editingTplId = null;
let broadcastCancelled = false;
let sentCount = 0, failCount = 0;

const $ = id => document.getElementById(id);

/* ════════════════════════════════
   VARIABLE DETECTION — Quick Message
   ════════════════════════════════ */
// All supported template variables (phone always comes from the phone field)
const VAR_CONFIG = [
    { key: 'name', label: 'Recipient Name', placeholder: 'e.g. Ama Owusu', icon: '👤' },
    { key: 'voucher', label: 'Voucher Code', placeholder: 'e.g. ABCD1234', icon: '🎫' },
    { key: 'package', label: 'Package Name', placeholder: 'e.g. Short 1hr', icon: '📦' },
    { key: 'amount', label: 'Amount (GH₵)', placeholder: 'e.g. 12.00', icon: '💰' },
];

function detectAndRenderVars(msgId, wrapId) {
    const text = $(msgId) ? $(msgId).value : '';
    const wrap = $(wrapId);
    if (!wrap) return;

    // Find which variables are present in the message text
    const found = VAR_CONFIG.filter(v =>
        new RegExp(`\\{\\{${v.key}\\}\\}`, 'i').test(text)
    );

    // Preserve any values the user has already typed
    const saved = {};
    wrap.querySelectorAll('input[data-qvar]').forEach(el => {
        saved[el.dataset.qvar] = el.value;
    });

    if (!found.length) {
        // Animate out if currently showing
        if (wrap.children.length) {
            wrap.style.opacity = '0';
            wrap.style.transform = 'translateY(-4px)';
            setTimeout(() => {
                wrap.innerHTML = '';
                wrap.style.opacity = '';
                wrap.style.transform = '';
            }, 180);
        }
        return;
    }

    wrap.innerHTML = `
      <div style="
        background: var(--bg3);
        border: 1px solid var(--border2);
        border-radius: var(--radius-sm);
        padding: 14px 16px;
        margin-bottom: 13px;
        animation: varsSlideIn .18s cubic-bezier(.4,0,.2,1);
      ">
        <div style="
          font-size: 11px;
          font-weight: 700;
          letter-spacing: .08em;
          text-transform: uppercase;
          color: var(--text3);
          margin-bottom: 12px;
          display: flex;
          align-items: center;
          gap: 6px;
        ">
          <span style="color:var(--amber)">⚡</span> Fill in message variables
        </div>
        <div style="display:grid;grid-template-columns:repeat(auto-fill,minmax(180px,1fr));gap:10px">
          ${found.map(v => `
            <div>
              <label style="display:block;font-size:12px;font-weight:600;color:var(--text2);margin-bottom:5px;letter-spacing:.01em">
                ${v.icon} ${v.label}
                <span style="
                  display:inline-block;
                  background:var(--amber-dim);
                  color:var(--amber);
                  border:1px solid rgba(251,191,36,.25);
                  border-radius:4px;
                  font-size:9px;
                  padding:1px 6px;
                  font-family:var(--font-mono);
                  margin-left:4px;
                  vertical-align:middle;
                ">{{${v.key}}}</span>
              </label>
              <input
                type="text"
                class="fi"
                id="qv_${v.key}"
                data-qvar="${v.key}"
                placeholder="${v.placeholder}"
                value="${esc(saved[v.key] || '')}"
                style="font-size:13px"
              />
            </div>`).join('')}
        </div>
      </div>`;

    wrap.style.opacity = '1';
    wrap.style.transform = 'translateY(0)';
}

/* ════════════════════════════════
   TOAST
   ════════════════════════════════ */
const TOAST_ICONS = { ok: '✓', err: '✕', warn: '⚠', info: 'ℹ' };
let toastTimer;

function toast(msg, type = 'info') {
    const el = $('toast');
    // clear previous type classes
    el.className = '';
    void el.offsetWidth; // reflow to restart animation

    const icon = el.querySelector('.toast-icon') || (() => {
        const s = document.createElement('span');
        s.className = 'toast-icon';
        el.prepend(s);
        return s;
    })();
    const textNode = el.querySelector('.toast-text') || (() => {
        const s = document.createElement('span');
        s.className = 'toast-text';
        el.appendChild(s);
        return s;
    })();

    icon.textContent = TOAST_ICONS[type] || 'ℹ';
    textNode.textContent = msg;

    el.classList.add('show', `t-${type}`);
    clearTimeout(toastTimer);
    toastTimer = setTimeout(() => el.classList.remove('show'), 3800);
}

/* ════════════════════════════════
   NAVIGATION
   ════════════════════════════════ */
function switchTab(id, el) {
    document.querySelectorAll('.panel').forEach(p => p.classList.remove('active'));
    document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
    $(id).classList.add('active');
    el.classList.add('active');
}

/* ════════════════════════════════
   UTILS
   ════════════════════════════════ */
const sleep = ms => new Promise(r => setTimeout(r, ms));

function normPhone(p) {
    p = String(p).replace(/\s+/g, '').replace(/[^\d+]/g, '');
    if (p.startsWith('0')) p = '+233' + p.slice(1);
    if (!p.startsWith('+')) p = '+233' + p;
    return p;
}

function parseCSV(txt) {
    const lines = txt.trim().split(/\r?\n/);
    const headers = lines[0].split(',').map(h => h.trim().replace(/^"|"$/g, ''));
    const rows = lines.slice(1).map(line => {
        const vals = []; let cur = '', inQ = false;
        for (const ch of line) {
            if (ch === '"') { inQ = !inQ; }
            else if (ch === ',' && !inQ) { vals.push(cur.trim()); cur = ''; }
            else cur += ch;
        }
        vals.push(cur.trim());
        const obj = {};
        headers.forEach((h, i) => obj[h] = (vals[i] || '').replace(/^"|"$/g, '').trim());
        return obj;
    }).filter(r => Object.values(r).some(v => v));
    return { headers, rows };
}

function dlBlob(content, name) {
    const a = document.createElement('a');
    a.href = URL.createObjectURL(new Blob([content], { type: 'text/csv' }));
    a.download = name;
    a.click();
}

function resolveVars(body, data = {}) {
    return body
        .replace(/\{\{name\}\}/gi, data.name || 'Customer')
        .replace(/\{\{voucher\}\}/gi, data.voucher || data.voucher_code || 'XXXXXXXX')
        .replace(/\{\{package\}\}/gi, data.package || data.package_name || 'Standard')
        .replace(/\{\{amount\}\}/gi, data.amount || '0.00')
        .replace(/\{\{phone\}\}/gi, data.phone || '+233XXXXXXXXX');
}

function esc(s) {
    return String(s || '')
        .replace(/&/g, '&amp;')
        .replace(/</g, '&lt;')
        .replace(/>/g, '&gt;')
        .replace(/"/g, '&quot;');
}

function updateStats() {
    $('statTotal').textContent = contacts.length;
    $('statSent').textContent = sentCount;
    $('statFail').textContent = failCount;
}

/* ════════════════════════════════
   SMS API CALL (via Google Apps Script)
   ════════════════════════════════ */
const STATUS_CODES = {
    '1000': { ok: true, label: 'Submitted', hint: 'Accepted by gateway' },
    '1002': { ok: false, label: 'Failed', hint: 'Gateway rejected' },
    '1003': { ok: false, label: 'Low balance', hint: 'Top up your account' },
    '1004': { ok: false, label: 'Invalid key', hint: 'Check your API key' },
    '1005': { ok: false, label: 'Bad phone', hint: 'Invalid phone number' },
    '1006': { ok: false, label: 'Bad sender', hint: 'Check sender ID' },
    '1007': { ok: true, label: 'Queued', hint: 'Message is scheduled' },
    '1008': { ok: false, label: 'Empty msg', hint: 'Message body was empty' }
};

/**
 * callSMS — sends a message via Google Apps Script proxy.
 * The API returns JSON: { success, code, data, message }
 * We extract `code` and return it as a string for STATUS_CODES lookup.
 */
async function callSMS(to, msg, attempt = 1) {
    if (!CFG.scriptUrl || CFG.scriptUrl.includes('...')) {
        toast('Set your Google Apps Script URL in CFG.scriptUrl', 'err');
        throw new Error('No script URL configured');
    }
    const url = `${CFG.scriptUrl}?to=${encodeURIComponent(to)}&msg=${encodeURIComponent(msg)}&sender_id=${encodeURIComponent(CFG.senderId)}`;
    try {
        const r = await fetch(url);
        if (!r.ok) throw new Error('HTTP ' + r.status);
        const raw = (await r.text()).trim();

        // API returns JSON — parse it and extract the numeric code
        try {
            const json = JSON.parse(raw);
            // Prefer json.code; fall back to success flag  
            if (json.code !== undefined) return String(json.code);
            if (json.success === true) return '1000';
            if (json.success === false) return '1002';
        } catch (_) {
            // Not JSON — treat as a bare code string (legacy fallback)
        }
        return raw;
    } catch (e) {
        if (attempt <= CFG.retries) {
            await sleep(600 * attempt);
            return callSMS(to, msg, attempt + 1);
        }
        throw e;
    }
}

/* ════════════════════════════════
   BALANCE CHECK
   ════════════════════════════════ */
async function checkBalance(silent = false) {
    const btn = $('balBtn');
    btn.classList.add('loading');
    try {
        const url = `${CFG.scriptUrl}?action=balance`;
        const r = await fetch(url);
        const raw = (await r.text()).trim();

        // Parse JSON response if possible
        let display = raw;
        try {
            const json = JSON.parse(raw);
            // Common balance field names from various APIs
            display = json.balance ?? json.credit ?? json.credits ??
                json.sms_balance ?? json.data?.balance ??
                json.data?.credit ?? raw;
        } catch (_) { /* plain text response — use as-is */ }

        $('balText').textContent = display + ' SMS';
        $('balDisplay').classList.add('show');
        if (!silent) toast('Balance: ' + display + ' SMS credits', 'ok');
    } catch (e) {
        if (!silent) toast('Could not reach API — check Script URL', 'err');
    } finally {
        btn.classList.remove('loading');
    }
}

/* ════════════════════════════════
   CHAR COUNTERS
   ════════════════════════════════ */
function updateCharCount(lenId, pgId, containerId, length) {
    $(lenId).textContent = length;
    $(pgId).textContent = Math.max(1, Math.ceil(length / 160));
    const el = $(containerId);
    el.classList.toggle('warn', length > 140 && length <= 160);
    el.classList.toggle('over', length > 160);
}
function countQ() {
    updateCharCount('qcc', 'qpg', 'qCharCount', $('quickMsg').value.length);
    detectAndRenderVars('quickMsg', 'qVarsWrap');
}
function countB() { updateCharCount('bcc', 'bpg', 'bCharCount', $('msgBody').value.length); }
function countTpl() { $('tplCC').textContent = $('tplBody').value.length; }

/* ════════════════════════════════
   QUICK MESSAGE
   ════════════════════════════════ */
function setQStatus(state, txt) {
    const dot = $('qStatus'), label = $('qStatusTxt');
    dot.className = 'status-dot' + (state === 'ok' ? ' ok' : state === 'err' ? ' err' : '');
    label.textContent = txt;
}

async function sendQuick() {
    const rawPhone = $('quickPhone').value.trim();
    const msg = $('quickMsg').value.trim();
    if (!rawPhone) { toast('Enter a phone number', 'err'); return; }
    if (!msg) { toast('Enter a message', 'err'); return; }

    const phone = normPhone(rawPhone);

    // Gather values from dynamically-rendered variable inputs
    const varData = { phone };
    const wrap = $('qVarsWrap');
    if (wrap) {
        wrap.querySelectorAll('input[data-qvar]').forEach(el => {
            varData[el.dataset.qvar] = el.value.trim();
        });
    }

    const resolved = resolveVars(msg, varData);

    const btn = $('qBtn');
    btn.disabled = true;
    btn.textContent = 'Sending…';
    setQStatus('', 'Sending…');

    try {
        const code = await callSMS(phone, resolved);
        const status = STATUS_CODES[code] || { ok: false, label: 'Code: ' + code, hint: '' };
        addLog(varData.name || phone, phone, resolved, status.ok, status.label, status.hint || '');
        if (status.ok) {
            sentCount++;
            setQStatus('ok', status.label + ' ✓');
            toast('Message submitted to ' + phone, 'ok');
            // Refresh balance silently after successful send
            checkBalance(true);
        } else {
            failCount++;
            setQStatus('err', status.label);
            toast('Send failed: ' + status.label, 'err');
        }
    } catch (e) {
        failCount++;
        setQStatus('err', 'Error');
        toast('Error: ' + e.message, 'err');
        addLog(varData.name || phone, phone, resolved || msg, false, 'Error', e.message);
    }
    updateStats();
    btn.disabled = false;
    btn.textContent = 'Send ↗';
}

/* ════════════════════════════════
   BROADCAST
   ════════════════════════════════ */
function getRecipients() {
    const filter = $('recipientFilter').value;
    if (filter === 'all') return contacts;
    const sel = contacts.filter(c => c._selected);
    return sel.length ? sel : contacts.filter(c => {
        const tags = (c.tags || '').split(',').map(t => t.trim().toLowerCase());
        return tags.includes(filter.toLowerCase());
    });
}

function updateRCount() {
    const n = getRecipients().length;
    $('rCount').textContent = n + ' recipient' + (n !== 1 ? 's' : '');
}

function previewB() {
    const msg = $('msgBody').value.trim();
    if (!msg) { toast('Write a message first', 'err'); return; }
    const sample = contacts[0] || { name: 'Ama Owusu', phone: '+233241234567', voucher_code: 'DEMO1234', package_name: 'Short', amount: '12.00' };
    const preview = resolveVars(msg, { ...sample, phone: normPhone(sample.phone || '+233000000000') });
    alert('Preview for first contact:\n\n' + preview);
}

function cancelBroadcast() {
    broadcastCancelled = true;
    toast('Stopping broadcast…', 'warn');
}

async function sendBroadcast() {
    const msg = $('msgBody').value.trim();
    if (!msg) { toast('Write a broadcast message', 'err'); return; }
    const recipients = getRecipients();
    if (!recipients.length) { toast('No recipients selected', 'err'); return; }

    const confirmed = confirm(`Send to ${recipients.length} contact(s)?`);
    if (!confirmed) return;

    broadcastCancelled = false;
    const sendBtn = $('sendBtn');
    const cancelBtn = $('cancelBtn');
    sendBtn.disabled = true;
    cancelBtn.style.display = 'inline-flex';
    $('progWrap').style.display = 'block';

    let sent = 0, failed = 0;

    for (let i = 0; i < recipients.length; i++) {
        if (broadcastCancelled) break;
        const c = recipients[i];
        const phone = normPhone(c.phone || c.Phone || '');
        if (!phone || phone.length < 10) {
            addLog(c.name || '—', phone, '—', false, 'Bad phone');
            failed++; failCount++;
            continue;
        }
        const resolved = resolveVars(msg, { ...c, phone });
        const pct = Math.round((i / recipients.length) * 100);
        $('progBar').style.width = pct + '%';
        $('progPct').textContent = pct + '%';
        $('progLabel').textContent = `Sending ${i + 1} / ${recipients.length}…`;

        try {
            const code = await callSMS(phone, resolved);
            const status = STATUS_CODES[code] || { ok: false, label: 'Code: ' + code, hint: '' };
            addLog(c.name || phone, phone, resolved, status.ok, status.label, status.hint || '');
            if (status.ok) { sent++; sentCount++; } else { failed++; failCount++; }
        } catch (e) {
            console.error('Broadcast send error for', phone, e);
            addLog(c.name || phone, phone, resolved, false, 'Error', e.message || 'Network error');
            failed++; failCount++;
        }
        updateStats();
        if (i < recipients.length - 1) await sleep(CFG.delay);
    }

    $('progBar').style.width = '100%';
    $('progPct').textContent = '100%';
    $('progLabel').textContent = broadcastCancelled
        ? `Stopped · ${sent} sent, ${failed} failed`
        : `Done · ${sent} sent, ${failed} failed`;

    toast(
        broadcastCancelled
            ? `Stopped — ${sent} sent, ${failed} failed`
            : `Broadcast complete — ${sent} sent, ${failed} failed`,
        failed === 0 ? 'ok' : 'warn'
    );

    sendBtn.disabled = false;
    cancelBtn.style.display = 'none';

    // Refresh balance after broadcast
    checkBalance(true);
}

/* ════════════════════════════════
   SEND LOG
   ════════════════════════════════ */
function addLog(name, phone, msg, ok, label, hint = '') {
    sendLog.push({ name, phone, msg, ok, label, hint, time: new Date() });
    renderLog();
}

function renderLog() {
    const el = $('sendLog');
    if (!sendLog.length) {
        el.innerHTML = `
          <div class="log-empty">
            <div style="font-size:22px;margin-bottom:8px">📭</div>
            No messages sent yet
          </div>`;
        return;
    }
    el.innerHTML = sendLog.slice().reverse().map((e, i) => {
        const num = sendLog.length - i;
        const time = e.time.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
        // truncate message to ~80 chars for preview
        const msgPreview = e.msg.length > 80
            ? esc(e.msg.slice(0, 80)) + '<span style="color:var(--text3)">…</span>'
            : esc(e.msg);
        const badgeClass = e.ok ? 'b-ok' : 'b-err';
        const hint = e.hint ? `title="${esc(e.hint)}"` : '';
        return `
<div class="log-entry">
  <div class="log-num">${num}</div>
  <div class="log-body">
    <div class="log-header">
      <span class="log-name">${esc(e.name || '—')}</span>
      <span class="log-phone">${esc(e.phone)}</span>
    </div>
    <div class="log-preview" title="${esc(e.msg)}">${msgPreview}</div>
  </div>
  <div class="log-meta">
    <span class="badge ${badgeClass}" ${hint}>${e.label}</span>
    <span class="log-time">${time}</span>
  </div>
</div>`;
    }).join('');
}

function clearLog() {
    if (!sendLog.length) return;
    if (confirm('Clear send log?')) { sendLog = []; renderLog(); toast('Log cleared', 'info'); }
}

function exportLog() {
    if (!sendLog.length) { toast('Nothing to export', 'warn'); return; }
    const rows = [['#', 'Name', 'Phone', 'Message', 'Status', 'Time']];
    sendLog.forEach((e, i) => rows.push([
        i + 1, e.name, e.phone,
        '"' + e.msg.replace(/"/g, '""') + '"',
        e.label,
        e.time.toLocaleString()
    ]));
    dlBlob(rows.map(r => r.join(',')).join('\n'), 'novalink_log_' + Date.now() + '.csv');
}

/* ════════════════════════════════
   CONTACTS
   ════════════════════════════════ */
function cDrop(e) {
    e.preventDefault();
    $('cDropZone').classList.remove('drag');
    const file = e.dataTransfer.files[0];
    if (file) loadContactsFile(file);
}

function loadContacts(event) { loadContactsFile(event.target.files[0]); }

function loadContactsFile(file) {
    if (!file) return;
    const reader = new FileReader();
    reader.onload = e => {
        const { headers, rows } = parseCSV(e.target.result);
        const phoneKey = headers.find(h => /phone/i.test(h)) || headers[0];
        contacts = rows.map(r => ({
            ...r,
            phone: r[phoneKey] || r.phone || r.Phone || '',
            name: r.name || r.Name || '',
            tags: r.tags || r.Tags || ''
        })).filter(r => r.phone);
        updateStats();
        renderContacts();
        buildTagFilters();
        buildRecipientFilter();
        toast(`Loaded ${contacts.length} contacts`, 'ok');
    };
    reader.readAsText(file);
}

let cFilter = '', cTagFilter = 'all';

function filterC(v) { cFilter = v.toLowerCase(); renderContacts(); }

function buildTagFilters() {
    const tags = new Set(['all']);
    contacts.forEach(c => (c.tags || '').split(',').map(t => t.trim()).filter(Boolean).forEach(t => tags.add(t)));
    const el = $('tagFilters');
    el.innerHTML = [...tags].map(t => `
  <span class="chip ${t === cTagFilter ? 'on' : 'off'}" onclick="setTagFilter('${t}')">${t === 'all' ? 'All' : t}</span>
`).join('');
}

function setTagFilter(t) { cTagFilter = t; buildTagFilters(); renderContacts(); }

function getFilteredContacts() {
    return contacts.filter(c => {
        const matchTag = cTagFilter === 'all' || (c.tags || '').split(',').map(x => x.trim()).includes(cTagFilter);
        const matchSearch = !cFilter ||
            (c.name || '').toLowerCase().includes(cFilter) ||
            (c.phone || '').includes(cFilter);
        return matchTag && matchSearch;
    });
}

function renderContacts() {
    const filtered = getFilteredContacts();
    $('cCount').textContent = contacts.length;
    const tbody = $('cBody');
    if (!filtered.length) {
        tbody.innerHTML = '<tr><td colspan="6" style="text-align:center;color:var(--text3);padding:28px 0">No contacts found</td></tr>';
        return;
    }
    tbody.innerHTML = filtered.map((c, i) => `
  <tr>
    <td><input type="checkbox" ${c._selected ? 'checked' : ''} onchange="toggleSel(${contacts.indexOf(c)})" /></td>
    <td style="color:var(--text3);font-size:12px">${i + 1}</td>
    <td>${esc(c.name || '—')}</td>
    <td style="font-family:var(--font-mono);font-size:12px">${esc(normPhone(c.phone))}</td>
    <td>${(c.tags || '').split(',').filter(Boolean).map(t => `<span class="tag-chip">${esc(t.trim())}</span>`).join('')}</td>
    <td style="font-family:var(--font-mono);font-size:12px;color:var(--text2)">${esc(c.voucher_code || '—')}</td>
  </tr>`).join('');
    updateSelCount();
}

function toggleSel(idx) { contacts[idx]._selected = !contacts[idx]._selected; updateSelCount(); }

function toggleAll() {
    const checked = $('selAll').checked;
    getFilteredContacts().forEach(c => c._selected = checked);
    renderContacts();
}

function updateSelCount() {
    const n = contacts.filter(c => c._selected).length;
    $('selCount').textContent = n + ' selected';
}

function clearContacts() {
    if (!contacts.length) return;
    if (confirm('Remove all contacts?')) {
        contacts = [];
        renderContacts();
        buildTagFilters();
        buildRecipientFilter();
        updateStats();
        toast('Contacts cleared', 'info');
    }
}

function goMsg() {
    const sel = contacts.filter(c => c._selected);
    if (!sel.length) { toast('Select contacts first', 'warn'); return; }
    $('recipientFilter').value = 'selected';
    updateRCount();
    switchTab('messaging', document.querySelectorAll('.tab')[0]);
    toast(sel.length + ' contacts ready to message', 'info');
}

function buildRecipientFilter() {
    const tags = new Set();
    contacts.forEach(c => (c.tags || '').split(',').map(t => t.trim()).filter(Boolean).forEach(t => tags.add(t)));
    const sel = $('recipientFilter');
    sel.innerHTML = '<option value="all">All contacts</option>' +
        '<option value="selected">Selected contacts</option>' +
        [...tags].map(t => `<option value="${t}">${t}</option>`).join('');
    updateRCount();
}

function dlTemplate() {
    dlBlob('name,phone,tags,voucher_code,package_name,amount\nAma Owusu,0241234567,customer,ABCD1234,Short,12.00\n', 'novalink_template.csv');
}

/* ════════════════════════════════
   CSV CONVERTER
   ════════════════════════════════ */
const PRESETS = {
    hotspot: {
        label: 'Hotspot → Voucher Sheet',
        cols: ['voucher_code', 'status', 'package_name', 'amount', 'phone', 'reference', 'date_used', 'sms_status']
    },
    contacts: {
        label: 'Contact Importer',
        cols: ['name', 'phone', 'tags']
    },
    custom: {
        label: 'Custom',
        cols: []
    }
};

function vDrop(e) {
    e.preventDefault();
    e.currentTarget.classList.remove('drag');
    loadConvCSVFile(e.dataTransfer.files[0]);
}

function loadConvCSV(ev) { loadConvCSVFile(ev.target.files[0]); }

function loadConvCSVFile(file) {
    if (!file) return;
    const reader = new FileReader();
    reader.onload = e => {
        conv = parseCSV(e.target.result);
        $('srcCols').innerHTML = conv.headers.map(h => `<span class="chip off">${h}</span>`).join('');
        $('srcRowCount').textContent = conv.rows.length + ' rows detected';
        applyPreset($('presetSel').value);
        toast('CSV loaded: ' + conv.rows.length + ' rows', 'ok');
    };
    reader.readAsText(file);
}

function applyPreset(preset) {
    const p = PRESETS[preset] || PRESETS.custom;
    const el = $('outColsPreview');
    el.innerHTML = p.cols.map(c => `<span class="chip on">${c}</span>`).join('') ||
        '<span style="color:var(--text3);font-size:12px">Define below</span>';
    convMap = {};
    const mr = $('mapRows');
    mr.innerHTML = '';
    if (preset !== 'custom') {
        p.cols.forEach(col => {
            const src = conv.headers.find(h => h.toLowerCase().replace(/[\s_]/g, '') === col.toLowerCase().replace(/[\s_]/g, '')) || '';
            convMap[col] = src;
            addMapRow(col, src, '');
        });
    }
    updateConvPreview();
}

/**
 * addMapRow — adds a mapping row to the converter
 * @param {string} outName   — output column name
 * @param {string} srcName   — source CSV column (empty = none / use fixed value)
 * @param {string} fixedVal  — fixed/static value (used when srcName is empty)
 */
function addMapRow(outName = '', srcName = '', fixedVal = '') {
    const id = 'mr_' + Date.now() + Math.random().toString(36).slice(2);
    const div = document.createElement('div');
    div.className = 'map-row';
    div.id = id;

    const srcOpts = ['', ...conv.headers].map(h =>
        `<option value="${h}" ${h === srcName ? 'selected' : ''}>${h || '— skip —'}</option>`).join('');

    div.innerHTML = `
      <input  class="fi" placeholder="Output column" value="${esc(outName)}"
              oninput="updateConvPreview()" style="font-size:12px;padding:7px 10px"/>
      <div class="map-arrow">←</div>
      <select class="fi" onchange="updateConvPreview()" style="font-size:12px;padding:7px 10px">${srcOpts}</select>
      <input  class="fi" placeholder="Fixed value (optional)" value="${esc(fixedVal)}"
              oninput="updateConvPreview()" style="font-size:12px;padding:7px 10px"
              title="If a source column is selected, it takes priority. Fixed value is used only when source column is empty or '— skip —'."/>
      <button class="btn btn-danger btn-sm" style="padding:6px 9px"
              onclick="document.getElementById('${id}').remove();updateConvPreview()">✕</button>`;

    $('mapRows').appendChild(div);
    updateConvPreview();
}

function getConvMapping() {
    const rows = $('mapRows').querySelectorAll('.map-row');
    const map = {};
    rows.forEach(row => {
        const inputs = row.querySelectorAll('input,select');
        const outCol = inputs[0].value.trim();
        const srcCol = inputs[1].value;          // select
        const fixVal = inputs[2].value.trim();   // fixed value input
        if (outCol) map[outCol] = { src: srcCol, fix: fixVal };
    });
    return map;
}

function updateConvPreview() {
    const map = getConvMapping();
    const outHeaders = Object.keys(map);
    if (!outHeaders.length || !conv.rows.length) {
        $('convPreview').textContent = 'Configure mapping above…';
        $('convRowCount').textContent = '0 rows';
        return;
    }
    convOut = conv.rows.map(row => {
        const out = {};
        outHeaders.forEach(h => {
            const { src, fix } = map[h];
            // source column takes priority; fall back to fixed value
            out[h] = (src && row[src] !== undefined) ? (row[src] || fix || '') : (fix || '');
        });
        return out;
    });
    const preview = [outHeaders.join(','),
    ...convOut.slice(0, 5).map(r => outHeaders.map(h => `"${(r[h] || '').replace(/"/g, '""')}"`).join(','))
    ].join('\n');
    $('convPreview').textContent = preview;
    $('convRowCount').textContent = convOut.length + ' rows';
}

function copyPreview() {
    navigator.clipboard.writeText($('convPreview').textContent).then(() => toast('Copied!', 'ok'));
}

function importAsContacts() {
    if (!convOut.length) { toast('No converted data', 'err'); return; }
    const phoneKey = Object.keys(convOut[0]).find(k => /phone/i.test(k));
    if (!phoneKey) { toast('No phone column in output', 'err'); return; }
    contacts = convOut.filter(r => r[phoneKey]).map(r => ({
        ...r,
        phone: r[phoneKey],
        name: r.name || r.Name || '',
        tags: r.tags || r.Tags || ''
    }));
    buildTagFilters();
    buildRecipientFilter();
    updateStats();
    switchTab('contacts', document.querySelectorAll('.tab')[1]);
    renderContacts();
    toast(`Imported ${contacts.length} contacts`, 'ok');
}

function dlConverted() {
    if (!convOut.length) { toast('Nothing to download', 'err'); return; }
    const headers = Object.keys(convOut[0]);
    const csv = [headers.join(','),
    ...convOut.map(r => headers.map(h => `"${(r[h] || '').replace(/"/g, '""')}"`).join(','))
    ].join('\n');
    dlBlob(csv, 'novalink_converted_' + Date.now() + '.csv');
}

/* ════════════════════════════════
   TEMPLATES
   ════════════════════════════════ */
function renderTplList() {
    const el = $('tplList');
    if (!templates.length) {
        el.innerHTML = '<div style="color:var(--text3);text-align:center;padding:24px">No templates</div>';
        return;
    }
    el.innerHTML = templates.map(t => `
  <div class="tpl-card" id="tc_${t.id}" onclick="toggleTplCard('${t.id}')">
    <div style="display:flex;align-items:flex-start;gap:8px">
      <div style="flex:1;min-width:0">
        <div class="tpl-name">${esc(t.name)}</div>
        <div class="tpl-desc">${esc(t.body.slice(0, 60).replace(/\n/g, ' '))}…</div>
      </div>
      <div class="tpl-badge">
        <span class="badge b-info">${t.category}</span>
        <button onclick="event.stopPropagation();editTpl('${t.id}')" class="btn btn-outline btn-sm" style="font-size:11px">Edit</button>
        ${!BUILTIN_TEMPLATES.find(b => b.id === t.id)
            ? `<button onclick="event.stopPropagation();deleteTpl('${t.id}')" class="btn btn-danger btn-sm" style="font-size:11px">Del</button>` : ''}
      </div>
    </div>
    <div class="tpl-preview">${t.body.replace(/\{\{(\w+)\}\}/g, '<span class="var-tag">{{$1}}</span>').replace(/\n/g, '<br>')}</div>
  </div>`).join('');

    const opts = '<option value="">— choose template —</option>' +
        templates.map(t => `<option value="${t.id}">${t.name}</option>`).join('');
    $('quickTplSel').innerHTML = opts;
    $('broadTplSel').innerHTML = opts;
}

function toggleTplCard(id) {
    const el = $('tc_' + id);
    const wasSelected = el.classList.contains('selected');
    document.querySelectorAll('.tpl-card').forEach(c => c.classList.remove('selected'));
    if (!wasSelected) el.classList.add('selected');
}

function saveTpl() {
    const name = $('tplName').value.trim();
    const body = $('tplBody').value.trim();
    const cat = $('tplCat').value;
    if (!name) { toast('Enter a template name', 'err'); return; }
    if (!body) { toast('Enter a message body', 'err'); return; }
    if (editingTplId) {
        const t = templates.find(x => x.id === editingTplId);
        if (t) { t.name = name; t.body = body; t.category = cat; }
    } else {
        templates.push({ id: 'tpl_' + Date.now(), name, category: cat, body });
    }
    resetTplForm();
    renderTplList();
    toast('Template saved', 'ok');
}

function editTpl(id) {
    const t = templates.find(x => x.id === id);
    if (!t) return;
    editingTplId = id;
    $('tplName').value = t.name;
    $('tplBody').value = t.body;
    $('tplCat').value = t.category;
    countTpl();
    switchTab('templates', document.querySelectorAll('.tab')[3]);
    $('tplName').focus();
    toast('Editing: ' + t.name, 'warn');
}

function deleteTpl(id) {
    if (!confirm('Delete this template?')) return;
    templates = templates.filter(t => t.id !== id);
    renderTplList();
    toast('Template deleted', 'info');
}

function resetTplForm() {
    editingTplId = null;
    $('tplName').value = '';
    $('tplBody').value = '';
    $('tplCC').textContent = '0';
    $('tplPreviewOut').style.display = 'none';
}

function previewTpl() {
    const body = $('tplBody').value.trim();
    if (!body) { toast('Write a template first', 'err'); return; }
    const sample = { name: 'Ama Owusu', voucher: 'nnby2835', package: 'Short', amount: '12.00', phone: '+233241234567' };
    $('tplPreviewBox').textContent = resolveVars(body, sample);
    $('tplPreviewOut').style.display = 'block';
}

function applyTplToQuick() {
    const id = $('quickTplSel').value;
    if (!id) return;
    const t = templates.find(x => x.id === id);
    if (!t) return;
    $('quickMsg').value = t.body;
    countQ();
    toast('Template applied', 'info');
}

function applyTplToBroad() {
    const id = $('broadTplSel').value;
    if (!id) return;
    const t = templates.find(x => x.id === id);
    if (!t) return;
    $('msgBody').value = t.body;
    countB();
    toast('Template applied', 'info');
}

/* ════════════════════════════════
   INIT
   ════════════════════════════════ */
document.addEventListener('DOMContentLoaded', () => {
    renderTplList();
    updateStats();
    updateRCount();
    // Auto-fetch balance on page load (silent — no error toast on first visit)
    checkBalance(true);
});
