---
date: 2026-08-07
title: Ordres de grandeur
categories:
  - Autres
description: Ordres de grandeur surfaces et animaux
type: Document
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<link href="https://unpkg.com/tabulator-tables@5.5.2/dist/css/tabulator.min.css" rel="stylesheet">
<script type="text/javascript" src="https://unpkg.com/tabulator-tables@5.5.2/dist/js/tabulator.min.js"></script>

<div id="excel-table" style="margin-top: 20px;"></div>

<script>
fetch('/documents-utiles/fichiers/stats_animaux_pdir25_2026-08-07.xlsx')
    .then(response => response.arrayBuffer())
    .then(data => {
        const workbook = XLSX.read(data, {type: 'array'});
        const firstSheetName = workbook.SheetNames[0];
        const worksheet = workbook.Sheets[firstSheetName];
        const jsonData = XLSX.utils.sheet_to_json(worksheet);

        const columns = Object.keys(jsonData[0] || {}).map(key => ({
            title: key,
            field: key,
            headerFilter: "input",
            headerFilterPlaceholder: "Filtrer..."
        }));

        var table = new Tabulator("#excel-table", {
            data: jsonData,
            layout: "fitColumns",
            responsiveLayout: "collapse",
            pagination: "local",
            paginationSize: 15,
            columns: columns,
        });
    })
    .catch(error => console.error('Erreur lors du chargement du fichier Excel :', error));
</script>




