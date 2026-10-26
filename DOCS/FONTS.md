<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

# Fonts

The UI uses **Montserrat** as the OgiGrid CFMS brand typeface. It is bundled locally from the
exactly pinned `@fontsource-variable/montserrat` package and served from the application origin.
No font is fetched from Google or another third party during the build or at runtime.

`src/styles.scss` declares the variable font for normal and italic text at weights 100–900. The
application currently ships only the Latin and Latin Extended subsets. Those subsets cover the
English UI and accented Latin text while avoiding unused Cyrillic and Vietnamese assets. Hindi
and Korean text falls back to the platform UI font because Montserrat does not provide Devanagari
or Hangul glyphs.

The global stack is:

```scss
font-family:
  'Montserrat',
  system-ui,
  -apple-system,
  BlinkMacSystemFont,
  'Segoe UI',
  Roboto,
  'Helvetica Neue',
  Arial,
  sans-serif;
```

Two components intentionally use `'Roboto Mono', monospace` for numeric and diagnostic readouts.
Roboto Mono is not bundled, so those fields use the platform monospace font when it is not
installed.

## Why the font is self-hosted

The production CSP in `deploy/nginx.conf.template` permits fonts only from `'self'` and `data:`.
An external Google Fonts stylesheet would therefore be blocked at runtime. Angular's font
inlining can also turn an external stylesheet into a build-time network dependency, preventing a
reproducible offline build.

Bundling the font through npm provides a lockfile integrity hash, works under the existing CSP,
and keeps builds independent of third-party font servers. Do not add a Google Fonts `<link>`,
`@import`, or external `url()`.

## Licence and distribution

Montserrat is Copyright 2011 The Montserrat Project Authors and licensed under the SIL Open Font
License 1.1. ASF policy classifies OFL as Category B: the binary font may be included in the built
application but must not be committed as source-release content.

For that reason:

- the WOFF2 files remain under `node_modules/` and enter only the generated build artifact;
- `README.md` prominently identifies the bundled font and its licence;
- `angular.json` copies the package's verbatim `LICENSE` into the built application at
  `assets/licenses/montserrat/LICENSE`; and
- `audit/third-party-assets.csv` records the distributed files and attribution requirement.

Do not commit Montserrat `.woff2` files to this repository.

## Guard

`scripts/ga-check.mjs` scans source and built output for external Google Fonts URLs. Run:

```bash
npm run ga:check
```

The production build should also contain four hashed Montserrat WOFF2 assets and the licence file:

```bash
npm run build
find dist/fineract-backoffice-ui/browser -iname '*montserrat*' -o -path '*/licenses/montserrat/*'
```

The original external-font investigation and ASF licensing analysis are retained in
`audit/GOOGLE-FONTS-ASF-AUDIT.md`.
