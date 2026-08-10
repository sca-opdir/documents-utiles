---
date: 2026-08-07
title: Ordres de grandeur
categories:
  - Paiements-directs
description: Ordres de grandeur surfaces et animaux
type: Document
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<link rel="stylesheet" href="https://unpkg.com/tabulator-tables@5.5.2/dist/css/tabulator.min.css">
<script src="https://unpkg.com/tabulator-tables@5.5.2/dist/js/tabulator.min.js"></script>

<style>
.tab-buttons {
  display: flex;
  gap: 5px;
  margin-bottom: 15px;
  border-bottom: 2px solid #ccc;
}

.tab-btn {
  padding: 10px 20px;
  border: 0;
  background: #f1f1f1;
  cursor: pointer;
  font-weight: bold;
}

.tab-btn.active {
  background: #007bff;
  color: white;
}

.tab-content {
  display: none;
}

.tab-content.active {
  display: block;
}
</style>


*chiffres 2025 approximatifs non officiels*


#### SURFACES

<div class="tab-buttons">
  <button class="tab-btn active" data-tab="cat1">Niveau 1</button>
  <button class="tab-btn" data-tab="cat2">Niveau 2</button>
  <button class="tab-btn" data-tab="cat3">Code culture</button>
</div>

<div id="cat1" class="tab-content active">
  <div id="table-cat1"></div>
</div>

<div id="cat2" class="tab-content">
  <div id="table-cat2"></div>
</div>

<div id="cat3" class="tab-content">
  <div id="table-cat3"></div>
</div>

<hr>

#### ANIMAUX

<div id="table-animaux"></div>

<script>
async function loadExcel(file, sheet, element) {
  const response = await fetch(file);
  const buffer = await response.arrayBuffer();

  const workbook = XLSX.read(buffer);
  const worksheet = workbook.Sheets[sheet];

  const rows = XLSX.utils.sheet_to_json(worksheet, {
    header: 1,
    defval: ""
  });

  const headers = rows.shift();

  const data = rows.map(row =>
    Object.fromEntries(
      headers.map((header, i) => ["col" + i, row[i] ?? ""])
    )
  );

  const columns = headers.map((header, i) => ({
    title: header,
    field: "col" + i,
    headerFilter: "input"
  }));

  new Tabulator(element, {
    data,
    columns,
    layout: "fitColumns",
    pagination: true,
    paginationSize: 15
  });
}

const surfaces = "/documents-utiles/fichiers/stats_surfaces_pdir25_2026-08-05.xlsx";

loadExcel(surfaces, "cat1", "#table-cat1");
loadExcel(surfaces, "cat2", "#table-cat2");
loadExcel(surfaces, "cat3", "#table-cat3");

loadExcel(
  "/documents-utiles/fichiers/stats_animaux_pdir25_2026-08-07.xlsx",
  "animaux",
  "#table-animaux"
);

document.querySelectorAll(".tab-btn").forEach(button => {
  button.addEventListener("click", () => {
    document.querySelectorAll(".tab-btn").forEach(b =>
      b.classList.remove("active")
    );

    document.querySelectorAll(".tab-content").forEach(tab =>
      tab.classList.remove("active")
    );

    button.classList.add("active");
    document.getElementById(button.dataset.tab).classList.add("active");

    window.dispatchEvent(new Event("resize"));
  });
});
</script>
