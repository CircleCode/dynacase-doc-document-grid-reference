# Mode d'emploi

## Dépendances

L'intégration du plugin requiert l'ajout dans la page web des composants suivants :

* **json2** : lib/data/json2.js
* **jquery** : lib/jquery/jquery.js
* **jquery-ui** : lib/jquery-ui/js/jquery-ui-1.8.21.custom.min.js & lib/jquery-ui/css/smoothness/jquery-ui-1.8.21.custom.css
* **jquery dataTables** : lib/jquery-dataTables/js/jquery.dataTables.min.js & lib/jquery-dataTables/css/jquery.dataTables_themeroller.css
* **jquery docGrid** : DOCUMENT_GRID_UI/Layout/jquery.docGrid-code.js & DOCUMENT_GRID_UI/Layout/jquery.docGrid.css

**json2** : cet élément est nécessaire pour faire fonctionner le plugin sur les navigateurs ne possédant pas l'objet JSON.

Les adresses des éléments sont données à titre indicatif et sont valable dans l'optique d'une intégration au sein d'une action/application.

## Droits

Les utilisateurs devant avoir accès à DocumentGrid et ses actions associées doivent avoir l'ACL : BASIC de l'application DOCUMENT_GRID_UI.

## Initialisation

Idéalement l'initialisation du plugin doit avoir lieu sur l'évènement "ready" de la page en cours pour permettre à la DOM et aux dépendances d'être chargées.

L'initialisation de la documentGrid se fait sur une balise &lt;table&gt;, celle-ci doit être pré-existante à l'initialisation. On doit ensuite la sélectionner à l'aide de jQuery et l'initialiser:

	<html>
		<head>
			<title>DocumentGrid</title>

			<link href="lib/jquery-ui/css/smoothness/jquery-ui.css" rel="stylesheet" type="text/css"/>
			<link href="lib/jquery-dataTables/css/jquery.dataTables_themeroller.css" rel="stylesheet" type="text/css" />
			<link href="DOCUMENT_GRID_UI/Layout/jquery.docGrid.css" rel="stylesheet" type="text/css" />

			<script type="text/javascript" src="lib/jquery/jquery.js"></script>
			<script type="text/javascript" src="lib/jquery-ui/js/jquery-ui.js"></script>
			<script type="text/javascript" src="lib/jquery-dataTables/js/jquery.dataTables.min.js"></script>
			<script type="text/javascript" src="DOCUMENT_GRID_UI/Layout/jquery.docGrid-code.js"></script>

			<script type="text/javascript">
				$(document).on("ready", function () {
					$("#mydocGrid").docGrid({
						collection : "MY_COLLECTION",
						columnsDef : {
							"defaultFam" : "MYDEFAULTFAM",
							"columns" : [
								{type : "openDoc"},
								{id : "title"}
							]
						}
					});
				});
			</script>
		</head>

		<body>
			<table id="mydocGrid"></table>
		</body>

	</html>

Exemple d'initialisation de documentGrid contenant le titre et un bouton permettant d'ouvrir le document.

### Options de configuration

Les éléments en gras sont obligatoires.

* **collection** : nom logique d'une collection, elle est la source des données affichées (chaîne de caractères),
* **columnsDef** : configuration des colonnes (objet javascript),
* *filterable* : il indique que la grille proposera ou pas des filtres (valeur par défaut : false) (booléen),
* *sortable* : il indique que la grille proposera ou pas la possibilité de trier les éléments par colonnes (valeur par défaut : true) (booléen),
* *dataTableOptions* : objet de configuration de la dataTable associée à la documentGrid (objet javascript)
* *withOverlay* : indique si le système d'overlay doit être chargé avec la grille, ce système permet d'ouvrir les liens générés par la grille dans une fenêtre modale (valeur par défaut : true) (booléen),
* *offlineColumnsDef* : active ou désactive la recherche de la définition des colonnes sur le serveur (booléen),
* *autoload* : si à false désactive le chargement de la table par défaut, dans ce cas la table est chargée sans requête server et sans données (l'IHM indique alors aucune données et résultats 0 sur 0 à charge du progammeur de customizer l'IHM) (valeur par défaut : true) (booléen).
* *gridDataSourceUrl* : url de récupération des données alimentant la base (valeur par défaut : "?app=DOCUMENT_GRID_UI&action=GETJSONDOCGRIDCONTENT").
* *columnsDefUrl* : url de récupération de la définition des colonnes de la tables, inutile si offlineColumnsDef est à false (valeur par défaut : "?app=DOCUMENT_GRID_UI&action=GETCOLUMNSDEFINITION").
* *filterEnumContentUrl* : url de récupération des listes déroulantes pour les énumérés (valeur par défaut : "?app=DOCUMENT_GRID_UI&action=GETENUMCONTENT"),
* *filterStateContentUrl* : url de récupération des listes déroulantes pour les états (valeur par défaut : "?app=DOCUMENT_GRID_UI&action=GETSTATES"),
* *criterias* : tableau de configuration de critères s'appliquant à la table. Le format des critères est décrit dans le chapitre critère de cette documentation.

