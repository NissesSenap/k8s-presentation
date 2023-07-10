---
marp: true
theme: gaia
paginate: true
style: @import url('https://unpkg.com/tailwindcss@^2/dist/utilities.min.css');
---
# 1st page

The content of 1st page

---

## 2nd page

### The content of 2nd page

Hello, world!

---
<!--
These are speaker notes.
-->

# Multi columns in Marp slide

<div class="grid grid-cols-2 gap-4">
<div>

## Column 1

- test
- test
- test
- test
- test

</div>
<div>

<font size="6">

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: default
  labels:
    app: nginx
spec:
  containers:
  - image: nginx:1.13.9
    name: nginx
    ports:
    - name: http
      containerPort: 80
```

</font>

</div>
</div>

---

# I don't give

A test
