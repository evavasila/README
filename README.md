# TASK 1
## ΕΙΣΑΓΩΓΗ ΠΟΛΥΓΩΝΩΝ ΣΤΟΝ ΧΑΡΤΗ
Στην σελίδα My Cities Manager μου ζητήθηκε να προσθέσω την δυνατότητα εισαγωγής πολυγώνων σε έναν χάρτη του google maps API.Πατώντας το κουμπί "CREATE POLYGON" ο χρήστης  μεταφέρεται σε έναν χάρτη του google maps API στον οποίο μπορεί να ζωγραφίσει ένα πολύγωνο. Το πολύγωνο αυτό είναι απαραίτητο να ξεκινάει και να τελειώνει με τα ίδια points διαφορετικά δεν θα μπορεί να σχηματιστεί. Παίρνουμε ,λοιπόν, τα points και στη συνέχεια αποθηκεύεται το πολύγωνο σε έναν πίνακα στο database.
- Αρχικά, φτιάχνουμε το αρχείο polygon.html όπου περιέχει τις συναρτήσεις για την εμφάνιση του χάρτη,την επιλογή σχεδίασης πολυγώνου και την επιστροφή αυτού σε έναν πίνακα.(https://pleiades.math.uoi.gr/~ma11882/tasks/task1/polygon.html)

**Συνάρτηση σχεδίασης πολυγώνου**
```
var drawingManager= new google.maps.drawing.DrawingManager({
		drawingMode: google.maps.drawing.OverlayType.MARKER,
		drawingControl: true,
		drawingControlOptions:{
			position: google.maps.ControlPosition.TOP_CENTER,
			drawingModes:[
				google.maps.drawing.OverlayType.POLYGON,
				]
			},
			polygonOptions:{
				strokeColor:'black',
				strokeWeight: 2,
				clickable:true,
				draggable:false,
				editable:true,
				fillColor:'red',
				fillOpacity:0.1
			}

	});
  drawingManager.setMap(map);
  ```
  **Συνάρτηση αποθήκευσης πολυγώνου σε πίνακα**
  ```
  google.maps.event.addListener(drawingManager,'polygoncomplete',function(polygon){
		if (polygon.type != google.maps.drawing.OverlayType.MARKER) {
			selectedShape=polygon;
			var coordinates= (polygon.getPath().getArray());
			let pcoords={type:"polygon", points:coordinates};
			console.log(pcoords);
			geometries.unshift(pcoords);
			console.log(geometries);
		}
	});
  ```
  
- Έπειτα, δημιουργούμε πίνακα στο database με τις εντολές mysql geometry
```
$polygon_table="CREATE TABLE TBL_polygon (".
"ID INT NOT NULL AUTO_INCREMENT,".
"geometry POLYGON NOT NULL,".
"PRIMARY KEY (Id)".
")";
```
- Τέλος, φτιάχνουμε το αρχείο polygon.php που εξασφαλίζει την σύνδεση στη mysql και εισαγωγή των σχεδιασμένων πολυγώνων στον πίνακα της βάσης
```
if ($db->is_mysql_connect()) {
  if (isset($_POST['coordinates']) && isset($_POST['id'])) {
    $points="Coordinates($_POST['coordinates']);"
    $id=$_POST['id'];
    $polygon_table=array("id"=>$id,"coordinates"=>$points);
    $db->mysql_insert($polygon_table);
   }
}
```
