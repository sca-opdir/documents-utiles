---
date: 2026-08-06
title: Lexique
categories:
  - Autres
description: lexique et traduction de termes agricoles
type: Document
---

<!-- TON CODE HTML / JS COMMENCE ICI -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<link href="https://unpkg.com/tabulator-tables@5.5.2/dist/css/tabulator.min.css" rel="stylesheet">
<script type="text/javascript" src="https://unpkg.com/tabulator-tables@5.5.2/dist/js/tabulator.min.js"></script>

<!-- Largeur forcée à 100% -->
<div id="excel-table" style="margin-top: 20px; width: 100%;"></div>

<script>
fetch('/documents-utiles/fichiers/agroterm_lexique_complet_22-07-2026.xlsx')
    .then(response => response.arrayBuffer())
    .then(data => {
        const workbook = XLSX.read(data, {type: 'array'});
        const worksheet = workbook.Sheets[workbook.SheetNames[0]];
        const jsonData = XLSX.utils.sheet_to_json(worksheet);

        // Liste des colonnes à afficher par défaut
        const defaultVisible = ["fr_terme", "de_terme", "fr_Abréviation", "fr_Définition"];

        const columns = Object.keys(jsonData[0] || {}).map(key => ({
            title: key,
            field: key,
            headerFilter: "input",
            headerFilterPlaceholder: "Filtrer...",
            // Si la colonne n'est pas dans la liste par défaut, on la masque
            visible: defaultVisible.includes(key)
        }));

        var table = new Tabulator("#excel-table", {
            data: jsonData,
            layout: "fitColumns", // S'adapte à la largeur du conteneur
            pagination: "local",
            paginationSize: 15,
            columns: columns,
        });

        // Ajouter une interface simple pour gérer les colonnes manuellement
        // Tu peux ajouter ce bouton au-dessus de ton div si besoin
        console.log("Tabulator est prêt. Pour afficher toutes les colonnes : table.showColumn('NOM_COLONNE');");
    });
</script>
Source : [AgroTerm](https://www.agroterm.ch/), état au 25.07.2026
