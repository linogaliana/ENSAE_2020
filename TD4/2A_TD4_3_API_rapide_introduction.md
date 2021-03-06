---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.6.0
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

# 2A.eco - API, API REST

Petite revue d'[API REST](https://fr.wikipedia.org/wiki/Representational_state_transfer).

<!-- #region -->
## Définition :  

API, à part que ce mot qui vaut 5 au scrabble, c'est quoi au juste ?


API signifie Application Programming Interface. Le mot le plus important est “interface”, et c’est le mot le plus simple, car nous utilisons tous des interfaces.

Bon, et une interface ?

Définition Larrouse : "Une interface est un dispositif qui permet des échanges et interactions entre différents acteurs"

Pour faire simple, une API est un moyen efficace de faire communiquer entre elles deux applications : concrètement, un fournisseur de service met à disposition des développeurs une interface codifiée, qui leur permet d'obtenir des informations à partir de requêtes.

Sans rentrer dans le détail technique, le dialogue ressemble à : "envoie moi ton adresse sous la forme X = rue, Y = Ville, Z = Pays" et moi, en retour, je t'enverrai le code à afficher sur ton site pour avoir la carte interactive.



<!-- #endregion -->

<!-- #region -->
## Les API qui existent


De plus en plus de sites mettent à disposition des développeurs et autres curieux des API. 

Pour en citer quelques-uns : 

- Twitter : https://dev.twitter.com/rest/public
- Facebook : https://developers.facebook.com/
- Instagram : https://www.instagram.com/developer/
- Spotify : https://developer.spotify.com/web-api/


Ou encore : 

- Pole Emploi : https://www.emploi-store-dev.fr/portail-developpeur-cms/home.html
- SNCF : https://data.sncf.com/api
- Banque Mondiale : https://datahelpdesk.worldbank.org/knowledgebase/topics/125589

pour beaucoup d'entre elles, il faut créer un compte utilisateur afin de pouvoir accéder aux données (c'est notamment le cas pour les réseaux sociaux), nous regarderons en cours seulement les API ouvertes sans restriction d'accès.  

<!-- #endregion -->

## Comment parler à une API ?

La plupart des API donnent des exemples par communiquer avec les données présentes sur le site.

Simplement, il faut trouver l'url qui renvoit les données que vous souhaitez avoir

Par exemple, avec l'API de la Banque mondiale, voici comme s'écrit une requête pour les données de la Banque Mondiale : 

http://api.worldbank.org/v2/countries?incomeLevel=LMC&format=json

Avec cette url, on demande la liste des pays dont le niveau de revenus est LMC, c'est à dire "Lower middle income". 

En cliquant sur le lien, le site renvoit des données en XML, qui ressemblent pas mal à ce qu'on a vu plus tôt avec le scraping : une structure avec des balises qui s'ouvrent et qui se ferment.

<!-- #raw -->
<wb:countries xmlns:wb="http://www.worldbank.org" page="1" pages="2" per_page="50" total="52">
<wb:country id="ARM">
    <wb:iso2Code>AM</wb:iso2Code>
    <wb:name>Armenia</wb:name>
    <wb:region id="ECS">Europe & Central Asia</wb:region>
    <wb:adminregion id="ECA">Europe & Central Asia (excluding high income)</wb:adminregion>
    <wb:incomeLevel id="LMC">Lower middle income</wb:incomeLevel>
    <wb:lendingType id="IBD">IBRD</wb:lendingType>
    <wb:capitalCity>Yerevan</wb:capitalCity>
    <wb:longitude>44.509</wb:longitude>
    <wb:latitude>40.1596</wb:latitude>
</wb:country>

<wb:country id="BGD">
    <wb:iso2Code>BD</wb:iso2Code>
    <wb:name>Bangladesh</wb:name>
    <wb:region id="SAS">South Asia</wb:region>
    <wb:adminregion id="SAS">South Asia</wb:adminregion>
    <wb:incomeLevel id="LMC">Lower middle income</wb:incomeLevel>
    <wb:lendingType id="IDX">IDA</wb:lendingType>
    <wb:capitalCity>Dhaka</wb:capitalCity>
    <wb:longitude>90.4113</wb:longitude>
    <wb:latitude>23.7055</wb:latitude>
</wb:country>

.....
<!-- #endraw -->

 Quand on regare de plus près, on voit que les informations suivantes apparaissent
 