*note* : seules les options 'collection' et 'criterias' peuvent changées une fois la grille initialisée ($("#mydocGrid").docGrid("option", "collection", "new_collection_name");).


#### Détail de la configuration 

* columnsDef : cet objet de configuration donne les éléments permettant d'établir la configuration des colonnes de la dataTable. Pour ce faire, il possède deux propriétés : 
	* *defaultFam* : nom logique de la famille par défaut des attributs des colonnes (chaîne de caractères),
	* **columns** : tableau de définition de colonnes. Chaque élément est un objet composé des éléments suivants :
		* *id* : nom logique de l'attribut/propriété contenu dans cette colonne (chaîne de caractères),
		* *label* : nom de la colonne à afficher (chaîne de caractères),
		* *famId* : famille contenant l'attribut id (chaîne de caractères). Cet élément n'est pas obligatoire dans les cas suivants : 
			* defaultFam est défini,
			* l'élément à représenter est une propriété,
			* l'élément à représenter est une colonne virtuelle.
		* *sortable* : indique si la colonne est triable (cette valeur est calculée automatiquement si l'élément en cours est une propriété ou un attribut, sinon sa valeur par défaut est false) (booléen),
		* *filterable* : indique si la colonne est filtrable (cette valeur est calculée automatiquement si l'élément en cours est une propriété ou un attribut, sinon sa valeur par défaut est false) (booléen),
		* *type* : indique le type de la colonne, il est utilisé pour définir le type de rendu de la colonne ou pour définir des colonnes virtuelles (il faut alors utiliser le type "void") (cette valeur est calculée automatiquement si l'élément en cours est une propriété ou un attribut, sinon sa valeur est obligatoire). Les types virtuels pré-défini sont : 
			* *openTitle* ajoute une colonne permettant d'ouvrir le document en cours avec le titre du document comme lien,
			* *openDoc* : ajoute une colonne avec un bouton pour ouvrir le document
		* *render* : fonction ou chaîne de caractère surchargeant la fonction de rendu de la colonne en cours. Un render par défaut est fourni sur l'ensemble des colonnes de type attribut/propriété, il appelé avec la string "text" il permet d'avoir un rendu textuel de la colonne. Sinon render doit être une fonction qui retourne une chaine de caractère qui sera parsée en DOM et intégrée dans la cellule en cours de la table, elle a en entrée la ligne en cours et pour scope l'objet de configuration de la colonne en cours (fonction ou string),
		* *withIcon* : indique si une icône doit accompagner le rendu de la colonne en cours. Cette option est valide pour les colonnes de type : title, openDoc, openTitle, docid, file, image (booléen : false par défaut),
		* *withColor* : indique si la couleur doit accompagner le rendu de l'état. Cette option est valide pour les colonnes de type state (booléen : false par défaut),
		* *docTitle* : indique le nom de l'attribut docTitle. Cette option est valide pour les colonnes de type relation sur lesquel un filtre est prévu (chaine de caractère, rempli automatique si la grille n'est pas en mode offlineColumnsDef).

Lors de la définition d'une colonne doivent être présent a minima soit l'id, soit le type.

* dataTableOptions : options bas niveau de la dataTable (voir la page d'aide de dataTable). Il est à noter que si des options de configuration de la dataTable sont ajoutées Anakeen ne garantit le plus le fonctionnement de celle-ci.


## Méthodes associées

Deux méthodes sont associées à l'objet documentGrid :

 * destroy : elle permet de détruire la table. La table est alors supprimée (la balise reste en place, mais son contenu est vidé),
 * refresh : elle permet de rafraîchir la table, celle-ci conserve son état courant (page en cours, tri, filtre) mais recharge ses données en effectuant une requête serveur.
 * getFilters : elle permet de récupérer les filtres actuellement en cours sur la grille.

Elles se déclenchent de la manière suivante : $("#mydocGrid").docGrid("fonction_name");

## Évènement associés :

* *error* : déclenché chaque erreur détectée par la dataTable, il fourni un objet event et un objet erreur (tableau de messages d'erreurs),
* *change* : déclenché à chaque rechargement de la table (filtrage, changement de page, etc), il fourni un objet event et un objet contenant les informations qui vont être transmises à la dataTable (la modification de cet objet est déconseillée car elle peut avoir comme effet de bord de modifier le contenu de la table),
* *redraw* : déclenché après la mise à jour de la table et son ajout dans la page web.

Les évènements peuvent être écoutés de deux manières différentes :

* en utilisant les fonctionnalités pour s'attacher à un évènement de jQuery, il faut alors préfixer l'évènement cible par le nom du widget en minuscule (dans notre cas docgrid)
	$("#mydocGrid").on("docgriderror", function(e, ui) { console.log(ui);});
* en s'inscrivant directement à la création du widget :
	$("#mydocGrid").docGrid({ error : function(e, ui) { console.log(ui);}};

