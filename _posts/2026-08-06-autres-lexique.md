---
date: 2026-08-06
title: Lexique agricole FR-DE
categories:
  - Autres
description: lexique et traduction de termes agricoles
type: Document
---


<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<link href="https://unpkg.com/tabulator-tables@5.5.2/dist/css/tabulator.min.css" rel="stylesheet">
<script type="text/javascript" src="https://unpkg.com/tabulator-tables@5.5.2/dist/js/tabulator.min.js"></script>

<style>
    .main-content, .container, article {
        max-width: 100% !important;
        padding-left: 20px !important;
        padding-right: 20px !important;
    }
    /* Permet un défilement horizontal propre si le tableau est large */
    .table-container {
        width: 100%;
        overflow-x: auto;
    }
</style>

<div style="margin-bottom: 15px; display: flex; flex-wrap: wrap; gap: 10px; align-items: center;">
    <button id="btn-show-all" style="padding: 8px 14px; cursor: pointer; font-weight: bold;">Afficher tout</button>
    <button id="btn-show-default" style="padding: 8px 14px; cursor: pointer; font-weight: bold;">Vue simple</button>
    
    <select id="column-select" style="padding: 8px; cursor: pointer;">
        <option value="">-- Choisir une colonne à afficher/masquer --</option>
    </select>
</div>

<div class="table-container">
    <div id="excel-table" style="margin-top: 10px;"></div>
</div>

<script>
fetch('/documents-utiles/fichiers/agroterm_lexique_complet_22-07-2026.xlsx')
    .then(response => response.arrayBuffer())
    .then(data => {
        const workbook = XLSX.read(data, {type: 'array'});
        const worksheet = workbook.Sheets[workbook.SheetNames[0]];
        
        // Force la lecture complète en brut pour récupérer TOUTES les colonnes de l'en-tête (ligne 1)
        const jsonData = XLSX.utils.sheet_to_json(worksheet, {header: 1});
        const headers = jsonData[0]; // La première ligne contient les vrais noms de colonnes
        
        // Convertit les lignes suivantes en objets exploitables
        const rows = XLSX.utils.sheet_to_json(worksheet);

        const defaultVisible = ["fr_terme", "de_terme", "fr_Abréviation", "fr_Définition"];

        // Construit les colonnes à partir de la ligne d'en-tête exacte
        const columns = headers.map(key => ({
            title: key,
            field: key,
            headerFilter: "input",
            headerFilterPlaceholder: "Filtrer...",
            visible: defaultVisible.includes(key)
        }));

        var table = new Tabulator("#excel-table", {
            data: rows,
            layout: "fitData", // S'adapte à la taille des données pour forcer le scroll horizontal si besoin
            responsiveLayout: false,
            pagination: "local",
            paginationSize: 25,
            columns: columns,
        });

        // Remplit le menu déroulant avec toutes les colonnes détectées
        const selectDropdown = document.getElementById("column-select");
        headers.forEach(key => {
            let option = document.createElement("option");
            option.value = key;
            option.textContent = key;
            selectDropdown.appendChild(option);
        });

        selectDropdown.addEventListener("change", function() {
            let colName = this.value;
            if (!colName) return;
            let col = table.getColumn(colName);
            if (col) {
                if (col.isVisible()) {
                    col.hide();
                } else {
                    col.show();
                }
            }
            this.value = "";
        });

        document.getElementById("btn-show-all").addEventListener("click", function() {
            table.getColumns().forEach(col => col.show());
        });

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
