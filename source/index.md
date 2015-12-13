---
title: SupTrip CAMPUS

language_tabs:
  - html
  - java

toc_footers:
  - <a href='#'>Documentation Powered by SupTrip Campus TEAM</a>

search: true
---

# Introduction

Bienvenue sur la documentation officielle de SupTrip Campus! 
Vous pouvez utiliser notre application pour avoir accès à de nombreux voyages entre tous les campus. N'hésitez plus!

Nous avons plusieurs langages au sein de notre application (Java JEE, JQuery/JavaScript, HTML5, intégration du framework BootStrap). Vous pouvez voir des exemples de code dans la zone sombre à votre droite, vous pouvez changer la langue de programmation en fonction des exemples donnés avec les onglets en haut à droite.


# Page d'accueil

> Message de bienvenue, si l'utilisateur est connecté il sera affiché avec son ID Booster & restitution de statistiques diverses et variées.

```html
<!-- HOME.JSP -->
<!-- WELCOME MESSAGE -->
	<c:choose>
		<c:when test="${! empty sessionScope.user.getIdbooster()}">
			<h2>
				Bienvenue à toi,
				<c:out value="${sessionScope.user.getIdbooster()}" />
				!
			</h2>
		</c:when>
		<c:otherwise>
			<h2>Bienvenue !</h2>
		</c:otherwise>
	</c:choose>

	<!-- CONTENT -->
	...
	<!-- END CONTENT -->

	<!-- Début restitution des stats -->
	<p>
		C'est <b><c:out value="${requestScope.countUser}" /></b>
		utilisateurs passionés par SupTrip !
	</p>
	<p>
		C'est <b><c:out value="${requestScope.countCampus}" /></b> campus
		actifs par SupTrip !
	</p>
	<p>
		C'est <b><c:out value="${requestScope.countTrip}" /></b> voyages
		organisés par SupTrip !
	</p>

	<!-- TOP USER -->
	<div class="col-md-10 col-centered custyle">
		<table class="table table-striped custab">
			<thead>
				<tr>
					<th>ID Booster</th>
					<th>Prénom</th>
					<th>Nom du campus</th>
					<th>Nb de voyages</th>
				</tr>
			</thead>
			<c:forEach var="user" items="${users}">
			<c:if test="${user.getIdbooster() != 0 }">
				<tr>
					<td>#1 ${user.getIdbooster()}</td>
					<td>${user.getFirstname()}</td>
					<td>${user.getCamp().getName()}</td>
					<td><span class='btn btn-info btn-xs'>${user.trips.size()} voyages</span></td>
				</tr>
		</c:if>
			</c:forEach>
		</table>
	</div>			
```

```java
String countUser = DaoFactory.getUserDao().countUser();
		
List<Camp> listOfCampus = DaoFactory.getCampDao().getAllCampus();
int countCampus = listOfCampus.size();
int countTrip = (countCampus * (countCampus - 1));


List<User> users = (List<User>) DaoFactory.getUserDao().getAllUsers();

request.setAttribute("users", users);
request.setAttribute("countTrip", countTrip);
request.setAttribute("countCampus", countCampus);
request.setAttribute("countUser", countUser);

RequestDispatcher rd = request.getRequestDispatcher("/home.jsp");
rd.forward(request, response);
```

<b>Un résumé :</b> SupTrip est une toute nouvelle agence de voyage innovante créé par SUPINFO. Si vous aimez voyager facilement, vous êtes au bon endroit ! N’hésitez pas à vous inscrire et bénéficiez de nombreux tarifs étudiants !

### Integration des tagsLibs:
Les librairies de tags JSP (Taglibs) nous a permis de gérer quelques actions plus facilement. L'intégration se fait en une seule ligne: 
<code><%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%></code>


<aside class="notice">
Une fois connecté sur notre site, vous aurez accès à l'onglet "Panier" et "Voyager".
</aside>


# Utilisateur - Inscription

> Formulaire d'inscription utilisateur, doPost().

