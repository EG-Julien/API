# API
Api permettant la communication entre le serveur et le(s) client(s).

#Installation

Download le repo, le placé dans un dossier d'un serveur web (Apache/Nginx avec php et la réécriture d'url).
Ensuite, créer une base de donnée (nommé *"iserver"* pour utiliser la conf. par défault) puis créer une table *users* avec les colonnes suivantes (*nom* / *type*) : 
  - id / INT(11) AUTOINCREMENT;
  - login / VARCHAR(255);
  - email / VARCHAR(255);
  - password / VARCHAR(255) - (SHA1);
  - last_login / DATETIME;
  - last_ip / VARCHAR(255);

#Fonctionnement

Les appelles à l'api se font en get avec la syntax suivante : <br>

  HOST / method / protocol / data
  
  ##Les methods :
   
   - GET -> Demande de récupération d'informations au près du serveur
   - POST -> Envoi des données au serveur
   
  ##Les protocols :
   
   - JSON -> Récupère les données au format json
   - AUTH -> Indique que les données suivante sont un token d'authentification (SHA1 du mdp)
   - START -> Démarre le serveur de Com
   - DATA -> Indique que les informations suivante permettent de changer l'état du serveur
   
  ##Les datas
  
   - ALL -> Récupère toutes les états
   
   ###Exemple de requète : 
   
   GET iserver.gg:3000 / get / json / all 
   
   ###Donne : <br>
  	```json
    {
    "api":"iServer API",
    "version":"1.2.4",
    "timestamp":1469720885,
    "status":"success",
    "response":    
       {
           "degrees":"27.62",
           "light":"2.56",
           "door":"41.38",
           "tension":"5.00",
           "a_status":"0",
           "b_status":"0",
           "blue":"0",
           "red":"1",
           "green":"0",
           "white":"0"
       }
    }
    ```
  
  Ce qui nous intéresse vraiment est le tableau "response" qui contient toutes les informations nécessaire comme la température (degrees), l'ouverture de la porte (door en cm) ou encore l'état de l'alarme (a_status) et du buzzer (b_status).
  
   ###Ou encore :
   GET iserver.gg:3000 / post / data / token / blue::on$ ***Le token est à remplacer par le SHA1 du mdp user*** et *blue::on$* demande au serveur de Com de d'allumer la led bleu. 
  
  Voir le repo App pour un exemple d'utilisation de l'API : https://github.com/EG-Julien/App
   ###La réponse sera :
   ```json
{
    "api":"iServer API",
    "version":"1.2.4",
    "timestamp":1469720885,
    "status":"success",
    "user" : {"login": "EG Julien"},
    "response":
      {
        "data_posted": "blue::on$"
      }
}
```
  La réponse n'est pas vraiment utile dans ce cas, il peut juste permettre de vérifier que la commande à été executé corectement. Nous pourions aussi imaginer un système d'historique coté client dans ce cas le *timestamp* pourrait devenir utile ainsi que le paramètre *login* afin de savoir qui à fait quoi et quand.
  
  ##Sécurité ?
  
  Pour le moment le but n'est pas de faire quelque chose de très sécurisé, je limite quand même la modification de l'état du serveur car je demande un token d'accès qui est vérifié. Mais coté App il est très facile de contourner l'authentification, mais beaucoup plus dur de changer l'etat du serveur.
  
