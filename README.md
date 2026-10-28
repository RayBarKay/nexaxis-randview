# NexAxis Randview Data Centre — static website

Self-contained static site: one `index.html`, one CSS file, one JS file, five images,
plus the downloadable Reseller Agreement (`.docx`). No build step, no external libraries.
Cyan theme, scroll transitions, lightbox gallery, Google Maps embed.

## Structure

```
nexaxis-website/
├── index.html
├── assets/
│   ├── css/site.css
│   ├── js/site.js
│   ├── downloads/
│   │   └── NexAxis_Reseller_Agreement_2026-05-12.docx   (Resellers section)
│   └── images/
│       ├── facade.jpg   (hero background + gallery)
│       ├── hero.jpg
│       ├── hall.jpg
│       ├── notice.jpg
│       └── logo.jpeg    (nav/footer/favicon)
└── README.md
```

Sections: Overview · Services · Infrastructure · **Resellers &amp; Partners** (program +
agreement download; pricing kept out of the public page, per owner request) · Gallery · Location.

Quick local preview on any machine:

```bash
# Python (any OS)
python3 -m http.server 8080 -d nexaxis-website
# then open http://localhost:8080
```


- Facility copy, services list, infrastructure features and photos:
  [DataCenterMap — NexAxis Randview Datacentre](https://www.datacentermap.com/south-africa/johannesburg/nexaxis-randview-datacentre/)
- Photos were pulled to `assets/images/` at build time (the site has no
  hotlinks, so it works even if the source changes).
- Map embed + Street View buttons link to Google Maps for the address
  `3 Randview Ave, Randview Business Park, Ferndale Ext. 9, Randburg 2194`.

Built by Bond, 2026-10-23.