```html
<!-- EGISTER.JSP - Exemple : champ "nom de famille" et champ "nom du campus" - Utilisation de pattern (regExp) -->
<div class="form-group">
	<input id="lastname" name="lastname" maxlength="50"
		placeholder="Nom de famille" required pattern="\S+"
		tabindex="3" />
</div>
</div>
<div class="col-xs-12 col-sm-6 col-md-6">
<div class="form-group">
	<select name="campusId" class="form-control">
		<c:forEach var="camp" items="${listOfCampus}">
			<option value="${camp.getId()}">${camp.getName()}</option>
		</c:forEach>
	</select>
</div>

<button type="submit" class="btn btn-info btn-block md">Envoyer</button>

```

```java
//RegisterServlet.java - Extrait de la méthode doPost(); 

	userExists = DaoFactory.getUserDao().UserExists(tmp_idbooster);
	if (userExists != null) {
		req.setAttribute("registerfailed", true);
		RequestDispatcher rd = req.getRequestDispatcher("/register.jsp");
		rd.forward(req, resp);
	} else {
		User user = new User();
		Camp camp = DaoFactory.getCampDao().getCamp(campusId);
		// etc ...
		user.setCamp(camp);
		DaoFactory.getUserDao().addUser(user);

		HttpSession httpSession = req.getSession();
		httpSession.setAttribute("user", user);
		resp.sendRedirect(req.getContextPath() + "/home");
```

SupTrip Campus utilise un formulaire d'inscription pour pouvoir garder nos utilisateurs en base et ainsi gérer leurs demandes au niveau des voyages.

### Parametres

Paramètre | Type | Description
--------- | ------- | -----------
IdBooster| Int | <b>Obligatoire & Unique.</b> Entre 5 et 8 chiffres maximum.
FirstName | String | <b>Obligatoire.</b> Limité à 50 charactères maximum, n'accepte pas les espaces blancs (trim).
LastName | String | <b>Obligatoire.</b> Limité à 50 charactères maximum, n'accepte pas les espaces blancs (trim).
CampusName | String | Non obligatoire. Limité à une liste de campus.
Email | String | Non obligatoire. Doit suivre le pattern <code>pattern="[a-zA-Z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,4}$"</code>.
Password | String | <b>Obligatoire.</b> Avec double vérification (JQuery), limité à 50 charactères maximum, n'accepte pas les espaces blancs (trim). Stocké en MD5 !

<aside class="notice"> 
<b>Côté FRONT</b> : <br/>
- Intégration du framework BootStrap. <br/>
- Utilisation du côté HTML des taglibs en Java JEE <code>"c:forEach"</code>. <br/ >
- Notre page page HTML est au format .JSP d'où : <code>items=${listOfCampus}</code>. <br/>
- Vérification du mot de passe identique avec JQuery. <br/> <br/>
<b>Côté BACK</b> : <br/>
- Utilisation de notre DaoFactory qui inclue notre classe User. <br/>
- Vérification que l'on récupère correctement notre ID-BOOSTER.<br/ >
- Création de la session de l'utilisateur : <code>HttpSession httpSession = req.getSession();</code>.
</aside>



# Utilisateur - Connexion

> Formulaire de la connexion utilisateur, doPost(), vérification.

```html
<!-- LOGIN.JSP -->

<!-- Check Login - Error Message -->
<c:if test="${loginfailed}">
	<p class="bg-danger" style="padding: .5em;">Identifiant ou mot de passe incorrect.</p>
</c:if>

<div class="panel-container small-panel">
<!-- Form Begin -->
<div class="form-box">
	<form method="post" action="login">
        <input type="number" name="idbooster" min="0" type="email" placeholder="Votre ID-Booster" 
        	required pattern="[0-9]{5,8}" title="Votre ID doit contenir 5 à 8 chiffres." style="padding-left: 1.3em;" />
        <input class="last-input" maxlength="50" placeholder="Entrez votre mot de passe" type="password"
			id="password" name="password" required pattern="\S+" />
        <button class="btn btn-info btn-block md" type="submit">Connexion</button>
    </form>
</div>
```

