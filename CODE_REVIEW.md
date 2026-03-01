# Committee Manager code check

I reviewed the shared HTML/CSS/JS and found a few important issues.

## ✅ What looks good
- Clean visual design system with reusable CSS variables.
- Solid UX touches (loading state, toasts, empty states, table/card switch, print views).
- CRUD flow structure is clear and mostly consistent.

## ⚠️ Issues to fix

### 1) Missing `.page-header` wrapper in Members page (layout bug)
In the members section, `.page-title` and `.header-actions` are not wrapped inside a `div.page-header`, so the intended flex layout will not apply.

**Fix**
```html
<div id="page-members">
  <div class="page-header">
    <div class="page-title">...</div>
    <div class="header-actions">...</div>
  </div>
  ...
</div>
```

### 2) CSS class mismatch in ID card footer
CSS expects `.id-card-bot .id-num`, but HTML renders `class="id-card-id-num"`. The intended styling won't apply.

**Fix one of these:**
- Change HTML class to `id-num`, or
- Change CSS selector to `.id-card-bot .id-card-id-num`.

### 3) Auto ID generation can duplicate IDs
`autoId()` uses `allMembers.length + 1`. If records are deleted or loaded out of sequence, duplicate IDs can be generated.

**Better approach**
- Query max numeric ID and increment safely, or
- Use UUIDs / database-generated IDs.

### 4) XSS risk with `innerHTML`
Many user fields (name, notes, relation, etc.) are interpolated directly into `innerHTML`. A malicious value could inject script/HTML.

**Fix**
- Escape user-provided strings before interpolation, or
- Build DOM nodes using `textContent` for dynamic text.

### 5) Public key exposure note
You are using a **Supabase publishable key**, which is expected client-side, but make sure Row Level Security policies are strict and write operations are protected.

## Suggested quick patch order
1. Add `.page-header` wrapper in members page.
2. Fix the ID card footer class mismatch.
3. Replace `autoId()` strategy.
4. Add a small `escapeHtml()` helper and apply to all rendered fields.
5. Verify Supabase RLS + policies.
