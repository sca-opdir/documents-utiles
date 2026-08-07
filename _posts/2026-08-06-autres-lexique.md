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

<!-- Boutons de contrôle -->
<div style="margin-bottom: 15px;">
    <button id="btn-show-all" style="padding: 8px 14px; cursor: pointer; margin-right: 10px; font-weight: bold;">Afficher toutes les colonnes</button>
    <button id="btn-show-default" style="padding: 8px 14px; cursor: pointer; font-weight: bold;">Vue simple (par défaut)</button>
</div>

<!-- Conteneur avec une largeur forcée à 100% pour occuper tout l'espace -->
<div id="excel-table" style="width: 100%; max-width: 100%; margin-top: 10px;"></div>

<script>
fetch('/documents-utiles/fichiers/agroterm_lexique_complet_22-07-2026.xlsx')
    .then(response => response.arrayBuffer())
    .then(data => {
        const workbook = XLSX.read(data, {type: 'array'});
        const worksheet = workbook.Sheets[workbook.SheetNames[0]];
        const jsonData = XLSX.utils.sheet_to_json(worksheet);

        // Liste exacte des colonnes par défaut basées sur tes données
        const defaultVisible = ["fr_terme", "de_terme", "fr_Abréviation", "fr_Définition"];

        const columns = Object.keys(jsonData[0] || {}).map(key => ({
            title: key,
            field: key,
            headerFilter: "input",
            headerFilterPlaceholder: "Filtrer...",
            // On affiche par défaut uniquement si la colonne est dans la liste
            visible: defaultVisible.includes(key)
        }));

        var table = new Tabulator("#excel-table", {
            data: jsonData,
            layout: "fitColumns", // Répartit l'espace disponible sur les colonnes visibles
            pagination: "local",
            paginationSize: 25,
            columns: columns,
        });

        // Correction du bouton "Afficher tout"
        document.getElementById("btn-show-all").addEventListener("click", function() {
            table.getColumns().forEach(col => col.show());
        });

        // Correction du bouton "Vue simple"
        document.getElementById("btn-show-default").addEventListener("click", function() {
            table.getColumns().forEach(col => {
                const fieldName = col.getField();
                if (defaultVisible.includes(fieldName)) {
                    col.show();
                } else {
                    col.hide();
                }
            });
        });
    })
    .catch(error => console.error('Erreur lors du chargement du fichier Excel :', error));
</script>
Source : [AgroTerm](https://www.agroterm.ch/), état au 25.07.2026
