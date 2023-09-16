---
cssclasses:
  - custom-class-gray-italic
---
# PHP Code
## Datenbank verbinden
```php
<?php
// Variablen
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "myDB";

// Verbindung erstellen
$connection = new mysqli($servername, $username, $password, $dbname);

//// hier wird der nachfolgende Code stehen zum bearbeiten der Daten ////

// Verbindung trennen
mysqli_close($connection);
?>
```

PHP Code fängt immer mit `<?php` and und endet mit `?>`.
*(In den nachfolgenden Code-Snippets werde ich auf diese verzichten)*

Die Variablen sind unsere Daten zum einloggen in die Datenbank.
Um uns nun mit der Datenbank zu verbinden nutzten wir `new mysqli()`. Dieser Funktion geben wir in den Klammer alle Daten mit, welche wir zum Verbinden mit einer Datenbank brauchen (Reihenfolge ist wichtig, siehe Code).
Das `new` ist lediglich ein Hinweis darauf, dass wir eine neue Verbindung erstellen.
Um diese Verbindung nun weiter verwenden zu können, speichern wir sie in der Variable `$connection`.
Wenn wir die Verbindung wieder schließen wollen, rufen wir die Funktion `mysqli_close` auf und geben ihr `$connection` als Argument mit.
*Fall du dich wunderst: das i in der Funktion `mysqli` steht für "improved"*

## SELECT FROM
```php
//SELECT FROM
$datensatz = mysqli_query($connection,"SELECT stadtname FROM stadt WHERE
			stadt_id='1';");
$zeile = mysqli_fetch_array($datensatz);
echo $zeile[0];
```

Um Daten aus unsere Datenbank abzurufen nutzten wir die Funktion `mysqli_query()` (eng. query = Abfrage). Die Ausgabe dieser Abfrage speichern wir in der Variable `$datensatz`.
Wir geben der Funktion unsere Datenbank-Verbindung (`$connection`) mit, sowie den SQL Befehl, welchen wir ausführen wollen.
Um Daten aus eine Datenbank abzurufen nutzten wir `SELECT (Spalte) FROM (Tabelle)`.
Falls wir alle Spalten einer Tabelle abrufen wollen nutzen wir `SELECT * FROM (Tabelle)`.
Mit `WHERE stadt_id='1'` rufen wir nur die Daten ab, bei denen die stadt_id 1 beträgt
Um die unverarbeiteten Daten in ein richtigen Format (Array) zu formen, nutzen wir die Funktion `mysqli_fetch_array()` und geben den Datensatz als Argument mit.
Da wir nun die Daten der erste Spalte mit der stadt_id 1 aus unserer Datenbank ausgeben wollen nutzen wir `echo $zeile[0]`. Die 0 sorgt dafür, dass nur das erste Element ausgegeben wir (falls es noch einen weiteren Datensatz mit der stadt_id 1 geben sollte).

### Beispiel: Tabelle aller Daten in der Datenbank
```php
<?php
	// Importieren von Variablen wurden in dieser Datei definiert
	include("public_vars.php"); 
	//DB verbinden	
	$db_connect = new mysqli($db_host,$db_user,$db_password,$db_name);
	
	//SELECT FROM
	$datensatz=@mysqli_query($db_connect,"SELECT * FROM stadt;");
	
	echo "
<table border='1'>
	";
	while($zeile=@mysqli_fetch_array($datensatz)) {
		echo "
	<tr>
		<td>".$zeile['stadt_id']."</td>
		<td>".$zeile['stadtname']."</td>
		<td>".$zeile['einwohnerzahl']."</td>
	</tr>
		";
	}
	echo "
</table>
	";
	//DB trennen
	@mysqli_close($db_connect);
?>
```
*kompletter Code*

Die HTML-Struktur: *(was das echo ausgeben wird)*
```html
<table border='1'>
	<tr>
		<td>".$zeile['stadt_id']."</td>
		<td>".$zeile['stadtname']."</td>
		<td>".$zeile['einwohnerzahl']."</td>
	</tr>
</table>
```

Da wir aber nicht nur eine Zeile ausgeben wollen, sondern so viele wie es gibt, müssen wir eine `while`-Schleife einfügen. Solang noch eine `$zeile` existiert, wird diese Schleife fortgesetzt.
Pro Durchlauf fügen wir neues <[[html-elemete#tr Element|tr]]> Element ein (Neue Zeile), welches weitere <[[html-elemete#td Element|td]]> Element enthält, welche wiederum die jeweiligen Informationen des Datensatzes beinhalten.
![[20230915233056.png]]

## INSERT INTO, UPDATE, DELETE

Nachdem wir unsere [[QUIZZ#Datenbank verbinden|Datenbank verbunden]] haben, können wir eine SQL-Anfrage machen.
```php
$datensatz=mysqli_query($connection,"INSERT INTO stadt (stadtname, einwohnerzahl)
									  VALUES('Freiberg', '39700');");
```

`INSERT INTO (Tabellen) VALUES (Werte)` sorgt für das Hinzufügen der eingegebenen Daten in die Datenbank. Wir wählen die Tabelle `stadt` und geben zusätzlich die Spalten an, zu denen wir Informationen hinzufügen (`stadtname, einwohnerzahl`). Als nächstes geben wir die Daten an `VALUES('Freiberg', '39700')`. 
Diesen Prozess können wir nun schöner gestalten, durch ein Formular auf der Webseite.

```php
<?php
include("public_vars.php");

//DB verbinden	
$db_connect = new mysqli($db_host,$db_user,$db_password,$db_name);

if(isset($_POST['speichern'])) {
	//INSERT INTO
	$datensatz=@mysqli_query($db_connect,"INSERT INTO stadt (stadtname, einwohnerzahl)
		VALUES('".$_POST['stadtname']."', '".$_POST['einwohnerzahl']."');");
}

//Eingabeformular
echo "
<form name='form0' action='' method='post' enctype='multipart/form-data' autocomplete='off'>
<table border='0' cellpadding='5' cellspacing='0'>
	<tr>
		<td>Stadtname</td>
		<td><input name='stadtname' type='text' style='width:200px;'></td>
	</tr>
	<tr>
		<td>Einwohnerzahl</td>
		<td><input name='einwohnerzahl' type='text' style='width:200px;'></td>
	</tr>
</table>
<input style='margin-top: 15px;' type='submit' name='speichern' value='Speichern'>
</form>
";

//DB trennen
@mysqli_close($db_connect);
?>
```

Wir nutzen hier ein <[[html-elemete#form Element|form]]> Element