---
date: 2026-08-07
title: Ordres de grandeur
categories:
  - Paiements-directs
description: Ordres de grandeur surfaces et animaux
type: Document
---


<script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
<link href="https://unpkg.com/tabulator-tables@5.5.2/dist/css/tabulator.min.css" rel="stylesheet">
<script type="text/javascript" src="https://unpkg.com/tabulator-tables@5.5.2/dist/js/tabulator.min.js"></script>

<!-- Style simple pour les onglets -->
<style>
  .tab-container { margin-bottom: 20px; }
  .tab-buttons { display: flex; border-bottom: 2px solid #ccc; margin-bottom: 15px; }
  .tab-btn { background: #f1f1f1; border: none; padding: 10px 20px; cursor: pointer; font-size: 16px; font-weight: bold; margin-right: 5px; border-radius: 5px 5px 0 0; }
  .tab-btn.active { background: #007bff; color: white; }
  .tab-content { display: none; }
  .tab-content.active { display: block; }
</style>

#### SURFACES

<!-- Boutons des onglets -->
<div class="tab-container">
  <div class="tab-buttons">
    <button class="tab-btn active" onclick="openTab(event, 'tab-cat1')">Niveau 1</button>
    <button class="tab-btn" onclick="openTab(event, 'tab-cat2')">Niveau 2</button>
    <button class="tab-btn" onclick="openTab(event, 'tab-cat3')">Code culture</button>
  </div>

  <!-- Contenu des onglets -->
  <div id="tab-cat1" class="tab-content active">
    <div id="table-cat1" style="width: 100%; max-width: 100%;"></div>
  </div>
  <div id="tab-cat2" class="tab-content">
    <div id="table-cat2" style="width: 100%; max-width: 100%;"></div>
  </div>
  <div id="tab-cat3" class="tab-content">
    <div id="table-cat3" style="width: 100%; max-width: 100%;"></div>
  </div>
</div>

<hr style="margin: 40px 0;">

#### Animaux
<div id="table-animaux" style="width: 100%; max-width: 100%; margin-top: 10px;"></div>

<script>
// Fonction pour gérer le changement d'onglets
function openTab(evt, tabName) {
    var contents = document.getElementsByClassName("tab-content");
    for (var i = 0; i < contents.length; i++) {
        contents[i].classList.remove("active");
    }
    var buttons = document.getElementsByClassName("tab-btn");
    for (var i = 0; i < buttons.length; i++) {
        buttons[i].classList.remove("active");
    }
    document.getElementById(tabName).classList.add("active");
    evt.currentTarget.classList.add("active");

    setTimeout(function() {
        window.dispatchEvent(new Event('resize'));
    }, 50);
}

// Fonction générique corrigée pour charger l'en-tête complet et gérer le tri numérique
function loadExcelTable(filePath, sheetName, selector) {
    fetch(filePath)
        .then(response => response.arrayBuffer())
        .then(data => {
            const workbook = XLSX.read(data, {type: 'array'});
            const targetSheetName = sheetName && workbook.Sheets[sheetName] ? sheetName : workbook.SheetNames[0];
            const targetSheet = workbook.Sheets[targetSheetName];

            // 1. Lecture brute de la ligne d'en-tête (ligne 1) pour attraper TOUTES les colonnes
            const rawData = XLSX.utils.sheet_to_json(targetSheet, {header: 1});
            if (rawData.length === 0) return;
            const headers = rawData[0]; 

            // 2. Lecture normale des lignes de données
            const jsonData = XLSX.utils.sheet_to_json(targetSheet);

            // 3. Détection automatique du type numérique (si les valeurs de la colonne sont majoritairement des nombres)
            const columns = headers.map(key => {
                // Vérifie si la première valeur non vide de la colonne est un nombre
                let sampleValue = jsonData.find(row => row[key] !== undefined && row[key] !== "")?.[key];
                let isNumeric = !isNaN(sampleValue) && sampleValue !== undefined && typeof sampleValue !== 'string';

                // Tu peux aussi forcer manuellement des mots-clés dans le nom de la colonne si besoin :
                // if (key.toLowerCase().includes('montant') || key.toLowerCase().includes('surface')) isNumeric = true;

                return {
                    title: key,
                    field: key,
                    headerFilter: "input",
                    headerFilterPlaceholder: "Filtrer...",
                    sorter: isNumeric ? "number" : "string",
                    visible: true
                };
            });

            new Tabulator(selector, {
                data: jsonData,
                layout: "fitColumns",
                pagination: "local",
                paginationSize: 15,
                columns: columns,
            });
        })
        .catch(error => console.error('Erreur lors du chargement de ' + filePath, error));
}

// Chargement des 4 tableaux
loadExcelTable('/documents-utiles/fichiers/stats_surfaces_pdir25_2026-08-05.xlsx', 'cat1', '#table-cat1');
loadExcelTable('/documents-utiles/fichiers/stats_surfaces_pdir25_2026-08-05.xlsx', 'cat2', '#table-cat2');
loadExcelTable('/documents-utiles/fichiers/stats_surfaces_pdir25_2026-08-05.xlsx', 'cat3', '#table-cat3');
loadExcelTable('/documents-utiles/fichiers/stats_animaux_pdir25_2026-08-05.xlsx', 'animaux', '#table-animaux');
</script>
