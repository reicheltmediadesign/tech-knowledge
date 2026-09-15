# HTML

## Tabelle filtern mit JavaScript

Blendet alle Datenzeilen aus, die den eingegebenen Text nicht enthalten. Kopfzeilen (mit `th`) bleiben immer sichtbar.

```html
<input type="text" id="myInput" onkeyup="filterTable()" placeholder="Tabelle filtern...">

<table id="myTable">...</table>

<script>
    function filterTable() {
        var input = document.getElementById("myInput");
        var filter = input.value.toUpperCase();
        var table = document.getElementById("myTable");
        var tr = table.getElementsByTagName("tr");

        // Alle Zeilen durchgehen, Kopfzeilen werden unten übersprungen
        for (var i = 0; i < tr.length; i++) {
            // Prüfe, ob die aktuelle Zeile eine Kopfzeile ist (enthält th-Elemente)
            var th = tr[i].getElementsByTagName("th");
            if (th.length > 0) {
                // Wenn es eine Kopfzeile ist, immer anzeigen
                tr[i].style.display = "";
            } else {
                // Nur Datenzeilen filtern
                if (tr[i].textContent.toUpperCase().indexOf(filter) > -1) {
                    tr[i].style.display = "";
                } else {
                    tr[i].style.display = "none";
                }
            }
        }
    }
</script>
```
