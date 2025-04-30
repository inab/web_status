# Web Status Page

This repository contains a static HTML page to be used as a generic **"Service Unavailable"** placeholder for INB/ELIXIR-ES services that are temporarily down or discontinued.

It is a self-contained static page, requiring no backend, and is designed to:

- Display a clear service unavailable message.
- Show the current domain name dynamically.
- Attempt to show a logo specific to the domain.

---

## 💡 How it works

- The hostname is detected via `window.location.hostname`.
- The page tries to load a logo from: logos/hosts/<subdomain>.png

For example:
- `inb-elixir.es` → tries `logos/hosts/elixir.es.png`, etc.
- It uses a `HEAD` request to check whether the logo exists.
- If the logo exists, it is displayed to the right of the status message.
- If not, it is silently hidden.

---

## 🗂 Directory structure
<pre>
.
├── index.html
├── image_service.jpg
└── logos/
    ├── bsc.png
    ├── elixir.png
    └── hosts/
        ├── inb-elixir.es.png
        ├── openebench.bsc.es.png
        └── ...
</pre>

## 🙌 Acknowledgments

- Barcelona Supercomputing Center (BSC)

- INB / ELIXIR-ES

- And to all the servers that occasionally need a nap 😴