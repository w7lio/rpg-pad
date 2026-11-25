<div align="center">

# 🎮 rpgpad

**Nút điều khiển cảm ứng cho game RPG Maker bản web.**
Dán một đoạn code, chơi được ngay trên điện thoại.

`RPG Maker MV` · `Không cần plugin` · `Không cần file thêm`

</div>

---

## ✨ Tính năng

- **4 nút hướng** ↑ ↓ ← → dạng nút bấm vuông, nhận chạm ngay lập tức
- **A** (OK) và **B** (Hủy / Menu)
- **DASH** chạy nhanh, bấm để bật, bấm lại để tắt
- **⌨ TẮT/MỞ** ở góc trên trái để ẩn hoặc hiện toàn bộ nút
- Tự ẩn trên máy tính, chỉ hiện trên thiết bị cảm ứng
- Chạm vào nút không làm nhân vật tự đi theo điểm chạm

---

## 🚀 Cài đặt (3 bước)

**1.** Mở `index.html` của game bằng Notepad++ hoặc VS Code. Nên sao lưu file trước.

**2.** Trong `<head>`, đảm bảo có thẻ viewport này (thay dòng cũ nếu đã có):

```html
<meta name="viewport" content="width=device-width, initial-scale=1, user-scalable=no, viewport-fit=cover">
```

**3.** Dán đoạn code dưới đây **ngay trên `</body>`**, sau dòng `main.js`:

```html
<!-- ===== RPGPAD - bắt đầu ===== -->
<style>
    #mc-root{--mc-s:2; /* 1 = cỡ cũ, 2 = to gấp đôi. Chỉnh số này để to/nhỏ tùy ý */
        position:fixed;left:0;right:0;top:0;bottom:0;z-index:99999;pointer-events:none;
        -webkit-user-select:none;user-select:none;-webkit-touch-callout:none;touch-action:none;
        padding:env(safe-area-inset-top) env(safe-area-inset-right) env(safe-area-inset-bottom) env(safe-area-inset-left);}
    #mc-root *{box-sizing:border-box;-webkit-tap-highlight-color:transparent;}

    .mc-btn,.mc-small{pointer-events:auto;touch-action:none;opacity:.55;border-radius:0;
        background:rgba(255,255,255,.2);border:2px solid rgba(255,255,255,.6);color:#fff;
        font:bold calc(20px * var(--mc-s))/1 sans-serif;display:flex;align-items:center;justify-content:center;text-shadow:0 0 4px #000;}
    .mc-btn.on,.mc-small.on{opacity:1;background:rgba(255,255,255,.45);}

    .mc-dpad{position:absolute;left:3vmin;bottom:3vmin;width:calc(40vmin * var(--mc-s));height:calc(40vmin * var(--mc-s));
        max-width:calc(210px * var(--mc-s));max-height:calc(210px * var(--mc-s));
        display:grid;grid-template-columns:repeat(3,1fr);grid-template-rows:repeat(3,1fr);pointer-events:none;}
    .mc-dpad .mc-btn{position:static;width:100%;height:100%;}
    .mc-dpad .mc-btn::before{content:"";width:0;height:0;border:solid transparent;}
    #mc-up{grid-column:2;grid-row:1;}   #mc-up::before{border-width:0 calc(9px * var(--mc-s)) calc(15px * var(--mc-s)) calc(9px * var(--mc-s));border-bottom-color:#fff;}
    #mc-left{grid-column:1;grid-row:2;} #mc-left::before{border-width:calc(9px * var(--mc-s)) calc(15px * var(--mc-s)) calc(9px * var(--mc-s)) 0;border-right-color:#fff;}
    #mc-right{grid-column:3;grid-row:2;}#mc-right::before{border-width:calc(9px * var(--mc-s)) 0 calc(9px * var(--mc-s)) calc(15px * var(--mc-s));border-left-color:#fff;}
    #mc-down{grid-column:2;grid-row:3;} #mc-down::before{border-width:calc(15px * var(--mc-s)) calc(9px * var(--mc-s)) 0 calc(9px * var(--mc-s));border-top-color:#fff;}

    #mc-a,#mc-b,#mc-dash{position:absolute;width:calc(17vmin * var(--mc-s));height:calc(17vmin * var(--mc-s));
        max-width:calc(92px * var(--mc-s));max-height:calc(92px * var(--mc-s));}
    #mc-a{right:3vmin;bottom:calc(3vmin + 15.3vmin * var(--mc-s));}
    #mc-b{right:calc(3vmin + 18.7vmin * var(--mc-s));bottom:3vmin;}
    #mc-dash{right:calc(3vmin + 18.7vmin * var(--mc-s));bottom:calc(5vmin + 17vmin * var(--mc-s));
        width:calc(13vmin * var(--mc-s));height:calc(13vmin * var(--mc-s));max-width:calc(70px * var(--mc-s));max-height:calc(70px * var(--mc-s));
        font-size:calc(13px * var(--mc-s));}

    .mc-small{position:absolute;top:2vmin;left:2vmin;min-width:66px;height:46px;padding:0 14px;
        background:rgba(0,0,0,.45);font:bold 16px/44px sans-serif;opacity:.8;}
    #mc-root.hidden .mc-dpad,#mc-root.hidden #mc-a,#mc-root.hidden #mc-b,#mc-root.hidden #mc-dash{display:none;}
</style>

<div id="mc-root">
    <div class="mc-dpad">
        <div class="mc-btn" id="mc-up"></div>
        <div class="mc-btn" id="mc-left"></div>
        <div class="mc-btn" id="mc-right"></div>
        <div class="mc-btn" id="mc-down"></div>
    </div>
    <div class="mc-btn" id="mc-a">A</div>
    <div class="mc-btn" id="mc-b">B</div>
    <div class="mc-btn" id="mc-dash">DASH</div>
    <div class="mc-small" id="mc-hide">⌨ TẮT</div>
</div>

<script>
(function () {
    'use strict';
    var root = document.getElementById('mc-root');
    var isTouch = ('ontouchstart' in window) || (navigator.maxTouchPoints > 0);
    if (!isTouch) { root.style.display = 'none'; return; }

    var KEY = { left: 37, up: 38, right: 39, down: 40, ok: 13, cancel: 27, shift: 16 };
    var held = {};

    function press(code) {
        if (held[code] || !window.Input) return;
        held[code] = true;
        Input._onKeyDown({ keyCode: code, preventDefault: function () {} });
    }
    function release(code) {
        if (!held[code] || !window.Input) return;
        held[code] = false;
        Input._onKeyUp({ keyCode: code });
    }

    ['touchstart', 'touchmove', 'touchend', 'touchcancel', 'mousedown', 'mouseup'].forEach(function (ev) {
        root.addEventListener(ev, function (e) {
            if (e.target !== root) { e.stopPropagation(); if (e.cancelable) e.preventDefault(); }
        }, { passive: false });
    });
    root.addEventListener('contextmenu', function (e) { e.preventDefault(); });

    function bindHold(el, code) {
        var ids = {};
        el.addEventListener('pointerdown', function (e) {
            ids[e.pointerId] = true;
            try { el.setPointerCapture(e.pointerId); } catch (_) {}
            el.classList.add('on');
            press(code);
        });
        function end(e) {
            delete ids[e.pointerId];
            if (Object.keys(ids).length === 0) { el.classList.remove('on'); release(code); }
        }
        el.addEventListener('pointerup', end);
        el.addEventListener('pointercancel', end);
        el.addEventListener('lostpointercapture', end);
    }
    bindHold(document.getElementById('mc-up'), KEY.up);
    bindHold(document.getElementById('mc-down'), KEY.down);
    bindHold(document.getElementById('mc-left'), KEY.left);
    bindHold(document.getElementById('mc-right'), KEY.right);
    bindHold(document.getElementById('mc-a'), KEY.ok);
    bindHold(document.getElementById('mc-b'), KEY.cancel);

    var dash = document.getElementById('mc-dash');
    var dashOn = false;
    dash.addEventListener('pointerdown', function () {
        dashOn = !dashOn;
        dash.classList.toggle('on', dashOn);
        dashOn ? press(KEY.shift) : release(KEY.shift);
    });

    var hideBtn = document.getElementById('mc-hide');
    hideBtn.addEventListener('pointerdown', function () {
        var h = root.classList.toggle('hidden');
        hideBtn.textContent = h ? '⌨ MỞ' : '⌨ TẮT';
        if (h) { Object.keys(KEY).forEach(function (k) { release(KEY[k]); }); dashOn = false; dash.classList.remove('on'); }
    });

    document.addEventListener('visibilitychange', function () {
        if (document.hidden) Object.keys(KEY).forEach(function (k) { release(KEY[k]); });
    });
})();
</script>
<!-- ===== RPGPAD - kết thúc ===== -->
```

