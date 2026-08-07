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

<!-- CSS pour forcer le contenu à s'élargir et réduire les marges de la page -->
<style>
    /* Permet d'élargir la zone de contenu de ton site si le thème bloque */
    .main-content, .container, article {
        max-width: 100% !important;
        padding-left: 20px !important;
        padding-right: 20px !important;
    }
</style>

<!-- Barre d'outils : Boutons + Menu déroulant des colonnes -->
<div style="margin-bottom: 15px; display: flex; flex-wrap: wrap; gap: 10px; align-items: center;">
    <button id="btn-show-all" style="padding: 8px 14px; cursor: pointer; font-weight: bold;">Afficher tout</button>
    <button id="btn-show-default" style="padding: 8px 14px; cursor: pointer; font-weight: bold;">Vue simple</button>
    
    <!-- Menu déroulant pour choisir les colonnes individuellement -->
    <select id="column-select" style="padding: 8px; cursor: pointer;">
        <option value="">-- Choisir une colonne à afficher/masquer --</option>
    </select>
</div>

<!-- Conteneur du tableau élargi -->
<div id="excel-table" style="width: 100%; margin-top: 10px;"></div>

<script>
fetch('/documents-utiles/fichiers/agroterm_lexique_complet_22-07-2026.xlsx')
    .then(response => response.arrayBuffer())
    .then(data => {
        const workbook = XLSX.read(data, {type: 'array'});
        const worksheet = workbook.Sheets[workbook.SheetNames[0]];
        const jsonData = XLSX.utils.sheet_to_json(worksheet);

        const defaultVisible = ["fr_terme", "de_terme", "fr_Abréviation", "fr_Définition"];
        const columnKeys = Object.keys(jsonData[0] || {});

        const columns = columnKeys.map(key => ({
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
            paginationSize: 25,
            columns: columns,
        });

        // Remplir le menu déroulant avec toutes les colonnes
        const selectDropdown = document.getElementById("column-select");
        columnKeys.forEach(key => {
            let option = document.createElement("option");
            option.value = key;
            option.textContent = key;
            selectDropdown.appendChild(option);
        });

        // Gérer le choix dans le menu déroulant (bascule l'état de la colonne choisie)
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
            this.value = ""; // Réinitialiser le select après le choix
        });

        // Bouton "Afficher tout"
        document.getElementById("btn-show-all").addEventListener("click", function() {
            table.getColumns().forEach(col => col.show());
        });

        // Bouton "Vue simple"
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