```java
//LoginServlet.java - extrait de la méthode doPost();
User user = new User();
String idbooster = req.getParameter("idbooster");
String password = null;
try {
	password = Hash.encode(req.getParameter("password"), "MD5");
} catch (NoSuchAlgorithmException e) {
	e.printStackTrace();
}

user = DaoFactory.getUserDao().getUser(idbooster, password);

if (user != null) {
	HttpSession httpSession = req.getSession();
	httpSession.setAttribute("user", user);
	resp.sendRedirect(getServletContext().getContextPath() + "/home");
} else {
	req.setAttribute("loginfailed", true);
	RequestDispatcher rd = req.getRequestDispatcher("/login.jsp");
	rd.forward(req, resp);
}
```


SupTrip Campus utilise un formulaire de connexion pour pouvoir donner tous les accès à nos utilisateurs en base et ainsi accéder à des pages supplémentaires. Nous avons mis en place l'utilisation d'un filtre (authentication).

### Parametres

Paramètre | Type | Description
--------- | ------- | -----------
IdBooster| Int | <b>Obligatoire.</b> Entre 5 et 8 chiffres maximum.
Password | String | <b>Obligatoire.</b> Vérification via la BDD, limité à 50 charactères maximum, n'accepte pas les espaces blancs (trim).

<aside class="warning">
Si Combinaison ID-Booster/MDP non valide, affichage d'un message d'erreur. 
</aside>

<aside class="success">
Si Combinaison ID-Booster/MDP valide, création de la session pour l'utilisateur, redirection sur la page d'accueil. 
</aside>


# Trip - Liste des voyages 

> Listing de tous les voyages possibles, addTrip();

```html
<!-- TRIP.JSP -->
<table class="table table-striped custab">
	<thead>
		<tr>
			<th class="th-cart">Départ</th>
			<th class="th-cart">Arrivée</th>
			<th></th>
		</tr>
	</thead>
	<% int count = 1; %>
	<c:forEach items="${campusOrder}" var="departure">
		<c:forEach items="${listOfCampus}" var="arrival">
			<tr class="row-trip">
				<c:if test="${departure.getId() != arrival.getId()}">
					<td><%=count%> <% count++;%>.
						 &nbsp;<c:out value="${departure.getName()}" />
					</td>
					<td><c:out value="${arrival.getName()}" /></td>
					<td>
						<form action="addTrip" method="post">
							<input type="hidden" name="departure" value="<c:out value="${departure.getId()}" />">
							<input type="hidden" name="arrival" value="<c:out value="${arrival.getId()}" />">
							<button type="submit" class="btn btn-info">
								<span class="glyphicon glyphicon-shopping-cart"></span> Ajouter au panier
							</button>
						</form>
					</td>
				</c:if>
			</tr>
		</c:forEach>
	</c:forEach>
</table>
```

```java
//AddTripServlet.java - Extrait de la méthode doPost();
	Camp departure = DaoFactory.getCampDao().getCamp(idDeparture);
	Camp arrival = DaoFactory.getCampDao().getCamp(idArrival);

	Trip trip = new Trip();

	trip.setUser(user);
	trip.setDeparture(departure);
	trip.setArrival(arrival);

	@SuppressWarnings("unchecked")
	List<Trip> list = (List<Trip>) session.getAttribute("listOfTrips");
	if (list == null) {
		list = new LinkedList<Trip>();
	}
	list.add(trip);
	session.setAttribute("listOfTrips", list);
	resp.sendRedirect(req.getContextPath() + "/auth/shoppingCart");
	rd.forward(request, response);	
```

SupTrip Campus utilise deux méthodes d'affichage pour lister tous les voyages possibles. La première sous forme de deux select, la seconde consiste à afficher tous les voyages sous forme de tableau. Pour faciliter la tâche à notre utilisateur, nous lui avons mis à disposition une pagination par ordre alphabétique et une barre de recherche.

<aside class="warning">
Il est impossible à l'utilisateur de choisir un campus de départ égal au campus d'arrivée. Gestion côté front et back (JQuery & Java) 
</aside>

<aside class="success">
Une fois un voyage ajoutée au panier, l'utilisateur va être redirigé vers la page "Panier" avec une liste de ses voyages.
</aside>


