---
date: 2026-08-06
title: Lexique
categories:
  - Autres
description: lexique et traduction de termes agricoles
type: Document
---

<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<link href="https://unpkg.com/tabulator-tables@5.5.2/dist/css/tabulator.min.css" rel="stylesheet">
<script type="text/javascript" src="https://unpkg.com/tabulator-tables@5.5.2/dist/js/tabulator.min.js"></script>

<!-- Boutons de contrôle pour afficher/masquer les colonnes -->
<div style="margin-bottom: 10px;">
    <button id="btn-show-all" style="padding: 6px 12px; cursor: pointer; margin-right: 10px;">Afficher toutes les colonnes</button>
    <button id="btn-show-default" style="padding: 6px 12px; cursor: pointer;">Vue simple (par défaut)</button>
</div>

<!-- Largeur à 100% -->
<div id="excel-table" style="width: 100%;"></div>

<script>
fetch('/documents-utiles/fichiers/agroterm_lexique_complet_22-07-2026.xlsx')
    .then(response => response.arrayBuffer())
    .then(data => {
        const workbook = XLSX.read(data, {type: 'array'});
        const worksheet = workbook.Sheets[workbook.SheetNames[0]];
        const jsonData = XLSX.utils.sheet_to_json(worksheet);

        // Liste des colonnes par défaut (ajuste l'orthographe si besoin selon ton Excel)
        const defaultVisible = ["fr_terme", "de_terme", "fr_Abréviation", "fr_Définition"];

        const columns = Object.keys(jsonData[0] || {}).map(key => ({
            title: key,
            field: key,
            headerFilter: "input",
            headerFilterPlaceholder: "Filtrer...",
            visible: defaultVisible.includes(key)
        }));

        var table = new Tabulator("#excel-table", {
            data: jsonData,
            layout: "fitColumns",
            pagination: "local",
            paginationSize: 25, // Augmenté à 25 lignes par page pour plus de hauteur
            columns: columns,
        });

        // Action du bouton "Afficher tout"
        document.getElementById("btn-show-all").addEventListener("click", function() {
            table.showColumn(true); // 'true' permet d'afficher toutes les colonnes d'un coup
        });

        // Action du bouton "Vue simple"
        document.getElementById("btn-show-default").addEventListener("click", function() {
            table.hideColumn(true); // Masque tout d'abord, puis réaffiche uniquement les 4 principales
            defaultVisible.forEach(col => table.showColumn(col));
        });
    })
    .catch(error => console.error('Erreur lors du chargement du fichier Excel :', error));
</script>



Source : [AgroTerm](https://www.agroterm.ch/), état au 25.07.2026
