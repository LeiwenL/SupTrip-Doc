---
title: SupTrip CAMPUS

language_tabs:
  - html
  - java
  - javascript

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
		<c:when test="${! empty sessionScope.idbooster}">
			<h2>
				Bienvenue à toi,
				<c:out value="${sessionScope.idbooster}" />
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
	<h3>Quelques stats ...</h3>
	<p>C'est <b><c:out value="${requestScope.countUser}"/></b> utilisateurs passionés par SupTrip !</p>
	<p>C'est <b><c:out value="${requestScope.countCampus}"/></b> campus actifs par SupTrip !</p>
	<p>C'est <b><c:out value="${requestScope.countTrip}"/></b> voyages organisés par SupTrip !</p>
				
```

```java
	//HomeServlet.java - extrait de la méthode doGet(); - Permet d'afficher des statistiques.

	String countUser = DaoFactory.getUserDao().countUser();
	
	List<Camp> listOfCampus = DaoFactory.getCampDao().getAllCampus();
	int countCampus = listOfCampus.size();
	int countTrip = (countCampus * (countCampus - 1));

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

> Formulaire d'inscription utilisateur, doPost(), vérification.

```html
<!-- EGISTER.JSP - Exemple : champ "nom de famille" et champ "nom du campus" - Utilisation de pattern (regExp) -->
<div class="form-group">
	<input id="lastname" name="lastname" maxlength="50"
		placeholder="Nom de famille" required pattern="\S+"
		tabindex="3" />
</div>
<div class="form-group">
	<select name="campusId" class="form-control">
		<c:forEach var="camp" items="${listOfCampus}">
			<option value="${camp[0]}">${camp[1]}</option>
		</c:forEach>
	</select>
</div>

<button type="submit" class="btn btn-info btn-block md">Envoyer</button>

```

```java
//RegisterServlet.java - Extrait de la méthode doPost(); 

		Long campusId = null;
		User user = new User();
		user.setLastname(req.getParameter("lastname"));
		user.setCampusId(campusId);

		DaoFactory.getUserDao().addUser(user);
		HttpSession httpSession = req.getSession();
		if (campusId != null) {
			Camp camp = DaoFactory.getCampDao().getCamp(user.campusId);
			httpSession.setAttribute("campusname", camp.name);
		}
```

```javascript
//Register.js - Exemple de la vérification du mot de passe

$(document).ready(function() {
	$('#validate-password, #password').on('keyup input', function() {
		if ($('#validate-password').val() != $('#password').val()) {
			$('#validate-password').css('border', '1px solid red');
		} else {
			$('#validate-password').css('border', '1px solid #ccc');
		}
	});
	$('#register-form').submit(function(e) {
		if ($('#validate-password').val() != $('#password').val()) {
			e.preventDefault();
		}
	});
});
```

SupTrip Campus utilise un formulaire d'inscription pour pouvoir garder nos utilisateurs en base et ainsi gérer leurs demandes au niveau des voyages.

### Parametres

Paramètre | Type | Description
--------- | ------- | -----------
IdBooster| Int | <b>Obligatoire.</b> Entre 5 et 8 chiffres maximum.
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
<!-- Beginning Form -->
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

		user = DaoFactory.getUserDao().checkUser(idbooster, password);

		if (user != null) {
			HttpSession httpSession = req.getSession();
			if (user.campusId != null) {
				Camp camp = DaoFactory.getCampDao().getCamp(user.campusId);
				httpSession.setAttribute("campusId", user.campusId);
				//etc ...
			}
			httpSession.setAttribute("idbooster", user.idbooster);
			//etc...
			resp.sendRedirect(getServletContext().getContextPath() + "/home");
		} else {
			req.setAttribute("loginfailed", true);
			RequestDispatcher rd = req.getRequestDispatcher("/login.jsp");
			rd.forward(req, resp);
		}
	}
```


SupTrip Campus utilise un formulaire de connexion pour pouvoir donner tous les accès à nos utilisateurs en base et ainsi accéder à des pages supplémentaires. Nous avons mis en place l'utilisation d'un filtre (authentication).
### HTTP Request

`GET http://example.com/api/suptrip`

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