Lưu file, upload lại game, xóa cache rồi mở trên điện thoại. Xong.

---

## 🕹️ Các nút

| Nút | Tác dụng |
|:---:|---|
| ↑ ↓ ← → | Di chuyển, chọn mục trong menu |
| **A** | Xác nhận, nói chuyện, tương tác |
| **B** | Hủy, hoặc mở menu khi ở bản đồ |
| **DASH** | Bật/tắt chạy nhanh (chỉ ở bản đồ) |
| **⌨** | Ẩn/hiện toàn bộ nút |

---

## 🎨 Tùy chỉnh nhanh

| Muốn đổi | Sửa trong code |
|---|---|
| Độ mờ nút | `opacity:.55` |
| Kích thước tất cả nút | `--mc-s:2` (1 = cỡ nhỏ, 2 = gấp đôi, 1.5 = vừa) |
| Bo góc | `border-radius:0` → `border-radius:6px` |
| Vị trí | `left` / `right` / `bottom` của `.mc-dpad`, `#mc-a`, `#mc-b`, `#mc-dash` |

---

## ❓ Gặp lỗi?

- **Không thấy nút:** nút chỉ hiện trên thiết bị cảm ứng. Thử trên máy tính bằng cách bấm F12 và bật chế độ giả lập điện thoại. Kiểm tra code đã dán trên `</body>` chưa, và xóa cache.
- **Bấm nhưng nhân vật không chạy:** game có thể dùng plugin đổi phím. Mở Console, gõ `Input.keyMapper` và sửa số trong dòng `var KEY = {...}` cho khớp.
- **Bị trùng nút:** game đã có plugin nút mobile riêng, hãy tắt plugin đó.

---

<div align="center">

Viết cho **RPG Maker MV**. Bản MZ dùng cùng hệ thống `Input` nên nhiều khả năng chạy được, nhưng chưa kiểm tra.
Muốn gỡ: xóa phần nằm giữa hai dòng chú thích `RPGPAD`.

</div>