# Trip - Panier

> Ajout sur le compte utilisateur des différents trips présent dans le panier - confirmation de l'ajout.

```html
<!-- ShoppingCart.JSP -->
<table class="table table-hover">
<tbody>
	<c:forEach var="trip" items="${sessionScope.listOfTrips}">
		<tr>
			<td class="col-sm-8 col-md-6">
				<div class="media">
						<h4 class="media-heading">
							<a href="#" style="color: #31b0d5;">Trip Index: ${sessionScope.listOfTrips.indexOf(trip)}</a>
						</h4>
						<h5 class="media-heading">User ID : ${trip.user.getId()}</h5>
				</div>
			</td>
			<td class="col-sm-1 col-md-1 text-center"><strong>${trip.departure.getName()}</strong></td>
			<td class="col-sm-1 col-md-1 text-center"><strong>${trip.arrival.getName()}</strong></td>
			<td class="col-sm-1 col-md-1">
				<form method="post">
					<input type="hidden" value="${sessionScope.listOfTrips.indexOf(trip)}" name="tripIndex"/>
					<input type="hidden" value="delete" name="requestType"/>
					<button type="submit" class="btn btn-danger">
						<span class="glyphicon glyphicon-remove"></span> Supprimer
					</button>
				</form>
			</td>
		</tr>
	</c:forEach>
	<tr>
		<form method="post">
		<input type="hidden" value="post" name="requestType">
		<button type="submit" class="btn btn-info">
			Confirmer <span class="glyphicon glyphicon-play"></span>
		</button>
		</form>
	</tr>
</tbody>
</table>
```

```java
//ShoppingCartServlet.java - Extrait de la méthode doPost();
RequestDispatcher rd = null;
String requestType = req.getParameter("requestType");

HttpSession session = req.getSession();
if ("post".equals(requestType)) {

	if (session.getAttribute("listOfTrips") == null) {
		req.setAttribute("notice", "Pas de commande à valider !");
	} else {
		@SuppressWarnings("unchecked")
		List<Trip> list = (List<Trip>) session.getAttribute("listOfTrips");
		for (Trip trip : list) {
			DaoFactory.getTripDao().addTrip(trip);
		}
		User tmp_user = (User) session.getAttribute("user");
		String idbooster = Long.toString(tmp_user.getIdbooster());
		User user = (User) DaoFactory.getUserDao().getUser(idbooster, tmp_user.getPassword());
		session.setAttribute("user", user);
		req.setAttribute("notice", "Commande bien enregistrée !");
	}
	session.removeAttribute("listOfTrips");
	
} else if ("delete".equals(requestType)) {
	int tripIndex = 0;
	@SuppressWarnings("unchecked")
	List<Trip> list = (List<Trip>) session.getAttribute("listOfTrips");
	String tmp_tripindex = req.getParameter("tripIndex");
	try {
		tripIndex = Integer.parseInt(tmp_tripindex);
	} catch (NumberFormatException nfe) {
		// TODO gérer l'exception et l'erreur !
	}
	list.remove(tripIndex);
	session.setAttribute("listOfTrips", list);
	req.setAttribute("notice", "Votre voyage a été supprimé de votre liste !");
}
rd = req.getRequestDispatcher("/auth/shoppingCart.jsp");
rd.forward(req, resp);
```

SupTrip Campus utilise un système de panier. Une fois que notre panier contient au moins un élément, nous ajoutons en base toutes les données en fonction de l'utilisateur qui a commandé.

<aside class="warning">
Il est impossible d'envoyer un panier vide, un message d'erreur sera affiché pour avertir l'utilisateur.
</aside>

<aside class="notice">
L'utilisateur peut selon son envie retourner via un bouton à la liste des voyages ou supprimer un voyage ajouté dans son panier. Un message de confirmation sera affiché pour avertir l'utilisateur que l'action a été affectué.
</aside>

<aside class="success">
Une fois que l'utilisateur confirme sa transaction, toute la liste de voyage sera ajouté en base. Un message de confirmation sera affiché pour avertir l'utilisateur que la prise en compte de ses voyages a été effectué.
</aside>