Code du pays | Nom du pays | Région | Classification en termes de revenus | Les types de prêt pour ces pays | La capitale | Longitude | Latitude


----------------
<wb:country id="ARM">
    <wb:iso2Code>AM</wb:iso2Code>
    <wb:name>Armenia</wb:name>
    <wb:region id="ECS">Europe & Central Asia</wb:region>
    <wb:adminregion id="ECA">Europe & Central Asia (excluding high income)</wb:adminregion>
    <wb:incomeLevel id="LMC">Lower middle income</wb:incomeLevel>
    <wb:lendingType id="IBD">IBRD</wb:lendingType>
    <wb:capitalCity>Yerevan</wb:capitalCity>
    <wb:longitude>44.509</wb:longitude>
    <wb:latitude>40.1596</wb:latitude>
</wb:country>

<wb:country id="BGD">
    <wb:iso2Code>BD</wb:iso2Code>
    <wb:name>Bangladesh</wb:name>
    <wb:region id="SAS">South Asia</wb:region>
    <wb:adminregion id="SAS">South Asia</wb:adminregion>
    <wb:incomeLevel id="LMC">Lower middle income</wb:incomeLevel>
    <wb:lendingType id="IDX">IDA</wb:lendingType>
    <wb:capitalCity>Dhaka</wb:capitalCity>
    <wb:longitude>90.4113</wb:longitude>
    <wb:latitude>23.7055</wb:latitude>
</wb:country>


En utilisant cette url ci : http://api.worldbank.org/v2/countries?incomeLevel=LMC&format=json, on obtient directement un json, qui est finalement presque comme un dictionnaire en python.


Rien de plus simple donc pour demander quelque chose à une API, il suffit d'avoir la bonne url.


## Et Python : comment il s'adresse aux API ?

C'est là qu'on revient aux fondamentaux : on va avoir besoin du module requests de Python et suivant les API, un parser comme BeautifulSoup ou rien si on réussit à obtenir un json.

On va utiliser le module requests et sa méthode get : on lui donne l'url de l'API qui nous intéresse, on lui demande d'en faire un json et le tour est joué !


### Banque Mondiale : données économiques par pays

```python
import requests
data_json = requests.get("http://api.worldbank.org/v2/countries?incomeLevel=LMC&format=json").json()
data_json
```

```python
data_json[0]
# On voit qu'il y a nous manque des informations : 
# il y a un total de 52 éléments
data_json_page_2 = requests.get("http://api.worldbank.org/v2/countries?incomeLevel=LMC&format=json&page=2").json()
data_json_page_2
```

```python
# pour obtenir une observation 
# on voit dans l'objet que l'élément 0 correspond à des informations sur les pages , pour avoir les informations des pays,
# il faudra voir à partir de l'élément 1 de la liste qui est également une liste
data_json[1][0]
```

### DVF : les transactions immobilières en France


Le site DVF (demandes de valeurs foncières),permet de visualiation toutes les données relatives mutations à titre onéreux réalisées les 5 dernières années (pour faire simple les ventes de maison ou d'apparement depuis 2015). 
https://app.dvf.etalab.gouv.fr/

Ce site est très complet quand il s'agit de connaitre le prix moyen au mètre carré d'un quartier ou de comparer des régions entre elles. 

L'API DVF a été réalisée par Christian Quest, Son code est disponible sur https://github.com/cquest/dvf_as_api




Un exemple : on recherche toutes les transactions existantes dans DVF à Plogoff (code commune 29168, en Bretagne)

```python
data_immo = requests.get("http://api.cquest.org/dvf?code_commune=29168").json()
```

```python
data_immo['resultats'][20]
```

Les critères de recherche sont les suivants :
- code_commune = code INSEE de la commune (ex: 94068)
- section = section cadastrale (ex: 94068000CQ)
- numero_plan = identifiant de la parcelle, (ex: 94068000CQ0110)
- lat + lon + dist (optionnel): pour une recherche géographique, dist est par défaut un rayon de 500m
- code_postal

Les filtres de sélection complémentaires :
- nature_mutation (Vente, etc)
- type_local (Maison, Appartement, Local, Dépendance)


Par exemple si seules les maisons nous intéressent 

```python
data_immo = requests.get("http://api.cquest.org/dvf?code_commune=29168&type_local=Maison").json()
```

```python
data_immo['resultats'][0]
```
